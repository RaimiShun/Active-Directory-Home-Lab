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
<img width="1274" height="1051" alt="Settings" src="https://github.com/user-attachments/assets/fb62ea93-e00d-49c4-9e81-2547e68b29df" />

<img width="1283" height="1096" alt="Settings (2)" src="https://github.com/user-attachments/assets/5e4b179d-9913-417a-9a32-6a419853ec7e" />


## **Phase 2: Active Directory Architecture & Identity Design**
**Objective:** Establishing a scalable, enterprise-grade Identity and Access Management (IAM) foundation within the LAB.local forest.

### **Hierarchical Organizational Unit (OU) Design**

* **Scalable Framework:** Implemented a tiered OU structure to facilitate granular Group Policy application and administrative delegation.

* **Separation of Concerns:** Isolated Administrative Accounts, Standard Accounts (departmentalized by Finance, HR, and IT), and Security Groups into distinct containers to prevent policy leakage.

* **Standardized Provisioning:** Developed an account naming and description convention to simulate enterprise auditing requirements, as seen in the profile for John Wick (Finance Manager).

<img width="1023" height="774" alt="AD UC" src="https://github.com/user-attachments/assets/41251eb9-7c8a-42c7-b25d-5d2b0e54d966" />



## **Phase 3: Endpoint Enrollment & Trust Validation** 
**Objective:** Securely integrating client assets into the domain and verifying the Kerberos trust relationship.

* **Domain Handshake:** Successfully migrated the Windows 11 Pro workstation from a standalone workgroup to the LAB.local managed forest.

* **Security Validation:** Utilized sysdm.cpl to verify the authoritative trust relationship between the endpoint (WS01.LAB.local) and the Domain Controller.

* **Identity Handshake:** Confirmed that the workstation successfully honors domain credentials, allowing for centralized authentication and policy inheritance from the DC.

<img width="1017" height="863" alt="Trust Validation" src="https://github.com/user-attachments/assets/693683f2-6978-4ab6-8fc1-a1a5cc2a3a04" />


## **Phase 4: Enterprise Resource Management & Identity-Based Access**
I transitioned the lab from basic file sharing to an Identity-Based Access Control (IBAC) model, simulating how a Tier 2 SysAdmin manages corporate data at scale.

* **Scalable OU & Group Architecture:** Abandoned flat structures in favor of a tiered OU hierarchy. I separated Administrative Accounts from Standard Accounts and centralized permissions into dedicated Security Groups (e.g., `SG_Finance_Users`, `SG_IT_Admins`).

* **Dynamic Drive Mapping via GPP:** Replaced legacy `.bat` logon scripts with modern Group Policy Preferences (GPP). I utilized Item-Level Targeting and Security Filtering to ensure that a single GPO dynamically maps resources only to authorized users.

* **Access-Based Enumeration (ABE):** Implemented ABE on the global `\\DC01\Shares` root. This ensures "Security through Obscurity" by hiding folders (HR, IT, Finance) from any user who does not have explicit NTFS Read permissions, preventing internal reconnaissance.

* **Verification:** As shown below, `John Wick (Finance)` can only see the Finance directory, while `Raimi (IT Admin)` has full visibility into all departmental archives.

<img width="1023" height="770" alt="Finance only" src="https://github.com/user-attachments/assets/e683699f-e3a3-4723-835d-3af31d66d5b0" />

<img width="1026" height="772" alt="IT Admin" src="https://github.com/user-attachments/assets/187e2b29-3ce6-4a22-b5a5-6da512aa7db9" />

### **Granular Permission Audit (NTFS Verification)**
To validate that the Principle of Least Privilege was successfully applied beyond just visibility, I utilized the Effective Access tool to audit specific NTFS permissions for the Finance directory.

* **Administrative Oversight:** Confirmed that `IT_Admin` (Raimi) retains Full Control, ensuring administrative continuity and the ability to manage data lifecycle.

* **Restricted Management:** Verified that `Finance_Manager` (John Wick) has necessary Read/Write access but is strictly denied the ability to change permissions or take ownership. This prevents accidental or malicious privilege escalation within the department.

<img width="1021" height="773" alt="effective access 2" src="https://github.com/user-attachments/assets/76202c6b-bac2-42f9-a3cc-8b890cf14bf6" />

<img width="1021" height="764" alt="effetive access" src="https://github.com/user-attachments/assets/e241c65b-8c83-4cb9-990b-a5f1d628566f" />


## **Phase 5: Network Services & Security Hardening**
To transition the lab into a **"Hardened"** state, I implemented centralized networking and defensive auditing policies.

### **Centralized Network Management (DHCP)**

* **Managed Infrastructure:** Deployed a managed DHCP Scope (`192.168.56.100 - 192.168.56.200`) on the Domain Controller to eliminate static IP overhead.
 
* **DNS Infrastructure Integration:** Configured DHCP Option 006 (DNS Servers) to automatically assign the Domain Controller’s IP (`192.168.56.10`) to all network clients. This ensures seamless Active Directory service discovery and reliable Kerberos authentication across the forest.
 
* **Validation:** Verified the **"Handshake"** by migrating the **Windows 11** client to a dynamic lease.

<img width="1022" height="766" alt="DHCP Handshake" src="https://github.com/user-attachments/assets/ee4e36af-523e-4b26-88e4-6b419c2858f0" />

<img width="1018" height="764" alt="DNS Server 006" src="https://github.com/user-attachments/assets/ae4501ab-1528-415c-a152-2f35c2229dd2" />


### **Brute-Force Mitigation & GPO Hardening** 

* **Strategy:** Authored and enforced a **"GPO Security Hardening Baseline"** to mitigate automated credential-stuffing and brute-force attacks.

* **Policy Configuration:** Enforced an Account Lockout Threshold of 3 failed attempts with a 30-minute reset window.

<img width="1022" height="766" alt="Security hardening GPO" src="https://github.com/user-attachments/assets/495f4397-a681-4e21-a365-7dd530897783" />


### **Security Auditing & Incident Simulation**

* **The Attack Simulation:** Triggered a deliberate lockout on the **Windows 11** workstation after exceeding the authorized logon attempts.

* **The Forensic Chain:** Configured the **Audit Account Management** policy to track identity-based threats.

* **Log Correlation:** Successfully correlated Event ID 4740 in the Domain Controller’s Security Log, proving the "Detection-to-Action" pipeline is fully operational.

<img width="1016" height="757" alt="AC Lockout" src="https://github.com/user-attachments/assets/74945b68-ca9e-445b-8dc9-9463e59fd2ba" />

<img width="1026" height="776" alt="Audit Policy" src="https://github.com/user-attachments/assets/3439bbc0-7784-4522-916e-efec77589c7b" />

<img width="1022" height="769" alt="AC Lockout Eventviewer" src="https://github.com/user-attachments/assets/7bd103df-42d4-4af5-b01d-de7809e9702f" />


## **Phase 6: Data Governance & Resiliency**
**Objective:** Implementing enterprise-grade data protection through automation and surgical recovery methods. 

### **Task A: Automated Endpoint Migration**
* **Action:** Deployed a PowerShell script to migrate "Critical Project" data from local user desktops to the centralized **S: Drive**.

* **Impact:** Eliminated local data silos and ensured all business-critical files are included in server-side backup rotations.

<img width="1021" height="766" alt="Script" src="https://github.com/user-attachments/assets/093bab95-0e5a-4bae-b1dd-ef5c7b97ff40" />

### **Task B: Legacy Application Risk Mitigation**

* **Action:** Identified vulnerable legacy accounting data and migrated it to a **Hidden Administrative Share** (`Secure_Archives$`).

* **Impact:** Reduced the attack surface by hiding the share from standard network discovery and enforcing IT-Admin-only access.

<img width="1020" height="769" alt="Legacy migration" src="https://github.com/user-attachments/assets/cf3c023e-da90-42fa-9777-56b4d8870500" />

### **Task C: Disaster Recovery (VSS)**

* **Action:** Configured Volume Shadow Copy Service (VSS) for point-in-time recovery.

* **Impact:** Enabled self-service "Previous Version" restoration, significantly reducing Helpdesk RTO (Recovery Time Objective) for accidental deletions.

<img width="1020" height="774" alt="Shadowcopies" src="https://github.com/user-attachments/assets/e7af4c88-86fe-4a4b-ae1e-fd2b17f341fe" />

<img width="1019" height="769" alt="self-service recovery" src="https://github.com/user-attachments/assets/52bda06a-b728-4675-b7d7-cd9591a0680b" />


## **Phase 7: Infrastructure Health & Validation**
**Objective:** Final validation of Domain integrity and performance baselining.

### **Active Directory Health Audit:**
Executed `dcdiag` to verify forest connectivity, advertising, and replication status. All tests passed.

<img width="1019" height="771" alt="dcdiag" src="https://github.com/user-attachments/assets/bc3fc8cf-e123-4b35-9e8d-4068fea5ba9c" />


### **Perfomance Monitoring:**
Utilized **Resource Monitor** to baseline network and disk I/O, ensuring no bottlenecks exist within the virtualized environment.

<img width="1019" height="766" alt="resmon" src="https://github.com/user-attachments/assets/dfbd871b-fd10-4502-93a2-39cfb43c306a" />


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

### **Policy-Driven Updates (GPO):**
Configured Group Policy Objects to redirect the workstation to the local update server. As shown in the client-side view, the "managed" status is verified by the system blocking manual user overrides.

<img width="1022" height="772" alt="Screenshot 2026-04-23 153748" src="https://github.com/user-attachments/assets/33dc9617-9d7d-49b7-9bd3-004eb0cd469c" />


### **Centralized Administration:** 
Utilized the WSUS console to organize assets into a dedicated `Lab_Workstations` group. Successfully synchronized 335 updates, achieving a 99% patch compliance rate across the domain.

<img width="1019" height="773" alt="Screenshot 2026-04-23 154418" src="https://github.com/user-attachments/assets/c816b4c9-e744-47f7-bae9-cca8b415e7b2" />

### **Infrastructure Troubleshooting & Reporting:** 
Resolved a critical dependency issue involving legacy SQL CLR and Report Viewer runtimes on Server 2022. This enabled the generation of "Computer Detailed Status Reports," providing actionable security audits for stakeholders.

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
