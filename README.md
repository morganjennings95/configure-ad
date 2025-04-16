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

- Setup 2 virtual machines, 1 running windows server & 1 running windows 10 on the same VPN
- Setup active directory on the domain controller and create random users
- Change Group Policy to create rules for users
- Utilize active directory to solve problems, change permissions & organize users

<h2>Deployment and Configuration Steps</h2>

![image](https://github.com/user-attachments/assets/b999d6e2-63e3-4bf0-9555-83acdbf60979)

<p>
First we will setup a resource group in Azure, review and create once information is filled in
</p>
<br />

![image](https://github.com/user-attachments/assets/36b9d2b0-1e47-4662-9fa2-66a996d8dab8)

<p>
Setup your VPN as shown in Azure, review and create
</p>
<br />

![image](https://github.com/user-attachments/assets/d683f88e-82ed-48cf-a8ff-dc624ceb8178) ![image](https://github.com/user-attachments/assets/877b074e-7ebb-4303-a1a1-9843bbe13344) ![image](https://github.com/user-attachments/assets/d8dd01db-57d5-4614-8905-3e5da11f8bc7)

<p>
Now we will setup our windows server VM. Be sure to copy all information above, substituting as needed if some of these options are not available. Setup a username and password that you desire, I selected "labuser" if you wish to copy, be sure to select the licensing boxes at the bottom of the page.
</p>
<br />

![image](https://github.com/user-attachments/assets/7cb27398-e859-4629-bbe8-fa9740e30791)

<p>
Select "Networking" next and be sure to match the above information and "review and create" to launch your server VM
</p>
<br />

![image](https://github.com/user-attachments/assets/58402cb5-6372-4637-83cd-4e6082d848b1)

<p>
Now we will create another VM named client 1, be sure to copy the previous step in setting up client-1, we will use a windows 10 Pro image instead of server, remember to select the same VNET in networking before creating the VM
</p>
<br />

![image](https://github.com/user-attachments/assets/678001a6-bbc9-4d7d-9f24-c4ffc7e4963a) ![image](https://github.com/user-attachments/assets/5053dfe1-9439-4002-b51f-cf9670d696b8)



<p>
Now we want to set our server IP Address to static so select Virtual Machine -> dc-1 -> Networking -> Network settings -> Network infterface/IP configuration -> ipconfig1 -> static -> save
</p>
<br />

![image](https://github.com/user-attachments/assets/74033b97-f04f-4cfd-9b41-b6be8645d404)

<p>
Now using remote desktop, copy the Public IP of dc-1 and login using the creditials you setup when launching the VM
</p>
<br />

![image](https://github.com/user-attachments/assets/78000864-a497-4173-83f6-219f4adfb9d7) ![image](https://github.com/user-attachments/assets/2423affa-b56b-4144-a2af-8a81531559f2)


<p>
Once the server launches, right click the start menu type "wf.msc" to pull up windows defender, select the firewall Properties, and turn off firewall in each header category where it says "Firewall state" -> Apply -> OK
</p>
<br />

![image](https://github.com/user-attachments/assets/e41017a1-c484-45f9-b6b9-cb072857818b) ![image](https://github.com/user-attachments/assets/ca290edf-111d-4e6b-b912-c40187baac3d) ![image](https://github.com/user-attachments/assets/6ef3df07-d1db-485b-9668-9e7e20914883)



<p>
Now we will go back to Azure select dc-1 and in network settings locate the Private IP Address, copy it and in client-1, under network interface, select DNS servers -> Custom and paste in the private IP from dc-1 -> save. Now return to your VM page in Azure and restart ALL VMs to ensure all your changes went into effect
</p>
<br />

![image](https://github.com/user-attachments/assets/0ed5db65-4d1c-4c3f-82b2-07515adec981)

<p>
Access dc-1 and we will install active directory on this VM, select start menu -> server manager -> Add roles and features, select next until you get to the page above and check "Active Directory Domain Services" and scroll through to the end and select install
</p>
<br />

![image](https://github.com/user-attachments/assets/45953235-c7e9-4842-a8cc-66a0aa4e9b9c) ![image](https://github.com/user-attachments/assets/69e53665-ec10-40ef-a8b2-19549af5a8a4) ![image](https://github.com/user-attachments/assets/7919dd27-3940-4487-b4df-e6999bc049ce)

<p>
Next we will promote the server to a domain controller, call it "mydomain.com", create a password, uncheck "create DNS delegation", select next untill install highlights, select install. Once installed, restart your system if it does not automatically prompt you to do so.
</p>
<br />

![image](https://github.com/user-attachments/assets/45e5196d-da08-408a-8ce7-8abc96c8372e)

<p>
You will not be able to log back in the traditional way if you did everything correctly, instead you will need to use mydomain.com\labuser and your password to login
</p>
<br />

![image](https://github.com/user-attachments/assets/230e62c4-fcb9-4499-ad2b-ca001565393a)

<p>
Select start -> windows administrative tools -> active directory users and computers
</p>
<br />

![image](https://github.com/user-attachments/assets/7c2502e3-cad5-4dbe-bdd2-d9490834614a)

<p>
Now we will create a couple new organizational units, select mydomain.com -> new -> organizational unit and create two new units "_EMPLOYEES" & "_ADMINS"
</p>
<br />

![image](https://github.com/user-attachments/assets/4d3f98ef-3554-4722-ab20-d9fac61f0aec) ![image](https://github.com/user-attachments/assets/5a09b7b2-2c30-40e9-91fc-230b3d22ffc3)

<p>
Right click Admins -> new -> User and we will create our first admin "jane_admin" set a password and finish, for the purposes of this lab uncheck "change password" and select "password never expires" to make things easier because this is all practice and we do not want to continue resetting a password however often the system prompts us to change
</p>
<br />

![image](https://github.com/user-attachments/assets/327fea67-2359-4865-b366-6669145b714b)

<p>
Now we want to make jane a domain admin right click Jane Doe -> properties -> member of -> add, now type "domain admins" check names and select OK -> apply -> OK. Now check your work and logout and back in to dc-1 with username "mydomain.com\jane_admin" and we will use the user from here going forward
</p>
<br />

![image](https://github.com/user-attachments/assets/6bec7822-1eea-4aa9-ac03-47a5e0839396)

<p>
Let us login into client one as the labuser and join the domain, right click start -> properties -> rename this PC (advanced) -> change -> Domain & type "mydomain.com -> OK -> use "mydomain.com\jane_admin" with your password -> OK
</p>
<br />

![image](https://github.com/user-attachments/assets/fc0aa6c8-7386-4750-ba34-2ba0ccb08807)

<p>
Create another organizational unit in ADUC called "_CLIENTS" and in the Computers folder, move Client-1 into the _CLIENTS folder
</p>
<br />

![image](https://github.com/user-attachments/assets/cf730c0f-4044-491c-8c8e-8dd37c0c55f2)

<p>
Allow domain users to log in to client-1, right click start -> system -> remote desktop -> select users that can remotely access the PC -> add -> type domain users -> check names -> OK
</p>
<br />

![image](https://github.com/user-attachments/assets/ac65d867-8e87-4ecf-b2c9-bc7132c7481e)

<p>
Now logging back into dc-1 we will create users, copy this link -> https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1 and open it in the browser of your choice and you will find "copy raw file", copy that and open up Powershell ISE as an admin, select a new script at the top left of the window under "file" and paste in this script, I changed the user count to 500 from 10000 because we do not need that many new user accounts (line 3) for reference
</p>
<br />

![image](https://github.com/user-attachments/assets/3e36da68-fe62-4d0e-9f94-c6994567b798)

<p>
Select "Run" and it will create 500 employees
</p>
<br />

![image](https://github.com/user-attachments/assets/feb83037-9fa7-4e4a-8814-553b7491203b) ![image](https://github.com/user-attachments/assets/a9d830a0-0074-4b16-a460-62e9d2f571ae)


<p>
Now if we go over to ADUC, we can select "_EMPOYEES" and notice that all the new users have been added to the system. Now log into client-1 as one of these users as mydomain.com\" " Open up your file folder and locate "users" and notice the user you selected "fes.nubip" in my case appears along with jane_admin which we logged on with earlier, once finished, log out of your new user in client-1
</p>
<br />

![image](https://github.com/user-attachments/assets/2040c938-913b-44b5-bfe8-3a1c81384016)

<p>
In dc-1 we are going to configure the account lockout policy. In the search bar "group policy management" expand the top drop down lists, click default domain policy -> right click default domain policy -> edit -> policies -> windows settings -> security settings -> account policies -> account lockout policy
</p>
<br />

![image](https://github.com/user-attachments/assets/8946986d-020c-4dee-a8eb-b26a936432fb)

<p>
Set the policy as shown for each parameter, change them as you wish later, this is the easiest set to demonstrate with what is possible
</p>
<br />

![image](https://github.com/user-attachments/assets/37cf4cab-459d-4d1a-bba8-c20e02613176)

<p>
Log back into client-1 as jane_admin and force the group policy update "gpupdate /force" to make the update take effect immediately
</p>
<br />

![image](https://github.com/user-attachments/assets/e9e24b89-bab9-4cfe-b9bc-f33ed87a7861)

<p>
Attempt to log on an excessive amount of times until you get this security login error, we have exceeded our login attempts
</p>
<br />

![image](https://github.com/user-attachments/assets/7cb98f7d-3a7b-4d79-a99f-d01fe95ee266)

<p>
Back to dc-1, under ADUC, find your user under employees select account -> unlock account -> apply -> OK. Now if you attempt to log back into client-1 as your selected user, you will find that you are no longer logged out, if you wish to reset your password, you may do so by right clicking on the username and select reset password, but active directory is much more powerful and I suggest playing around with the settings and finding out what else you can learn
</p>
<br />

![image](https://github.com/user-attachments/assets/29aaf817-8206-4e25-80b7-0e74d719dd16)

<p>
Additionally right clicking on the user, we are able to disable the account if need be, and if you look closely, it changes the status next to their user icon, in which it can also be re-enabled. This concludes the session and again this is a very powerful tool, I encourage looking more into group policy and seeing where and what you can change in order to get a better grasp on best practices and familiarizing yourself with what is possible
</p>
<br />
