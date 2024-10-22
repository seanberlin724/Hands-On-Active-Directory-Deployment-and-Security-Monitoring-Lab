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
Additionally, I modified the network settings on VirtualBox to NAT Network. This allows for the VMs to be on the same network and still have internet access. I created and named the NAT Network as "AD-Project" and set the IPv4 Prefix to "192.168.10.0/24". Be sure to also adjust the individual network settings for each individual VM to NAT Network as well.

*Ref 2: VM VirtualBox Manager of Active Machines Needed*

### 3. Configuring Sysmon and Splunk
I installed and configured Sysmon for logging system activity and set up Splunk as the Security Information and Event Management (SIEM) tool. This involved creating an 'endpoint' index in Splunk, managing data reception, and verifying that incoming events were logged correctly. This configuration is crucial for effective security monitoring and telemetry analysis.

To begin the configuration for the Splunk Server I started by setting a static IP. This was done by modifying the yaml config file located in the "/etc/netplan" directory. In this case, it was called "50-cloud-init.yaml". The IP was set to "192.168.10.10/24", DNS IP was set to "8.8.8.8" (Google's DNS), and the gateway IP (default route) to "192.168.10.1/24" 
<img src="Images/Splunk Yaml.png">

*Ref 3: 50-cloud-init.yaml file*

The next step was to install the guest add-ons for Virtual Box to enable the installation of Splunk from my host machine. I also "linked" the share folder of the VM to the folder on my host machine where I put the Splunk Installer in.
<img src="Images/Install Guest Add-ons and Link Shared Folder.png">

*Ref 4: Install Guest Add-ons and Link Shared Folder*




<img src="Images/Add User to vboxsf group.png">

*Ref 5: 50-cloud-init.yaml file*


### 4. Configuring Active Directory
In this phase, I installed Active Directory on Windows Server, promoted it to a domain controller, and created organizational units and users. I set a static IP address, verified connectivity, and successfully joined target machines to the new domain. This hands-on experience significantly enhanced my understanding of domain management and security considerations.
<img src="Images/4.png">

*Ref 4: Active Directory Configuration Steps*



### 5. Configuring Active Directory
I conducted a brute force attack using Kali Linux, targeting the Remote Desktop Protocol (RDP) on a Windows machine. This step included setting up Kali, installing the crowbar tool, and utilizing a wordlist for password attempts. I analyzed the generated telemetry with Splunk to gain insights into the attack process, improving my understanding of attacker behaviors and detection capabilities.
<img src="Images/5.png">

*Ref 5: Brute Force Attack Execution*






















