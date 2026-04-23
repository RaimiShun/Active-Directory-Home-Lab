# Active-Directory-Home-Lab
A functional Windows Server 2022 and Windows 11 enterprise lab environment. 

## **Objective**
The goal of this project was to build a functional, secure enterprise network environment using Oracle VirtualBox. This lab simulates a real-world corporate infrastructure, focusing on identity management, network configuration, and security principles like Least Privilege.

## **Core Competencies**
| Competency Area | Key Technologies & Skills |
| :--- | :--- |
| **Identity & Access** | Active Directory (AD DS), OU Design, Group Policy (GPO) |
| **Security & Auditing** | Account Lockout Policies, NTFS/Share Permissions, Hardening |
| **SIEM & Monitoring** | Splunk Enterprise, Universal Forwarder, Sysmon, Log Correlation |
| **Infrastructure** | DHCP, DNS, Network Mapping, Virtualization (Oracle VM) |
| **Incident Response** | Root Cause Analysis, Forensics, Log Aggregation, Troubleshooting |

## **Technologies Used**
* **Hypervisor:** Oracle VirtualBox
* **Operating Systems:** Windows Server 2022, Windows 11 Pro
* **Services:** Active Directory Domain Services (AD DS), DNS, SMB File Sharing, Group Policy (GPO)
* **Monitoring/SIEM:** Splunk Enterprise (Indexer), Splunk Universal Forwarder (Agent)

## **Lab Asset Inventory**
| Asset Name | Operating System | Role / Service | Network Configuration |
| :--- | :--- | :--- | :--- |
| **LAB-DC01** | Windows Server 2022 | Domain Controller, DNS, DHCP, WSUS | Static: 192.168.56.10 |
| **LAB-WS01** | Windows 11 Pro | Client Workstation | DHCP Assigned |
| **S: Drive** | SMB Share | Centralized User Backups | Hosted on DC01 |


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



## **Phase 3: Domain Integration & Identity Validation**
* **Domain Handshake:** Successfully integrated the Windows 11 Pro workstation into the `LAB.local` forest.

* **Trust Verification:** Utilized `sysdm.cpl ` to verify the trust relationship between the client and the Domain Controller.
<img width="1019" height="770" alt="Screenshot 2026-04-16 021037" src="https://github.com/user-attachments/assets/d12d04fc-bf09-4a27-849a-154be0d4966e" />



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

* **Transition to Automated Networking:** Migrated the client from a static configuration to a dynamic DHCP-managed environment. As shown in the `ipconfig` results, the client now automatically receives its IP allocation and DNS pointers from the Domain Controller, ensuring centralized network management.
<img width="511" height="385" alt="Screenshot 2026-04-16 011944" src="https://github.com/user-attachments/assets/0fc28295-5dc7-4d60-a485-0fc4e1827690" />

* **Brute-Force Protection:** Authored a "Security_Hardening_Policy" GPO to enforce an **Account Lockout Threshold** of 3 attempts.
<img width="1029" height="777" alt="Screenshot 2026-04-16 012238" src="https://github.com/user-attachments/assets/359a1ba4-9257-4bc7-8557-4d7604b5ea2a" />


* **Defensive Verification:** Successfully triggered and verified a lockout event on the Windows 11 workstation after multiple failed authentication attempts, simulating a real-world response to a brute-force attack.
<img width="1032" height="777" alt="Screenshot 2026-04-16 012354" src="https://github.com/user-attachments/assets/d0a8e517-6988-47c7-9d7c-45f56b98f62d" />


## **Phase 6: Application Support & Disaster Recovery**
To simulate a Junior IT Engineer's daily responsibilities, I managed a local mail infrastructure and implemented a platform-agnostic disaster recovery plan for end-user data. 

* **Logic-Based Troubleshooting:** Demonstrated that technical support principles are transferable across platforms. While utilizing Thunderbird for this lab, the methodology applied (Profile Management and Configuration File manipulation) directly simulates enterprise Microsoft Outlook troubleshooting (e.g., resolving .PST/.OST corruption).

* **Advanced Troubleshooting (Root Cause Analysis):** Successfully simulated and resolved a "Corrupt Profile" failure. By identifying and manipulating the `profiles.ini` (Configuration Settings) file, I demonstrated the ability to restore application functionality without data loss, bypassing the need for a full re-install.
<img width="1023" height="771" alt="Simulated_Profile_Failure_eml" src="https://github.com/user-attachments/assets/4720af1b-51bd-48f0-a491-8df4416317d0" />
<img width="1024" height="770" alt="Successful_Recovery_eml" src="https://github.com/user-attachments/assets/2f4df36b-bfd3-4049-9efe-e76908e9379a" />

* **Data Migration & Redundancy:** Executed a manual migration of local application databases (AppData/Roaming) from the Windows 11 workstation to a cross-platform **Shared Drive (S: Drive)** hosted on the Domain Controller.

* **Archival Compliance:** Built a structured archival hierarchy (`2025_Executive_Archive`) and validated the ingestion of "orphan" data files (`.eml`) into the local mail database.
<img width="1023" height="771" alt="Archival_Compliance" src="https://github.com/user-attachments/assets/71bf99db-d81d-473b-8c6e-2ffcc7382f11" />

## **Phase 7: Governance, Monitoring & Incident Response**
To transition from a "working" lab to a "managed" enterprise environment, I implemented a maintenance and security monitoring framework to ensure high availability and audit compliance.

* **Domain Health Diagnostics:** Executed `dcdiag` to verify the health of the `LAB.local` forest. Confirmed successful status for core services including **Connectivity**, **Advertising**, **MachineAccount**, and **NetLogons**.
<img width="1026" height="767" alt="Screenshot 2026-04-16 231227" src="https://github.com/user-attachments/assets/45ec4a6b-ad5a-4bba-ac13-e7f5fea3cc4d" />
<img width="1021" height="774" alt="Screenshot 2026-04-16 231702" src="https://github.com/user-attachments/assets/acdf0c81-43c6-4af5-9e5b-90945d69a770" />
<img width="1028" height="770" alt="Screenshot 2026-04-16 231855" src="https://github.com/user-attachments/assets/8009089f-07d8-4a41-8c8c-a19d66e7e013" />


* **Advanced Security Auditing:** Structured Active Directory Organizational Units (OUs) to enforce **Advanced Audit Policies**. By moving the workstation from a default container to a managed OU, I enabled granular tracking of authentication events.
* **Incident Capture & Log Correlation:** Successfully simulated a brute-force attack and validated the detection of **Event ID 4625** (Logon Failure) on the Windows 11 endpoint, correlating it with **Event ID 4740** (Account Lockout) on the Domain Controller.
<img width="1021" height="771" alt="Screenshot 2026-04-17 004607" src="https://github.com/user-attachments/assets/21c89942-22fd-4538-9131-bb0cbea401fa" />
<img width="1006" height="783" alt="Screenshot 2026-04-17 004825" src="https://github.com/user-attachments/assets/85b185d4-7d02-4d01-b8e9-9db47eda954a" />


* **Performance Baselining:** Utilized Windows Resource Monitor to baseline CPU, Disk, and Network throughput. Verified server stability during active file-share utilization and high-volume data transfers.
<img width="1024" height="783" alt="Screenshot 2026-04-17 005119" src="https://github.com/user-attachments/assets/414bd24a-452f-4d9b-846c-c048f2ef9edd" />

## **Phase 8: SIEM Integration & Real-Time Monitoring**
To transition from reactive troubleshooting to proactive monitoring, I deployed a Splunk SIEM environment to centralize logs from across the domain. 

## **Indexer Setup (Server VM)**
* **Networking:** Enabled TCP 9997 to receive data from the Universal Forwarder
<img width="1023" height="773" alt="Splunk_Indexer_Setup" src="https://github.com/user-attachments/assets/f73e062c-4d95-4061-ac94-72d276b555a0" />

* **Configuration:** Manually tuned `inputs.conf` to monitor local Security, System, and Application logs, providing 100% visibility into Domain Controller activity.
<img width="1025" height="768" alt="Screenshot 2026-04-18 221938" src="https://github.com/user-attachments/assets/9f1422fb-f3ee-4685-ad8a-5dced0bc049a" />


* **Endpoint Agent (Windows 11 VM):** Deployed the Splunk Universal Forwarder. Configured the agent to push Windows Event Logs (Security, System, Application) to the Indexer.
<img width="1026" height="770" alt="Screenshot 2026-04-18 221557" src="https://github.com/user-attachments/assets/fa55cfe2-dd05-41b0-b79a-cda136b44b06" />


* **Data Pipeline Verfication:** Confirmed active data flow by quering `index=main` and verifying the presence of both `LAB-DC01` and `LAB-WS01` as active hosts.
<img width="1022" height="767" alt="Screenshot 2026-04-18 201315" src="https://github.com/user-attachments/assets/ce07cc56-8baf-4103-a574-9c5876a873f5" />

## **Case Study: Incident Response (The "Locked-Out User")**
**Scenario:** A user reports they are locked out of their workstation. As a Junior IT Engineer, I used the SIEM to perform a Root Cause Analysis. 

## **Detection** 
I queried Splunk for `EventCode=4740`. This instantly identified that the account `IT_Admin` was locked out by the Domain Controller. 
<img width="1022" height="772" alt="Splunk_Scenario_2" src="https://github.com/user-attachments/assets/fc09a027-2c5d-4544-89a3-3e3b1e3d7c24" />
<img width="1021" height="773" alt="Splunk_Scenario_1" src="https://github.com/user-attachments/assets/57b93861-bef4-48e2-b5b3-b51183d817fc" />

## **Forensics**
By analyzing `EventCode=4625` (Logon Failure), I confirmed the source was the Windows 11 workstation. This allowed me to rule out a network-wide attack and focus on the local machine.
<img width="1023" height="776" alt="Splunk_Scenario_3" src="https://github.com/user-attachments/assets/006e868e-7442-47c4-99c2-0a28c462bacb" />

## **Resolution** 
I performed a manual account unlock in Active Directory and verified the user could re-authenticate successfully.
<img width="1015" height="773" alt="Splunk_Scenario_4" src="https://github.com/user-attachments/assets/2f1ea630-9897-4530-bb01-f8c628266286" />

## **Phase 9: Patch Management & Compliance (WSUS)**
To transition the lab from an "unmanaged" environment to a governed enterprise network, I deployed **Windows Server Update Services (WSUS)** on the Domain Controller.

* **Policy-Driven Updates (GPO):** Configured Group Policy Objects to redirect the workstation to the local update server. As shown in the client-side view, the "managed" status is verified by the system blocking manual user overrides.
<img width="1022" height="772" alt="Screenshot 2026-04-23 153748" src="https://github.com/user-attachments/assets/33dc9617-9d7d-49b7-9bd3-004eb0cd469c" />


* **Centralized Administration:** Utilized the WSUS console to organize assets into a dedicated `Lab_Workstations` group. Successfully synchronized 335 updates, achieving a 99% patch compliance rate across the domain.
<img width="1019" height="773" alt="Screenshot 2026-04-23 154418" src="https://github.com/user-attachments/assets/c816b4c9-e744-47f7-bae9-cca8b415e7b2" />


* **Infrastructure Troubleshooting & Reporting:** Resolved a critical dependency issue involving legacy SQL CLR and Report Viewer runtimes on Server 2022. This enabled the generation of "Computer Detailed Status Reports," providing actionable security audits for stakeholders.
<img width="1023" height="772" alt="Screenshot 2026-04-23 162445" src="https://github.com/user-attachments/assets/c0abd013-f72a-4125-b2b2-19513d58f73d" />






## **Key Takeaways & Troubleshooting**
* **Issue:** Encountered a "Version Mismatch" where Windows 11 Home could not join a domain.
   *   **Solution:** Successfully upgraded the VM to Windows 11 Pro to enable enterprise features.

* **Issue:** Kerberos Time Skew. Encountered an error where the computer clock was not synchronized with the Domain Controller.
   *   **Solution**: Identified a VM clock drift mismatch and utilized `w32tm /resync` to align the client with the DC's NTP source
      * **Security:** Implemented Account Lockout Policies to mitigate brute-force attacks.
       
* **Issue:** Application setup blocked by non-standard network environment (Thunderbird Wizard)
   *   **Solution:** Utilized the Profile Manager (`P` flag) and Advanced Configuration toggles to initialize the application database manually, ensuring laboratory tasks could proceed despite UI limitations.

* **Issue:** GPO settings (Audit Policies) were not applying to the Windows 11 workstation.
    * **Root Cause:** The workstation was located in the default "Computers" container, which does not                            support GPO linking.
    * **Solution:** Created a dedicated Workstation OU, moved the computer object, and performed a                              `gpupdate /force` to successfully trigger policy inheritance.
      
* **Issue:** Event Viewer GUI "Query Timeout" or hang on the client machine.
    * **Solution:** Pivoted to PowerShell-based forensics using `Get-EventLog` to bypass GUI limitations                        and extract security data directly from the `.evtx` buffer.
   
* **Issue:** Splunk CLI returned "parameter name: path must be a file or directory" when adding monitors.
   * **Root Cause:** The CLI was misinterpreting the Windows Event Log provider as a local file path.
   * **Solution:** Pivoted to manual configuration by authoring a custom `inputs.conf` file in                                 `etc/system/local`, ensuring proper ingestion of the Windows Security buffer.
 
* **Issue:** "Local Event Log Collection" UI returned a 404 error on the Server VM.
   *    **Solution:** Bypassed the GUI limitation by manually editing the server-side configuration files             and restarting the Splunk service via CLI to force-enable local log indexing. 

* **Issue:** WSUS Reporting console failed to load on Windows Server 2022.
   * **Root Cause:** A legacy hard-coded dependency requiring Microsoft Report Viewer 2012, which is not                         included in modern Server OS builds.
   * **Solution:** Navigated the prerequisite chain by manually installing the SQL Server 2012 System CLR                      Types library followed by the Report Viewer 2012 Runtime, enabling the generation of                        PDF/GUI compliance reports.
 
* **Issue:** Windows 11 client stuck at "Downloading 0%" from the local WSUS server.
   * **Solution:** Performed a SoftwareDistribution cache reset on the client machine and utilized `wuauclt /reportnow` to force a fresh handshake with the Domain Controller, resolving the hung                       update task.

---

## **Future Enhancements**
To further expand this lab, I plan to implement:
* **[Item-Level Targeting](docs/GPO_Optimization.md):** Consolidating multiple department rules into a single, high-efficiency GPO.
* **[VPN & Remote Access](docs/VPN_Configuration.md):** Configuring a Routing and Remote Access Service (RRAS) to simulate secure remote work.
