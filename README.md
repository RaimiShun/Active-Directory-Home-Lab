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
| **Remote Access** | VPN (L2TP/IPsec), RRAS, NPS, Perimeter Security |

## **Technologies Used**
* **Hypervisor:** Oracle VirtualBox
* **Operating Systems:** Windows Server 2022, Windows 11 Pro
* **Services:** Active Directory Domain Services (AD DS), DNS, SMB File Sharing, Group Policy (GPO), Routing & Remote Access (RRAS), Network Policy Server (NPS)
* **Monitoring/SIEM:** Splunk Enterprise (Indexer), Splunk Universal Forwarder (Agent)

## **Lab Asset Inventory**
| Asset Name | Operating System | Role / Service | Network Configuration |
| :--- | :--- | :--- | :--- |
| **LAB-DC01** | Windows Server 2022 | Domain Controller, DNS, DHCP, WSUS, VPN Gateway | Static: 192.168.56.10 |
| **LAB-WS01** | Windows 11 Pro | Client Workstation | Interal Network (LabNet) |
| **S: Drive** | SMB Share | Centralized User Backups | Hosted on DC01 |
| **VPN Pool** | Virtual Interface | Remote Client Access | 10.0.0.100 - 10.0.0.150 | 


## **Phase 1: Network Architecture** 
To ensure security and isolation, I implemented a dual-adapter network topology.

* **Internal Network (Host-Only):** Dedicated for private communication between the Domain Controller and Client.

* **NAT:** Provided controlled internet access for system updates and software installation.
<img width="1274" height="1051" alt="Settings" src="https://github.com/user-attachments/assets/fb62ea93-e00d-49c4-9e81-2547e68b29df" />

<img width="1283" height="1096" alt="Settings (2)" src="https://github.com/user-attachments/assets/5e4b179d-9913-417a-9a32-6a419853ec7e" />


## **Phase 2: Active Directory Architecture & Identity Design**
**Objective:** Establishing a scalable, enterprise-grade **Identity and Access Management (IAM)** foundation within the `LAB.local forest`.

### **Hierarchical Organizational Unit (OU) Design**

* **Scalable Framework:** Implemented a tiered OU structure to facilitate granular **Group Policy application** and **administrative delegation.**

* **Separation of Concerns:** Isolated `Administrative Accounts`, `Standard Accounts (departmentalized by Finance, HR, and IT)`, and `Security Groups` into distinct containers to prevent policy leakage.

* **Standardized Provisioning:** Developed an account naming and description convention to simulate enterprise auditing requirements, as seen in the profile for `John Wick (Finance Manager)`.

<img width="1023" height="774" alt="AD UC" src="https://github.com/user-attachments/assets/41251eb9-7c8a-42c7-b25d-5d2b0e54d966" />



## **Phase 3: Endpoint Enrollment & Trust Validation** 
**Objective:** Securely integrating client assets into the domain and verifying the Kerberos trust relationship.

* **Domain Handshake:** Successfully migrated the **Windows 11 Pro** workstation from a standalone workgroup to the `LAB.local` managed forest.

* **Security Validation:** Utilized `sysdm.cpl` to verify the authoritative trust relationship between the endpoint **(WS01.LAB.local)** and the Domain Controller.

* **Identity Handshake:** Confirmed that the workstation successfully honors domain credentials, allowing for centralized authentication and policy inheritance from the **DC**.

<img width="1017" height="863" alt="Trust Validation" src="https://github.com/user-attachments/assets/693683f2-6978-4ab6-8fc1-a1a5cc2a3a04" />


## **Phase 4: Enterprise Resource Management & Identity-Based Access**
I transitioned the lab from basic file sharing to an **Identity-Based Access Control (IBAC)** model, simulating how a **Tier 2 SysAdmin** manages corporate data at scale.

### **Scalable OU & Group Architecture:** 
Abandoned flat structures in favor of a tiered OU hierarchy. I separated `Administrative Accounts` from `Standard Accounts` and centralized permissions into dedicated `Security Groups` (e.g., `SG_Finance_Users`, `SG_IT_Admins`).

### **Dynamic Drive Mapping via GPP & Item-Level Targeting:** 
Replaced legacy `.bat` logon scripts with modern **Group Policy Preferences (GPP)**. I consolidated multiple departmental policies into a single, high-efficiency GPO that utilizes **Item-Level Targeting** to dynamically map the `S: Drive` based on the user's specific Security Group membership.

* **The Logic:** The GPO evaluates group membership (e.g., LAB\SG_HR) at logon before applying the mapping.

* **The Validation:** Verified with a test user **(Sarah Potter)**, ensuring the drive only maps once the security token is refreshed.

<img width="1020" height="767" alt="Screenshot 2026-04-29 020044" src="https://github.com/user-attachments/assets/058b713f-e00d-478b-81fc-82e5dcf0c39d" /> 

<img width="1026" height="768" alt="Screenshot 2026-04-29 022850" src="https://github.com/user-attachments/assets/c4edf13e-1901-4351-b9b1-ab59eb16e965" />


### **Access-Based Enumeration (ABE):** 
Implemented ABE on the global `\\DC01\Shares` root. This ensures "Security through Obscurity" by hiding folders **(HR, IT, Finance)** from any user who does not have explicit **NTFS** **Read** permissions, preventing internal reconnaissance.

* **Verification:** As shown below, `John Wick (Finance)` can only see the Finance directory, while `Raimi (IT Admin)` has full visibility into all departmental archives.

<img width="1023" height="770" alt="Finance only" src="https://github.com/user-attachments/assets/e683699f-e3a3-4723-835d-3af31d66d5b0" />

<img width="1026" height="772" alt="IT Admin" src="https://github.com/user-attachments/assets/187e2b29-3ce6-4a22-b5a5-6da512aa7db9" />

### **Granular Permission Audit (NTFS Verification)**
To validate that the **Principle of Least Privilege** was successfully applied beyond just visibility, I utilized the **Effective Access** tool to audit specific **NTFS permissions** for the `Finance` directory.

* **Administrative Oversight:** Confirmed that `IT_Admin` (Raimi) retains **Full Control**, ensuring administrative continuity and the ability to manage data lifecycle.

* **Restricted Management:** Verified that `Finance_Manager` (John Wick) has necessary `Read/Write` access but is strictly denied the ability to change permissions or take ownership. This prevents accidental or malicious privilege escalation within the department.

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

* **Policy Configuration:** Enforced an **Account Lockout Threshold** of **3 failed attempts** with a **30-minute** reset window.

<img width="1022" height="766" alt="Security hardening GPO" src="https://github.com/user-attachments/assets/495f4397-a681-4e21-a365-7dd530897783" />


### **Security Auditing & Incident Simulation**

* **The Attack Simulation:** Triggered a deliberate lockout on the **Windows 11** workstation after exceeding the authorized logon attempts.

* **The Forensic Chain:** Configured the **Audit Account Management** policy to track identity-based threats.

* **Log Correlation:** Successfully correlated `Event ID 4740` in the **Domain Controller’s Security Log**, proving the "Detection-to-Action" pipeline is fully operational.

<img width="1016" height="757" alt="AC Lockout" src="https://github.com/user-attachments/assets/74945b68-ca9e-445b-8dc9-9463e59fd2ba" />

<img width="1026" height="776" alt="Audit Policy" src="https://github.com/user-attachments/assets/3439bbc0-7784-4522-916e-efec77589c7b" />

<img width="1022" height="769" alt="AC Lockout Eventviewer" src="https://github.com/user-attachments/assets/7bd103df-42d4-4af5-b01d-de7809e9702f" />


## **Phase 6: Data Governance & Resiliency**
**Objective:** Implementing enterprise-grade data protection through automation and surgical recovery methods. 

### **Task A: Automated Endpoint Migration**
* **Action:** Deployed a **PowerShell** script to migrate "Critical Project" data from local user desktops to the centralized `S: Drive`.

* **Impact:** Consolidated local data into a centralized server to ensure 100% backup coverage for all critical business files.

<img width="1021" height="766" alt="Script" src="https://github.com/user-attachments/assets/093bab95-0e5a-4bae-b1dd-ef5c7b97ff40" />

### **Task B: Legacy Application Risk Mitigation**

* **Action:** Identified vulnerable legacy accounting data and migrated it to a **Hidden Administrative Share** (`Secure_Archives$`).

* **Impact:** Reduced the attack surface by hiding the share from standard network discovery and enforcing `IT-Admin-only` access.

<img width="1020" height="769" alt="Legacy migration" src="https://github.com/user-attachments/assets/cf3c023e-da90-42fa-9777-56b4d8870500" />

### **Task C: Disaster Recovery (VSS)**

* **Action:** Configured **Volume Shadow Copy Service (VSS)** for point-in-time recovery.

* **Impact:** Enabled self-service **"Previous Version"** restoration, significantly reducing **Helpdesk RTO (Recovery Time Objective)** for accidental deletions.

<img width="1020" height="774" alt="Shadowcopies" src="https://github.com/user-attachments/assets/e7af4c88-86fe-4a4b-ae1e-fd2b17f341fe" />

<img width="1019" height="769" alt="self-service recovery" src="https://github.com/user-attachments/assets/52bda06a-b728-4675-b7d7-cd9591a0680b" />


## **Phase 7: Infrastructure Health & Validation**
**Objective:** Final validation of **Domain integrity** and **performance baselining**.

### **Active Directory Health Audit:**
Executed `dcdiag` to verify forest **connectivity**, **advertising**, and **replication** status. All tests passed.

<img width="1019" height="771" alt="dcdiag" src="https://github.com/user-attachments/assets/bc3fc8cf-e123-4b35-9e8d-4068fea5ba9c" />


### **Perfomance Monitoring:**
Utilized **Resource Monitor** to baseline network and `disk I/O`, ensuring no bottlenecks exist within the virtualized environment.

<img width="1019" height="766" alt="resmon" src="https://github.com/user-attachments/assets/dfbd871b-fd10-4502-93a2-39cfb43c306a" />


## **Phase 8: Monitoring with Splunk (SIEM Integration)**
The goal of this phase is to centralize all security logs into one place so I could monitor the entire lab from a single dashboard.

### **The Setup: Building the Data Bridge**
To get the logs from my **Windows 11** machine to the **Splunk Server**, I had to configure a **"Handshake"** between them:

#### **The Receiver:**
I configured the **Splunk Indexer** to listen on `Port 9997`. Think of this as opening a specific door on the server for security data to enter.

<img width="1021" height="767" alt="Screenshot 2026-04-28 220418" src="https://github.com/user-attachments/assets/03add844-d95a-478b-b044-74a6bb9def11" />

#### **The Agent & Connection:**
I installed the **Splunk Universal Forwarder** on the workstation. I then used the command line to verify the **"Handshake,"** confirming the agent was actively forwarding data to the server at `192.168.56.10:9997`.

<img width="1022" height="770" alt="Screenshot 2026-04-28 222349" src="https://github.com/user-attachments/assets/b761e3ea-2f8c-4bc6-9aee-407e8040b7d9" />


### **Security Alerts & Activity Catching** 
Once the bridge was built, I tested it by performing actions that look like "hacker" activity to see if **Splunk** would catch them:

#### **High-Privilege Login (Event 4672)**
I tracked when an account with **"Super User"** powers logged in. This is a red flag if it’s an account that shouldn't have **administrative access**.

* **Result:** Splunk caught the `"Attack_Account"` logging in with special system-level privileges.

<img width="1023" height="771" alt="Screenshot 2026-04-28 220635" src="https://github.com/user-attachments/assets/9966c8d3-e722-4e62-b6ce-20725b8c05a2" />

#### **PowerShell Command Logging (Event 4104)**
I enabled a setting that records exactly what is typed into the **PowerShell** window. This is perfect for seeing if someone is trying to run malicious scripts.

* **Result:** I created a simple table that clearly lists the **time**, the **computer name**, and the exact **command** that was run.

<img width="1020" height="772" alt="Screenshot 2026-04-28 225953" src="https://github.com/user-attachments/assets/e4687b41-e2ac-4e1a-abd2-143c80ee64a5" />

### **New Service Created (Event 7045)**
I monitored for the creation of new services, which is a common technique used by attackers to establish **"persistence"** allowing their malicious programs to run automatically in the background.

* **Result:** I captured a successful alert showing the exact name of the new service `(SplunkTestService)` and the file path it used `(C:\Windows\System32\cmd.exe)`, allowing me to identify unauthorized background installations.

<img width="1023" height="769" alt="Screenshot 2026-04-28 221455" src="https://github.com/user-attachments/assets/90e91f71-c032-4f0c-9689-d6840beac2aa" />


## **Phase 9: Patch Management & Compliance (WSUS)**
To transition the lab from an **"unmanaged"** environment to a governed enterprise network, I deployed **Windows Server Update Services (WSUS)** on the **Domain Controller**.

### **Policy-Driven Updates (GPO):**
Configured `Group Policy Objects` to redirect the workstation to the local update server. As shown in the client-side view, the "managed" status is verified by the system blocking manual user overrides.

<img width="1022" height="772" alt="Screenshot 2026-04-23 153748" src="https://github.com/user-attachments/assets/33dc9617-9d7d-49b7-9bd3-004eb0cd469c" />

### **Centralized Administration:** 
Utilized the WSUS console to organize assets into a dedicated `Lab_Workstations` group. Successfully synchronized **335 updates**, achieving a 99% patch compliance rate across the domain.

<img width="1019" height="773" alt="Screenshot 2026-04-23 154418" src="https://github.com/user-attachments/assets/c816b4c9-e744-47f7-bae9-cca8b415e7b2" />

### **Infrastructure Troubleshooting & Reporting:** 
Resolved a critical dependency issue involving legacy **SQL CLR** and **Report Viewer** runtimes on `Server 2022`. This enabled the generation of **"Computer Detailed Status Reports,"** providing actionable security audits for stakeholders.

<img width="1023" height="772" alt="Screenshot 2026-04-23 162445" src="https://github.com/user-attachments/assets/c0abd013-f72a-4125-b2b2-19513d58f73d" />

## **Phase 10: Remote Access & VPN Implementation**
**Objective:** Bridging the gap between the internal lab and the outside world. I implemented a secure entry point to allow remote users to "dial-in" and access corporate resources as if they were sitting in the office.

### **The Architecture: Secure Remote Connectivity** 
* **Service:** Configured **Routing and Remote Access Service (RRAS)** on the Domain Controller to act as a **Full-Tunnel VPN Gateway**. By routing all remote traffic through this secure perimeter, the organization ensures centralized monitoring and security auditing of all external web activity.

* **Authentication:** Leveraged **Active Directory (AD DS)** for native identity verification, ensuring that only users with explicit "Dial-in" permissions can establish a tunnel.

* **Identity-Based Access:** Used **Network Policy Server (NPS)** to validate user credentials against the forest, maintaining a unified security posture for both local and remote endpoints.


### **Validation & Proof of Concept**
#### **Identity Verification:** 
Configured specific **"Dial-in"** permissions for `Sarah Potter` in Active Directory, verifying that the VPN acts as a gatekeeper for corporate network resources.

<img width="1018" height="769" alt="Screenshot 2026-04-30 025012" src="https://github.com/user-attachments/assets/b0a4aaf7-0e74-4723-80a3-d7e00401c679" />

#### **Connectivity Audit:**
Utilized `ipconfig` to confirm a successful VPN handshake, resulting in the acquisition of a virtualized tunnel IP address (`10.0.0.102`).

<img width="1021" height="769" alt="Screenshot 2026-04-30 024948" src="https://github.com/user-attachments/assets/20ed754c-a6a5-4390-a92e-8945d3c5cff1" />

### **Resource Access:** 
Verified that the remote client successfully mapped the HR (`S:`) drive over the encrypted tunnel, confirming that GPO-based file share mapping remains functional outside the local network perimeter.

<img width="1021" height="767" alt="Screenshot 2026-04-30 015053" src="https://github.com/user-attachments/assets/809a504c-296c-4962-8a0f-f69429ee298c" />


## **Key Takeaways & Troubleshooting**
* **Issue:** Encountered a "Version Mismatch" where Windows 11 Home could not join a domain.
   *   **Solution:** Successfully upgraded the VM to Windows 11 Pro to enable enterprise features.

* **Issue:** Kerberos Time Skew. Encountered an error where the computer clock was not synchronized with the Domain Controller.
   *   **Solution**: Identified a VM clock drift mismatch and utilized `w32tm /resync` to align the client with the DC's NTP source
      * **Security:** Implemented Account Lockout Policies to mitigate brute-force attacks.
      
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

* **Issue:** Encountered routing conflicts when attempting to implement **Split Tunneling**.
   * **Root Cause:** A gateway conflict between the NAT (Internet) adapter and the Internal (LAN) adapter                        caused the VPN handshake to fail.

    * **Solution:** Pivoted to a **Full Tunnel** configuration. While this routes all traffic through the                       corporate gateway, it ensures that 100% of the remote client's traffic is subject to                        organizational security policies and monitoring, a preferred "Security First" approach.

* **Issue:** VPN client was unable to resolve Domain names after connecting.
   * **Solution:** Manually configured the VPN adapter's DNS settings to point directly to the DC’s                            internal IP (`10.0.0.1`), ensuring proper resolution of local resource names.

 
---

## **Future Enhancements**
To further expand this lab, I plan to implement:

