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
<img src="https://i.imgur.com/Te4eo1N.png" alt="New Units"/>
<img src="https://i.imgur.com/UMgCSNu.png" alt="Employees,Admin"/>
</p>
<p>
  Create a new employee/administrator named “Jane Doe” with the username of “admin_janed”:
</p>
<p>
<img src="https://i.imgur.com/oFnpO4l.png" alt="Admin Jane Doe"/>
</p>
<p>
  Add admin_janed to the “Domain Admins” Security Group:
</p>
<p>
  <img src="https://i.imgur.com/Lq4aDfn.png" alt="Domain Admins"/>
</p>
<p>  
  Log out/close the Remote Desktop connection to DC-1 and log back in as “mylabdomain.com\admin_janed”. Use admin_janed as your admin account from now on:
</p>
<p>
<img src="https://i.imgur.com/6knP5YG.png" alt="Admin Jane New Login"/>
</p>
<br />
<br />
<h3 align="center">Join Client-1 to your domain (mylabdomain.com)</h3>
<br />
<p>
  From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address:
</p>
<p>
<img src="https://i.imgur.com/uNSwkJg.png" alt="DNS Server"/>
</p>
<p>
  From the Azure Portal, restart Client-1.
</p>
<p>
  Login to Client-1 (Remote Desktop) as the original local admin (clientuser) and join it to the domain (computer will restart):
</p>
<p>
<img src="https://i.imgur.com/zcH6Bpn.png" alt="Connect VM's"/>
</p>
<p>
  Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain.
</p>
<p>
  Create a new organzational unit named “CLIENTS” and drag Client-1 into there:
</p>
<p>
<img src="https://i.imgur.com/fcEWqHY.png" alt="Clients"/>
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
  You can now log into Client-1 as a normal, non-administrative user now
</p>
<p>
  <img src="https://i.imgur.com/hhXgnYP.png" alt="Domain Users"/>
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
  Create a new File and paste the contents of this script (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) into it:
</p>
<p>
  <img src="https://i.imgur.com/gQZWsF0.png" alt="Powershell ISE"/>
</p>
<p>
  Run the script and observe the accounts being created:
</p>
<p>
  <img src="https://i.imgur.com/34jgris.png" alt="New Users"/>
</p>
<p>
  When finished, open ADUC and observe the accounts in the appropriate OU and attempt to log into Client-1 with one of the accounts (take note of the password in the script):
</p>
<p>
  <img src="https://i.imgur.com/gdb5wPE.png" alt="Select New User"/>
  <img src="https://i.imgur.com/XSVZvgy.png" alt="User Ted Login"/>
  <img src="https://i.imgur.com/xtZ01O6.png" alt="Welcome Ted"/>
</p>
<br />
<br />
<p>
  Now the lab is complete. As always, make sure to close your Remote Desktop connection, delete the Resource Group(s) created at the beginning of this tutorial, and verify Resource Group deletion so you don't incur unnecessary extra charges.
</p>
<p>
