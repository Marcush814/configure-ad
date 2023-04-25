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
- Ensure Connectivity between client and domain conrtoller
- Install Active Directory
- Create admin and normal user in AD
- Join client-1 to your domain
- Setup remote desktop for non-admin users on client-1
- Create multiple users and login as on of them



<h2>Deployment and Configuration Steps</h2>


Setup resources in Azure

Create the domain Controller VM (Windows Server 2022) named DC-1:


<p>
<img src="https://i.imgur.com/P7gWx76.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/BXtwEmp.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Create Client-1 VM(Windows 10) use same resource group:
</p>
<br />

<p>
<img src="https://i.imgur.com/Pwm98YY.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
</p>
<br />

Set Domain Controllers NIC private IP Address to static:

<p>
<img src="https://i.imgur.com/sumkzKe.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Ensure both VM's are in the same Vnet ( you can see this ny looking at topology with Network Watcher): 

</p>
<br />

<p>
<img src="https://i.imgur.com/UZ5eKMP.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Login to Client-1 with Remote Desktop and ping DC-1's private ip address with ping-t (perpetual ping):


</p>
<br />

<p>
<img src="https://i.imgur.com/P9fRlly.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Login to the domain controller and enable ICMPv4 on the local windows network:

</p>
<br />

<p>
<img src="https://i.imgur.com/QojEHEs.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Check back on Client-1 and see the ping succeed: 


</p>
<br />

<p>
<img src="https://i.imgur.com/iHger8d.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Install Active Directory

Login to DC-1 and install Active Director Domain Services:

</p>
<br />

<p>
<img src="https://i.imgur.com/7xq6UsB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Promote DC-1 as a Domain Controller:

</p>
<br />

<p>
<img src="https://i.imgur.com/Zkne7yI.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Setup new forest as whatever you'd like(just remember what it is): 

</p>
<br />

<p>
<img src="https://i.imgur.com/igChQF5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Restart and log back into DC-1 as user: (your domain name.com\labuser)
</p>
<br />

<p>
<img src="https://i.imgur.com/SLP06wJ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

From Azure portal, set client-1's DNS settings to the DC's Private IP address: 


</p>
<br />

<p>
<img src="https://i.imgur.com/JyuP9XN.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

From the Azur portal, restart Client-1

Login to Client-1 (remote desktop) as the orignial local admin (labuser) and join it to the domain (computer will restart):

</p>
<br />

<p>
<img src="https://i.imgur.com/WJAWTdO.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Login to DC-1 and verify Client-1 shows up in Active Directory Users and Computers (ADCU) inside the "computers" container on the root of the domain.


Create a new OU named" _CLIENTS and drag Client-1 into it:


</p>
<br />

<p>
<img src="https://i.imgur.com/68VNFUI.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>


Setup Remote Desktop for non-administravtive users on Client-1

Log into Client-1 as mydomain.com\jane_admin and open system properties

Click “Remote Desktop”

Allow “domain users” access to remote desktop

You can now log into Client-1 as a normal, non-administrative user now

Normally you’d want to do this with Group Policy that allows you to change MANY systems at once

</p>
<br />

<p>
<img src="https://i.imgur.com/UCkC1GX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>


Login to DC-1 as jane_admin

Open PowerShell_ise as an administrator

Create a new File and paste the contents of the script into it (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)
Run the script and observe the accounts being created

When finished, open ADUC and observe the accounts in the appropriate OU

attempt to log into Client-1 with one of the accounts (take note of the password in the script)

</p>
<br />

<p>
<img src="https://i.imgur.com/Nkx360E.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Run script and observe accounts being created:

When executing the PowerShell script: 1) Make sure you are doing it on Windows Server VM, not the Client VM. It won't work if you do. 2) Make sure your "_EMPLOYEES" OU within Active Directory matches the OU in your script. If it doesn't match, you will get errors.


</p>
<img src="https://i.imgur.com/cPnHYyr.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Observe accounts in ADCU and attempt tp log into one using CLient-1:
</p>
<br />

<p>
<img src="https://i.imgur.com/dB0kqr5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

<p>
<img src="https://i.imgur.com/lEj9uw8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/MbvYiBq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>


This concludes the tutorial hope it was helpful to you.
