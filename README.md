<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
First we will create two virtual machines in Azure. Name the first “DC-1” and the second “Client-1”. Make sure the “Client-1” VM is placed into the same resource group as “DC-1”

For VM “DC-1” make sure the OS image is “Window Server”. For this lab we will use “Window Server 2022”

Make sure both VMs are on the same Virtual Network. (They likely already will be by default). Make sure you have created them both in the same region as well.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Set DC-1’s NIC Private address to be static. Click on DC-1, then click on Networking. You will
See something titled “network interface”. Click it, then on the sidebar click IP Configurations. You can see that the ip address is dynamic by default, click on it and change it to static before saving.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
With both VMs created, let’s test the connectivity between the two of them by attempting to ping DC-1 from Client-1.

You can find the private IP address on the page for DC-1. This is the IP address we will be attempting to ping. Copy it, and once you’ve logged into Client-1 open a command prompt. Type “ping -t” followed by the private IP address you’ve just copied. This will perpetually ping DC-1.
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
You will likely notice that the pings are unsuccessful. This is because the firewall needs to be opened to allow ICMPv4 traffic through. Login to DC-1. Once inside, press the windows key and then type “wf.msc”. Alternatively, you can type Windows Defender Firewall with Advanced Security.

Click on inbound rules, then sort by protocol. After finding ICMPv4, enable both ICMPv4 echo requests.

Once done, return to Client-1 and notice that the ping requests are now successful.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next up is actually installing Active Directory to DC-1. If it is not already open, open up your server manager. Click on Add Roles and Features. Click next until reaching the server roles. On this screen select Active Directory Domain Services. Continue pressing next and wait for it to install.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once that’s done you will notice the flag in the top right has a sign next to it. Click it, and then click “Promote this server to a domain controller”.

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Add a new forest. Now name your domain. This domain name can be anything you’d like, but for the sake of this tutorial and simplicity I will be using mydomain.com. 

Next you will be asked to input a restore mode password, we will not be using that for this lab, so you can put whatever you’d like into the box and proceed. Continue pressing next until reaching installation, and press install and wait. Upon completion it will automatically sign you out of the VM.  All you will need to do is log back in after refreshing the VM and Active Directory will be installed.

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In order to log back into the VM, you will need to use a different username. Instead, this time you will use the domain name you set followed by the original username. EX: mydomain.com\labuser. The password will remain unchanged.

Once you’ve reconnected to DC, we will now create a few organizational units as well as an Administrative User.

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
To open Active Directory, click on Tools in the top right corner and select “Active Directory Users and Computers” in the drop down menu. Alternatively you can search for “Active Directory Users and Computers” through the Windows Key search. 

 To create a new Organizational Unit, right click on mydomain.com and select New>Organizational Unit. The first one we create will be named “_EMPLOYEES” and the second will be called “_ADMINS”.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now we will create a user with administrative capabilities for our domain. Within the _ADMINS OU, create a new user. This User can be named whatever you’d like, but for simplicity’s sake I’m going to use “John Doe”. For the User login name I will use john_admin. Next set the password, and set the password to never expire rather than forcing a change on the first login.

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
With John Doe created, we will now have to assign them to the Domain Admins group. Right click and open Properties. Click on Member Of and add Domain Admins. 

With John Doe now created and added as an admin, let’s attempt to log in to the DC as John instead. When logging back into the VM change the user name to mydomain.com\john_admin and log in with the password you set.

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
With all the set up complete for DC, let’s now join Client-1 to the Domain. To do this, we will need to set Client-1’s DNS settings to DC-1’s private IP address.

First, copy DC-1’s private IP which will be found under Networking>NIC Private IP. Once copied, return to Client-1. Go to Networking>Network Interface>DNS servers. Change the DNS settings from Inherit to Custom and change the DNS server to the Private IP we copied. Save, and once done reset the Client-1 VM.

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once the VM has restarted, log back in. Once in, go to Settings>About>Rename this PC. Click change, and then change the Domain to  “mydomain.com”. When prompted to enter the name and password of an account with permission, fill in the details for john_admin.

After it's accepted, it will prompt you to restart the computer. Click yes and once it has restarted it will allow you to login to Client-1 with john_admin.

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Our last step is setting up the ability for all users to be able to login to Client-1. After you have logged back into Client-1 with john_admin, go to Settings>About>Remote Desktop>Select users that can remotely access this PC.

Add the group Domain Users.

With that group added, users who are not admins can now log into Client-1.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Finally we will create a regular user to log into Client-1 with.

Open up DC-1 as john_admin.

Open up Active Directory Users and Computers once again, and this time open the _EMPLOYEES organizational unit.

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create a regular user. I will name mine Jane Doe for simplicity’s sake. 

Now that our user has been completed - attempt logging into Client-1 using this account.

</p>
<br />
