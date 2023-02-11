<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create Resources
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create an Admin and Normal User Account in AD
- Join Client-1 to your domain (mylabdomain.com)
- Setup Remote Desktop for non-administrative users on Client-1
- Create additional users and attempt to log into client-1 with one of the users


<h2>Deployment and Configuration Steps</h2>
<br />
<br />
<h3 align="center">Setup Resources in Azure</h3>
<br />
<p>
  Create the Domain Controller VM (Windows Server 2022) named “DC-1”:
</p>
<p>
<img src="https://i.imgur.com/E1up1MO.png" alt="Resource Group"/>
<img src="https://i.imgur.com/KKlI3bC.png" alt="DC-1"/>
</p>
<p>
Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in previous step:
</p>
<p>
<img src="https://i.imgur.com/BC61CPH.png" alt="Client-1"/>
</p>
<p>
  Set Domain Controller’s NIC Private IP address to be static:
</p>
<p>
<img src="https://i.imgur.com/Xm3VCPS.png" alt="Static"/>
</p>
<p>
  Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher):
</p>
<p>
<img src="https://i.imgur.com/pZjmtNQ.png" alt="Topology"/>
</p>
<br />
<br />
<h3 align="center">Ensure Connectivity between the client and Domain Controller</h3>
<br />
<p>
  Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping):
</p>
<p>
<img src="https://i.imgur.com/1OHk1sI.png" alt="Perpetual Ping"/>
</p>
<p>
  Login to the Domain Controller and enable ICMPv4 in on the local windows firewall:
</p>
<p>
<img src="https://i.imgur.com/wmW5FVN.png" alt="Enable Firewall"/>
</p>
<p>
  Check back at Client-1 to see the ping succeed:
</p>
<p>
<img src="https://i.imgur.com/QamfTY9.png" alt="Ping Success"/>
</p>
<br />
<br />
<h3 align="center">Install Active Directory</h3>
<br />
<p>
  Login to DC-1 and install Active Directory Domain Services:
</p>
<p>
<img src="https://i.imgur.com/wr5oUjU.png" alt="ADDS"/>
  </p>
<p>
  Promote as a Domain Controller:
</p>
<p>
<img src="https://i.imgur.com/qcnUHi4.png" alt="Promote DC"/>
</p>
<p>
  Setup a new forest as mylabdomain.com
</p>
<p>
<img src="https://i.imgur.com/9epecnC.png" alt="New Forest"/>
</p>
<p>
  Restart and then log back into DC-1 as user: mylabdomain.com\jm_labuser:
</p>
<p>
<img src="https://i.imgur.com/1GcU734.png" alt="New Login"/>
</p>
<br />
<br />
<h3 align="center">Create an Admin and Normal User Account in AD</h3>
<br />
<p>
  In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “EMPLOYEES” and another one called "ADMINS":
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  Create a new employee named “Jane Doe” with the username of “jane_admin”:
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  Add jane_admin to the “Domain Admins” Security Group:
</p>
<p>
  <img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>  
  Log out/close the Remote Desktop connection to DC-1 and log back in as “mylabdomain.com\jane_admin”. Use jane_admin as your admin account from now on:
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />
<br />
<h3 align="center">Join Client-1 to your domain (mylabdomain.com)</h3>
<br />
<p>
  From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address:
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  From the Azure Portal, restart Client-1.
</p>
<p>
  Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart):
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain.
</p>
<p>
  Create a new OU named “_CLIENTS” and drag Client-1 into there:
</p>
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />
<br />
<h3 align="center">Setup Remote Desktop for non-administrative users on Client-1</h3>
<br />
<p>
  Log into Client-1 as mydomain.com\jane_admin and open system properties.
</p>
<p>
  Click “Remote Desktop”.
</p>
<p>
  Allow “domain users” access to remote desktop.
</p>
<p>
  You can now log into Client-1 as a normal, non-administrative user now.
</p>
<p>
  Normally you’d want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab):
</p>
<p>
  <img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />
<br />
<h3 align="center">Create a bunch of additional users and attempt to log into client-1 with one of the users</h3>
<br />
<p>
  Login to DC-1 as jane_admin
</p>
<p>
  Open PowerShell_ise as an administrator.
</p> 
<p>  
  Create a new File and paste the contents of this script (https://github.com/agruezo/configure-active-directory/blob/script/createUsers.ps1) into it:
</p>
<p>
  <img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  Run the script and observe the accounts being created:
</p>
<p>
  <img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  When finished, open ADUC and observe the accounts in the appropriate OU and attempt to log into Client-1 with one of the accounts (take note of the password in the script):
</p>
<p>
  <img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  <img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  <img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />
<br />
<p>
  I hope this tutorial helped you learn a little bit about network security protocols and observe traffic between virtual machines. And although I ran this on a my MacBook Air, this can be easily done on a PC without having to download a remote desktop app since Windows provides that with it's software.
</p>
<p>
  And now that we're done, DON'T FORGET TO CLEAN UP YOUR AZURE ENVIRONMENT so that you don't incur unnecessary charges.
</p>
<p>
  Close your Remote Desktop connection, delete the Resource Group(s) created at the beginning of this tutorial, and verify Resource Group deletion.
</p>
