# Active-Directory-Home-Lab
A functional Windows Server 2022 and Windows 11 enterprise lab environment. 

## **Objective**
The goal of this project was to build a functional, secure enterprise network environment using Oracle VirtualBox. This lab simulates a real-world corporate infrastructure, focusing on identity management, network configuration, and security principles like Least Privilege.

## **Technologies Used**
* **Hypervisor:** Oracle VirtualBox
* **Operating Systems:** Windows Server 2022, Windows 11 Pro
* **Services:** Active Directory Domain Services (AD DS), DNS, SMB File Sharing, Group Policy (GPO)


## **Phase 1: Network Architecture** 
To ensure security and isolation, I implemented a dual-adapter network topology.
* **Internal Network (Host-Only):** Dedicated for private communication between the Domain Controller and Client.
* **NAT:** Provided controlled internet access for system updates and software installation.
<img width="957" height="517" alt="Windows10-Server_Network_Settings" src="https://github.com/user-attachments/assets/f55d0665-537a-446a-a49f-5aab6164df0d" />
<img width="956" height="518" alt="Windows11_Network_Settings" src="https://github.com/user-attachments/assets/162d16eb-6967-4091-b456-84b929c8c382" />



## **Phase 2: Active Directory & User Management**
* I deployed Windows Server 2022 as a Domain Controller for the LAB.local forest.
* Created a custom Organizational Unit (OU) named "Accounts" to organize staff members.
* Provisioned domain users with standardized naming conventions.
* **Advanced OU Design:** Developed a scalable Organizational Unit (OU) structure consisting of departmental containers (Finance, IT, HR) to allow for granular GPO application.
* **Security Group Management:** Centralized all Security Groups into a deidicated "Groups" OU to separate access permissions from uses objects.

<img width="1028" height="785" alt="AD_Users" src="https://github.com/user-attachments/assets/c4173761-8e28-418e-89f8-eb27ac1b6788" />
<img width="512" height="389" alt="ADUC" src="https://github.com/user-attachments/assets/4ee90796-bd2a-41e9-9248-609b8adb3601" />



## **Phase 3: Client Integration & DNS Resolution**
* The Windows 11 Pro workstation was successfully integrated into the domain.
* DNS Configuration: Manually pointed the client to the Domain Controller's IP to ensure proper name resolution.
* Domain Join: Verified the trust relationship between the client and server.

<img width="512" height="380" alt="sysdm cpl_System_Properties" src="https://github.com/user-attachments/assets/da4f4383-0073-43c3-9c47-d815c5c416af" />
<img width="512" height="388" alt="IPConfig" src="https://github.com/user-attachments/assets/b4423007-5d3b-4e7f-aae3-ba7d91173d83" />



## **Phase 4: Resource Management (The "Help Desk" Scenario)**
I implemented departmental file sharing to simulate a corporate environment using a combination of GPO and scripting. 

* **Folder Permissions:** Configured NTFS and Share permissions to restrict access based on security groups.
* **Mapped Drives:** Verified that the Finance user automatically received the correct network drive upon login.
* **Automated Drive Mapping:** Authored and deployed **.bat** logon scripts via Group Policy to dynamically map the **A:**, **B:** drive based on the user's specific department.
* **Security Isolation**: Verified that while the drive is mapped via GPO, users are strictly blocked from accesing the folders outside their assigned security group.

<img width="515" height="389" alt="Mapped_Drive" src="https://github.com/user-attachments/assets/506b3694-414b-43d7-97d4-a95908313d1d" />
<img width="1025" height="780" alt="logon_propertities" src="https://github.com/user-attachments/assets/f043dff9-853d-4e3a-aeac-f166b3080b44" />
<img width="1019" height="773" alt="script" src="https://github.com/user-attachments/assets/29304439-ce85-47cd-9308-684fae8337b4" />
<img width="512" height="386" alt="Screenshot 2026-04-15 155231" src="https://github.com/user-attachments/assets/7ee2d3f2-947e-4a13-a3cd-554fbc2dd7e6" />
<img width="1025" height="772" alt="Screenshot 2026-04-15 154914" src="https://github.com/user-attachments/assets/7fe0b71f-fb9d-46c3-a876-6b8b316f54e3" />



## **Key Takeaways & Troubleshooting**
* **Issue:** Encountered a "Version Mismatch" where Windows 11 Home could not join a domain.
*   **Solution:** Successfully upgraded the VM to Windows 11 Pro to enable enterprise features.
* **Issue:** Kerberos Time Skew. Encountered an error where the computer clock was not synchronized with the Domain Controller.
*   **Solution**: Identified a VM clock drift mismatch and utilized **w32tm /resync** to align the client with the DC's NTP source
* **Security:** Implemented Account Lockout Policies to mitigate brute-force attacks.

---

## **Future Enhancements**
To further expand this lab and simulate a more complex enterprise environment, I plan to implement:
* **Item-Level Targeting:** Consolidating multiple department rules into a single, high-effciency GPO.
* **WSUS (Windows Server Update Services):** Setting up a centralized server to manage and distribute patches and updates to all workstations in the domain.
* **Splunk/SIEM Integration:** Installing a Universal Forwarder on the Windows machines to ship logs to a SIEM for real-time security monitoring and threat detection.
* **VPN & Remote Access:** Configuring a Routing and Remote Access Service (RRAS) to simulate secure remote work connections.
