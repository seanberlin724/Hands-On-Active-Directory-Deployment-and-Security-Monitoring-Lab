# Hands-On-Active-Directory-Deployment-and-Security-Monitoring-Lab

## Objective
This project involves creating a home lab to learn Active Directory and IT security, encompassing installations, configurations, and security monitoring.

### Skills Learned
- Installation and configuration of Windows Server and Active Directory
- Setting up Splunk for security monitoring
- Conducting and analyzing brute-force attacks
- Understanding of network diagramming and system interconnectivity
- Troubleshooting common Active Directory errors

### Tools Used
- VirtualBox
- Windows Server 2022
- Windows 10
- Kali Linux
- Splunk
- Sysmon
- Atomic Red Team

## Steps
### 1. Building the Network Diagram
I created a detailed network diagram using draw.io, visualizing the connections between various components such as servers, computers, switches, and routers. This diagram serves as a reference for understanding data flow and enhances my ability to better understand technical setups. Important details, including IP addresses and domain names, were documented for clarity in the lab setup.
<img src="Images/Diagram.png">
*Ref 1: Network Diagram via Draw.io*


### 2. Installing Virtual Machines
In this step, I installed essential virtual machines within VirtualBox, including Windows 10, Kali Linux, Windows Server 2022, and Ubuntu Server (Splunk Server). I followed specific instructions for each operating system, ensuring that all necessary dependencies were met. Tip: It is recommended to use snapshots of the VMs to allow for experimentation without the risk of permanent mistakes.
<img src="Images/VM Virtual Box Manager.png">
*Ref 2: VM VirtualBox Manager of Active Machines Needed*

Additionally, I modified the network settings on VirtualBox to NAT Network. This allows for the VMs to be on the same network and still have internet access. I created and named the NAT Network as "AD-Project" and set the IPv4 Prefix to "192.168.10.0/24". Be sure to also adjust the individual network settings for each individual VM to NAT Network as well.

### 3. Configuring Splunk
I installed and configured Sysmon for logging system activity and set up Splunk as the Security Information and Event Management (SIEM) tool. This involved creating an 'endpoint' index in Splunk, managing data reception, and verifying that incoming events were logged correctly. This configuration is crucial for effective security monitoring and telemetry analysis.

To begin the configuration for the Splunk Server I started by setting a static IP. This was done by modifying the yaml config file located in the "/etc/netplan" directory. In this case, it was called "50-cloud-init.yaml". The IP was set to "192.168.10.10/24", DNS IP was set to "8.8.8.8" (Google's DNS), and the gateway IP (default route) to "192.168.10.1/24" 
<img src="Images/Splunk Yaml.png">
*Ref 3: 50-cloud-init.yaml file*

The next step was to install the guest add-ons for Virtual Box to enable the installation of Splunk from my host machine. I also "linked" the shared folder of the VM to the folder on my host machine where I put the Splunk Installer in.
<img src="Images/Install Guest Add-ons and Link Shared Folder.png">

*Ref 4: Install Guest Add-ons and Link Shared Folder*


Next, I installed the "virtualbox-guest-utils" package enabling myself to be able to add my user (mydfir) to the "vboxsf" group.
<img src="Images/Add user to vboxsf group.png">

*Ref 5: Add user to vboxsf group*

Next, I mounted the shared folder onto a self-created directory called "share". This allows access to the Splunk Installer in the "share" directory. The Splunk Installer package was then installed.
<img src="Images/Splunk Installed.png">

*Ref 6: Splunk Installer Package Installed*

I then changed to the directory where splunk is located. It is shown that all the user and group permissions belong to "splunk". This is beneficial as it limits the permissions to that user. I then changed into the user "splunk" to access the binaries and run the splunk installer.

<img src="Images/Run Splunk Installer.png">

*Ref 7: Run Splunk Installer*



Lastly, I performed a command to make sure Splunk starts up every time the VM reboots. In other words, this makes it so that anytime the VM reboots, Splunk will run with the user "splunk".

<img src="Images/Enable Splunk at Start.png">

*Ref 8: Enable Splunk at Start*

### 4. Configuring Sysmon for Windows 10 VM and Windows Server
The following procedure is performed on the Windows 10 VM (target-pc) but the steps are the same for the Windows Server as well.
First, I adjusted the network settings and checked them using the command prompt.

<img src="Images/Adjust IP for Target Machine.png">

*Ref 9: Adjust IP for Target Machine*


The next step is to install Splunk Universal Forwarder and Sysmon on both the target machine and server. The download is available on splunk.com. The only significant change made during installation was setting the Receiving Indexer to the IPv4 of "192.168.10.10" and the port to "9997".

<img src="Images/Splunk Universal Forwarder.png">

*Ref 10: Splunk Universal Forwarder*


As far as the Sysmon installation, the download is available via "https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon". The configuration file, "sysmonconfig.xml", needed can be found at "https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml". I then opened Windows PowerShell with Administrator privileges and changed it to the Sysmon directory. The next command installs Sysmon with the proper configuration file.
<img src="Images/Sysmon Installation.png">

*Ref 11: Sysmon Installation*


The next part is the most important as the Splunk Forwarder needs to be instructed on what we want to send over to our Splunk server. This is achieved by configuring a file called "inputs.conf". The file content can be found at "https://github.com/MyDFIR/Active-Directory-Project". I then copied it into a notepad file with administrator privileges and saved it under the local file path as seen in the image.  This file instructs the Splunk Forwarder to push event-related applications, security, system, and Sysmon, over to the Splunk Server. Note that the "index" is pointed to the "endpoint" meaning whatever events that fall under these categories will be sent over to Splunk and placed under the index called "endpoint".

<img src="Images/Inputs conf.png">

*Ref 12: Inputs.conf*


The next step is to restart Splunk's Universal Forwarder Service and set the "Log on as" option to "Local System Account". This ensures that logs are able to be collected properly in accordance with account permissions.
<img src="Images/Adjust Services.png">

*Ref 13: Adjust Services*


Now, the Splunk server configuration can be finalized. I logged into the Splunk web portal and created a new index called "endpoint". This index will collect all of the events being sent over as specified in the "inputs.conf" file.
<img src="Images/Create index endpoint.png">

*Ref 14: Create index endpoint*



Next, to enable the Splunk server to receive the data, a new receiving port must be added. In this case, it is port "9997." Data should now be seen coming in from the Windows 10 machine if everything is set up correctly. 
<img src="Images/Add Port 9997.png">

*Ref 15: Add Port 9997*

The specified events from the "inputs.conf" can be seen when viewing the "endpoint" index.
<img src="Images/Index Endpoint.png">

*Ref 16: Index Endpoint*

### 5. Install and Configure Active Directory on Windows Server
In this phase, I installed Active Directory on Windows Server, promoted it to a domain controller, and created organizational units and users. I set a static IP address, verified connectivity, and successfully joined target machines to the new domain. This hands-on experience significantly enhanced my understanding of domain management and security considerations.

 To start, I began by setting a static IP address and verifying connectivity.
<img src="Images/Adjust IP for Windows Server.png">

*Ref 17: Adjust IP for Windows Server*


I then installed Active Directory (AD DS) as a Role-based installation.
<img src="Images/Install AD DS.png">

*Ref 18: Install AD DS*

I then configured Active Directory by creating a new domain with the root domain name being "mydfir.local"
<img src="Images/AD Configuration.png">

*Ref 19: AD Configuration*


I created two dummy departments aka Organizational Units called "IT" and "HR". I created and added the users "Jenny Smith" and "Terry Smith" to their respective departments.
<img src="Images/Add Users and Groups to AD.png">

*Ref 20: Add Users and Groups to AD*


I then joined the Windows target machine to the newly created domain "mydfir.local". Ensure that the DNS Server is pointing to the domain controller "192.168.10.7" to allow for the mydfir.local server to be resolved properly.
<img src="Images/Join mydfir Domain.png">

*Ref 20: Join mydfir Domain*


### 6. Kali Linux Brute Force Attack
I conducted a brute force attack using Kali Linux, targeting the Remote Desktop Protocol (RDP) on a Windows machine. This step included setting up Kali, installing the crowbar tool, and utilizing a wordlist for password attempts. I analyzed the generated telemetry with Splunk and Atomic Red Team (ATR) to gain insights into the attack process, improving my understanding of attacker behaviors and detection capabilities.

 To start, I set a static IP address and verified connectivity to both google.com and the Splunk server at "192.168.10.10."
<img src="Images/Adjust IP for Kali.png">

*Ref 21: Adjust IP for Kali.png*


I then created a directory called "ad-project" where I will put all the files created and used. Next, I installed the tool Crowbar to be utilized to perform brute force attacks. The description of Crowbar is as follows: Crowbar is a brute force tool which supports OpenVPN, Remote Desktop Protocol, SSH Private Keys and VNC Keys." Please note, this is for educational purposes only.
<img src="Images/Install Crowbar.png">

*Ref 22: Install Crowbar*

Following, I copied the "rockyou.txt" file into the "ad-project" directory. I then created a file called "passwords.txt" with only the first 20 lines of text from the file as it is 134 million lines long.
<img src="Images/Create passwords file.png">

*Ref 23: Create passwords file.png*


Following I edited the "passwords.txt" file and added the password of the designated target machine. In this case, I targeted the account "tsmith" logged in as a user on the Windows 10 VM with the password of "Basketball1!!!1".
<img src="Images/Edit passwords file.png">

*Ref 24: Edit passwords file.png*




Before launching the attack, I enabled RDP on the target machine while signed in as the user "tsmith". 
<img src="Images/Enable RDP.png">

*Ref 25: Enable RDP.png*


I then performed the attack on the target machine.
Explanation of command used:
-b flag specifies service which is "rdp" in this case
-u flag specifies the account of interest. In this case, it is "tsmith"
-C flag specifies using a password list. In this case, it is called "passwords.txt"
-s flag specifies the source IP of the target machine including the CIDR notation. In this case, it is "192.168.10.100/32"


<img src="Images/Attack Command.png">

*Ref 26: Attack Command*

### 7. Brute Force Attack Analysis using Splunk
I navigated to the Splunk Forwarder and logged in. I then performed a search specifying the "endpoint" index and narrowed down the results by including "tsmith". The EventCode field showcases different values that can be searched for analysis purposes. The EventCode 4625 occurred 20 times.

<img src="Images/Splunk Event Code.png">

*Ref 27: Splunk Event Code*


Log Event ID 4625 specifies a failed login attempt as shown in the screenshot. This aligns with the brute force attack as there were 20 incorrect passwords in the "passwords.txt" file.

<img src="Images/Log Event ID 4625.png">

*Ref 28: Log Event ID 4625*



Log Event ID 4624 specifies a successful log-on.

<img src="Images/Log Event ID 4624.png">

*Ref 29: Log Event ID 4624*


By expanding the event, the workstation "kali" can be seen as the workstation name along with the IP of the Kali VM.

<img src="Images/Expand Event.png">

*Ref 30: Expand Event*

### 8. Brute Force Attack Analysis using Atomic Red Team

I first set an exclusion for the entire C Drive to ensure Microsoft Defender will not detect and remove any of the files from Atomic Red Team.

<img src="Images/Add Exclusion.png">

*Ref 31: Add Exclusion*


I then opened PowerShell as administrator and ran the following commands to install the tool Atomic Red Team from the GitHub portfolio "https://github.com/redcanaryco/invoke-atomicredteam/blob/master/install-atomicredteam.ps1". 

<img src="Images/Install ATR.png">

*Ref 32: Install ATR*


By navigating to the "atomcis" directory there is a list of technique IDs that map back to MITRE Attack Framework. For example, I examined "T1136.001" which is a Persistence tactic of "Create Account" specifically "Local Account".
<img src="Images/Atomics T1136.png">

*Ref 33: Atomics T1136*




<img src="Images/MITRE Framework.png">

*Ref 34: MITRE Framework*




To generate telemetry for the atomic the following command is used specifying for "T1136.001". It is important to note the "User name" created is "NewLocalUser".

<img src="Images/Atomic Telemetry.png">

*Ref 35: Atomic Telemetry*


I then went back to the Splunk web portal and searched specifically for "NewLocalUser". However, no events show up. This means that the domain is blind to this activity. In other words, if an attacker compromised the system and created a local account with the current settings, it would not detect that activity. This is a major benefit to Atomic Red Team as it will identify the gaps and visibility for you. As well as ATR will generate the telemetry to see if the activity can be detected.

<img src="Images/Splunk NewLocalUser Search.png">

*Ref 36: Splunk NewLocalUser Search*

























