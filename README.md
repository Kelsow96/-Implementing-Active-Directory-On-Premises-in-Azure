<h1> Deploying Active Directory and Creating Users </h1> 

<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/5b730490-62e5-4e6a-99ed-903590512f80" width="600" />

<h2> What is Active Directory (AD)? </h2>
Active Directory (AD) is a Microsoft service for managing users, computers, and resources in a network. It centralizes security and administrative tasks, provides authentication and authorization, and organizes data into domains and organizational units. AD ensures data consistency and easy resource management across the network.
<br>
<br/>


In this tutorial, we'll set up our Domain Controller VM (DC-1) and a Client VM (C-1), ensure connectivity between C-1 and DC-1 install Active Directory on DC-1, create Admin and normal user accounts in AD, join C-1 to our domain, setup remote desktop for non-administrative users on C-1, create additional users on DC-1, and attempt to log into C-1 with one of those user accounts.

<h2> Environments and Technologies Used: </h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Windows Firewall
- Active Directory Domain Services (AD DS)
- Active Directory Users and Computers (ADUC)
- PowerShell

<h2> Operating Systems Used: </h2>

-  Windows 10
-  Windows Server 2022

<h2> List of Prerequisites: </h2>

-  Azure Account/Subscription

<h2> High-Level Steps: </h2>

1. Create our VMs
  - Create Domain Controller VM:
    - Deploy a Windows Server 2022 VM named "DC-1".
    - Set the NIC Private IP address to static.
  - Create Client VM:
    - Deploy a Windows 10 VM named "Client-1".
    - Use the same Resource Group and VNet as "DC-1".
    - Verify both VMs are in the same VNet.
  
2. Ensure Connectivity between Client and Domain Controller
  - Test Connectivity:
    - Log into "Client-1" via Remote Desktop.
    - Ping the private IP address of "DC-1".
    - On "DC-1", enable ICMPv4 in Windows Firewall.
    - Confirm successful ping from "Client-1".

3. Install Active Directory
  - Install AD DS:
    - On "DC-1", install Active Directory Domain Services.
    - Promote "DC-1" to a Domain Controller, setting up a new forest (e.g., mydomain.com).
    - Restart "DC-1" and log in as mydomain.com\labuser.

4. Create Admin and Normal User Accounts in AD
  - Create User Accounts:
    - In ADUC, create Organizational Units (OUs) "_EMPLOYEES" and "_ADMINS".
    - Create a new user "jane_admin" and add to the "Domain Admins" group.
    - Log in as mydomain.com\jane_admin.

5. Join Client-1 to the Domain
  - Configure DNS and Join Domain:
    - Set "Client-1" DNS to "DC-1"’s Private IP in Azure Portal.
    - Restart "Client-1".
    - Join "Client-1" to the domain and restart.
    - Verify "Client-1" in ADUC.

6. Setup Remote Desktop for Non-Admin Users on Client-1
  - Configure Remote Desktop:
    - Log into "Client-1" as mydomain.com\jane_admin.
    - Allow "domain users" access to Remote Desktop.

7. Create Additional Users and Test Logins
  - Automate User Creation:
    - On "DC-1", use PowerShell to run a script that creates multiple users.
    - Test logging into "Client-1" with one of the new accounts.

<h2> Steps: </h2> 

1. Create our VMs
  - Create Domain Controller VM:
    - Deploy a Windows Server 2022 VM named "DC-1". We'll use "labuser" as our Username and "Password1234" as our password. We can move straight to "Review + Create". It should pass validation, and we can create our DC-1 VM.

<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/30f052a1-dac2-481b-95ab-940e337b87ec" width="600" /> 
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/bcc45935-53d9-4f00-a8c9-9f98a413e981" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/248024d9-5b1f-4760-8087-f7b2ab9725cd" width="600" />
<br>
<br/>

  - Set the NIC Private IP address to static. Within DC-1 we'll navigate to "Network Settings", "Network interface: dc-1755_z1", "IP configurations", "ipconfig", finally select "Static" and "Save".

<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/d555ae44-1f52-41bd-99b5-c5ab2444162c" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/44679a7f-792f-4997-ae0d-256a014a65a0" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/d7a36717-6276-4a59-999f-f781da369ff2" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/0b65d1bf-9800-48fb-a6a7-647a3b7f2d77" width="600" />
<br>
<br/>
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/6398d1bc-21e7-4015-bb26-ab87b13d74da" width="400" />
<br>
<br/>

  - Create Client VM:
    - Deploy a Windows 10 VM named "Client-1". We'll do the same exact steps we used to create DC-1 for Client-1. Expect we'll use Windows 10 for the operating system and DC-1's Vnet.
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/1e27fc8f-6863-45c1-992e-e1afe8e27cdb" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/c7ce825b-8050-4ada-90bd-4aef5c7f10ce" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/0b735dca-0f35-4902-9460-60f88011d3c8" width="600" />
<br>
<br/>
   
- Verify both VMs are in the same VNet.
![Capture](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/f95d5819-1fd2-4fcd-a4c8-86acb10482dc)
![Capture](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/952949e2-59d1-40d6-b753-3345dcfb2bb9)


