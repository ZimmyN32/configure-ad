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

- Prepare Active Directory Infrastrucre in Azure
  - Create a Resource Group
  - Create a Virtual Network and Subnet
  - Create the Domain Controller VM (Windows Server 2022) named “DC-1”
    - Username: labuser
    - Password: Cyberlab123!
  - After VM is created, set Domain Controller’s NIC Private IP address to be static
  - Log into the VM and disable the Windows Firewall (for testing connectivity)
  - Create the Client VM (Windows 10) named “Client-1”
    - Username: labuser
    - Password: Cyberlab123!
  - Attach it to the same region and Virtual Network as DC-1
  - After VM is created, set Client-1’s DNS settings to DC-1’s Private IP address
  - From the Azure Portal, restart Client-1
  - Login to Client-1
  - Attempt to ping DC-1’s private IP address
    - Ensure the ping succeeded
  - From Client-1, open PowerShell and run ipconfig /all
  - The output for the DNS settings should show DC-1’s private IP Address

- Deploy Active Directory
  - Install Active Directory
    - Login to DC-1 and install Active Directory Domain Services
    - Promote as a DC: Setup a new forest as mydomain.com (can be anything, just remember what it is)
    - Restart and then log back into DC-1 as user: mydomain.com\labuser
  - Create a Domain Admin user within the domain
    - In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”
    - Create a new OU named “_ADMINS”
    - Create a new employee named “Jane Doe” (same password) with the username of “jane_admin” / Cyberlab123!
    - Add jane_admin to the “Domain Admins” Security Group
    - Log out / close the connection to DC-1 and log back in as “mydomain.com\jane_admin”
    - User jane_admin as your admin account from now on
  - Join Client-1 to your domain (mydomain.com)
    - Login to Client-1 as the original local admin (labuser) and join it to the domain (computer will restart)
    - Login to the Domain Controller and verify Client-1 shows up in ADUC
    - Create a new OU named “_CLIENTS” and drag Client-1 into there
  - Setup Remote Desktop for non-administrative users on Client-1
    - Log into Client-1 as mydomain.com\jane_admin
    - Open system properties
    - Click “Remote Desktop”
    - Allow “domain users” access to remote desktop
    - You can now log into Client-1 as a normal, non-administrative user now
    - Normally you’d want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab)



- Create Users With PowerShell
  - Login to DC-1 as jane_admin
  - Open PowerShell_ise as an administrator
  - Create a new File and paste the contents of the script into it
  - Run the script and observe the accounts being created
  - When finished, open ADUC and observe the accounts in the appropriate OU　(_EMPLOYEES)
  - Attempt to log into Client-1 with one of the accounts (take note of the password in the script)

- Group Policy and Managing Accounts
  - Enabling and Disabling Accounts
    - Disable the same account in Active Directory
    - Attempt to login with it, observe the error message
    - Re-enable the account and attempt to login with it.
  - Dealing with Account Lockouts
    - Get logged into dc-1
    - Pick a random user account you created previously
    - Attempt to log in with it 10 times with a bad password
    - Configure Group Policy to Lockout the account after 5 attempts:
      - [How To Configure Account Lockout Threshold in Group Policy](https://docs.google.com/document/d/1msUMWaPDMR1hPYxzGOlgN4KpUjnyyYEv3vvOQXkSpLQ/edit)
    - Attempt to log in with it 6 times with a bad password
    - Observe that the account has been locked out within Active Directory
    - Unlock the account
    - Reset the password
    - Attempt to login with it


<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/cNhoGBK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We will create one virtual machine running Windows Server that will act as the Domanin Controller and another virtual machine running Windows 10 that will act as the client. The client will join the domain and we will be able to log into the client as users that exist on the domain controller. In order for the client to join the domain and for this experiment to work, we will set the DNS IP address on our client's network interface card to the same IP address as the Domain controller.
</p>
<br />

