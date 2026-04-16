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
<img width="1028" height="785" alt="AD_Users" src="https://github.com/user-attachments/assets/c4173761-8e28-418e-89f8-eb27ac1b6788" />

* **Advanced OU Design:** Developed a scalable Organizational Unit (OU) structure consisting of departmental containers (Finance, IT, HR) to allow for granular GPO application.
* **Security Group Management:** Centralized all Security Groups into a dedicated "Groups" OU to separate access permissions from user objects.

<img width="512" height="389" alt="ADUC" src="https://github.com/user-attachments/assets/4ee90796-bd2a-41e9-9248-609b8adb3601" />



## **Phase 3: Domain Integration & Network Services Upgrade**
* **Domain Handshake:** Successfully integrated the Windows 11 Pro workstation into the `LAB.local` forest.

* **Trust Verification:** Utilized `sysdm.cpl ` to verify the trust relationship between the client and the Domain Controller.
<img width="1019" height="770" alt="Screenshot 2026-04-16 021037" src="https://github.com/user-attachments/assets/d12d04fc-bf09-4a27-849a-154be0d4966e" />

* **Transition to Automated Networking:** Migrated the client from a static configuration to a dynamic DHCP-managed environment. As shown in the `ipconfig` results, the client now automatically receives its IP allocation and DNS pointers from the Domain Controller, ensuring centralized network management.
<img width="511" height="385" alt="Screenshot 2026-04-16 011944" src="https://github.com/user-attachments/assets/0fc28295-5dc7-4d60-a485-0fc4e1827690" />


## **Phase 4: Resource Management (The "Help Desk" Scenario)**
I implemented departmental file sharing to simulate a corporate environment using a combination of GPO and scripting. 

* **Folder Permissions:** Configured NTFS and Share permissions to restrict access based on security groups.
* **Mapped Drives:** Verified that the IT user automatically received the correct network drive upon login.
<img width="1022" height="775" alt="Screenshot 2026-04-16 023656" src="https://github.com/user-attachments/assets/6f797efb-e9d6-4ddd-8fb8-ffac5c10e076" />

* **Automated Drive Mapping:** Authored and deployed `.bat` logon scripts via Group Policy to dynamically map the `B:` drive based on the user's specific department.
<img width="1025" height="780" alt="logon_propertities" src="https://github.com/user-attachments/assets/f043dff9-853d-4e3a-aeac-f166b3080b44" />
<img width="1019" height="773" alt="script" src="https://github.com/user-attachments/assets/29304439-ce85-47cd-9308-684fae8337b4" />
<img width="1025" height="772" alt="Screenshot 2026-04-15 154914" src="https://github.com/user-attachments/assets/7fe0b71f-fb9d-46c3-a876-6b8b316f54e3" />

* **Security Isolation**: Verified that while the drive is mapped via GPO, users are strictly blocked from accessing the folders outside their assigned security group.
<img width="1024" height="773" alt="Screenshot 2026-04-16 030320" src="https://github.com/user-attachments/assets/ae4a29bd-2728-48e3-8132-5603cb0dbd44" />


## **Phase 5: Network Services & Security Hardening**
To transition the lab from a basic setup to a secure enterprise environment, I implemented critical infrastructure services and defensive policies.

* **DHCP Server Deployment:** Configured a managed DHCP scope on the Domain Controller. Verified successful IP allocation (192.168.56.103) to the Windows 11 client via the DHCP Lease console and client-side `ipconfig` verification.
<img width="1026" height="778" alt="Screenshot 2026-04-16 011442" src="https://github.com/user-attachments/assets/e86d8545-fb50-4c87-bc14-9a9e6028a9c8" />
<img width="511" height="385" alt="Screenshot 2026-04-16 011944" src="https://github.com/user-attachments/assets/e6e35335-41c0-4f98-ae4f-18ea4be55be0" />


* **Brute-Force Protection:** Authored a "Security_Hardening_Policy" GPO to enforce an **Account Lockout Threshold** of 3 attempts.
<img width="1029" height="777" alt="Screenshot 2026-04-16 012238" src="https://github.com/user-attachments/assets/359a1ba4-9257-4bc7-8557-4d7604b5ea2a" />


* **Defensive Verification:** Successfully triggered and verified a lockout event on the Windows 11 workstation after multiple failed authentication attempts, simulating a real-world response to a brute-force attack.
<img width="1032" height="777" alt="Screenshot 2026-04-16 012354" src="https://github.com/user-attachments/assets/d0a8e517-6988-47c7-9d7c-45f56b98f62d" />


## **Phase 6: Application Support & Disaster Recovery (The "Help Desk" Mission)**
To simulate a Junior IT Engineer's daily responsibilities, I managed a local mail infrastructure and implemented a disaster recovery plan for end-user data.

* **Advanced Troubleshooting (Root Cause Analysis):** Successfully simulated and resolved a "Corrupt Profile" failure. By identifying and manipulating the `profiles.ini` (Configuration Settings) file, I demonstrated the ability to restore application functionality without data loss, bypassing the need for a full re-install.
<img width="1023" height="771" alt="Simulated_Profile_Failure_eml" src="https://github.com/user-attachments/assets/4720af1b-51bd-48f0-a491-8df4416317d0" />
<img width="1024" height="770" alt="Successful_Recovery_eml" src="https://github.com/user-attachments/assets/2f4df36b-bfd3-4049-9efe-e76908e9379a" />


* **Data Migration & Redundancy:** Executed a manual migration of local application databases (AppData/Roaming) from the Windows 11 workstation to a cross-platform **Shared Drive (S: Drive)** hosted on the Domain Controller.

* **Archival Compliance:** Built a structured archival hierarchy (`2025_Executive_Archive`) and validated the ingestion of "orphan" data files (`.eml`) into the local mail database.
<img width="1023" height="771" alt="Archival_Compliance" src="https://github.com/user-attachments/assets/71bf99db-d81d-473b-8c6e-2ffcc7382f11" />



## **Key Takeaways & Troubleshooting**
* **Issue:** Encountered a "Version Mismatch" where Windows 11 Home could not join a domain.
*   **Solution:** Successfully upgraded the VM to Windows 11 Pro to enable enterprise features.
* **Issue:** Kerberos Time Skew. Encountered an error where the computer clock was not synchronized with the Domain Controller.
*   **Solution**: Identified a VM clock drift mismatch and utilized `w32tm /resync` to align the client with the DC's NTP source
* **Security:** Implemented Account Lockout Policies to mitigate brute-force attacks.
* **Issue:** Application setup blocked by non-standard network environment (Thunderbird Wizard)
*   **Solution:** Utilized the Profile Manager (`P` flag) and Advanced Configuration toggles to initialize the application database manually, ensuring laboratory tasks could proceed despite UI limitations.

  
---

## **Future Enhancements**
To further expand this lab and simulate a more complex enterprise environment, I plan to implement:
* **Item-Level Targeting:** Consolidating multiple department rules into a single, high-effciency GPO.
* **WSUS (Windows Server Update Services):** Setting up a centralized server to manage and distribute patches and updates to all workstations in the domain.
* **Splunk/SIEM Integration:** Installing a Universal Forwarder on the Windows machines to ship logs to a SIEM for real-time security monitoring and threat detection.
* **VPN & Remote Access:** Configuring a Routing and Remote Access Service (RRAS) to simulate secure remote work connections.
