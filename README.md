# Building a Honeynet and SOC in Azure with Real-World Cyber Attacks
![Honeynet and SOC Overview](https://github.com/TylerDeaver/Azure-SOC/assets/149614301/ae193d44-fe1f-4204-b494-cab85c5ee980)


## Summary and Purpose

In this project, I built a honeynet and SOC in Azure. My goal was to capture and analyze logs from several sources and consolidate them within a Log Analytics workspace. I used Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. After configuring the honeynet, I allowed the unsecured environment to run for a 24-hour period. Following this phase, I implemented several security controls to harden the virtual environment. Once the environment was secure, I allowed it to run for another 24-hour period. The metrics from both 24-hours periods were logged and compared. 

The metrics I analyzed were:
- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious flows allowed into the honeynet)

## Technologies, Azure Components, and Regulations Used
- Azure Virtual Network (VNet)
- Azure Network Security Groups (NSG)
- Virtual Machines (2 Windows VMs, 1 Linux VM)
- Log Analytics Workspace with Kusto Query Language (KQL) Queries
- Azure Key Vault for Secure Secrets Management
- Azure Storage Account for Data Storage
- Microsoft Sentinel for Security Information and Event Management (SIEM)
- Microsoft Defender for Cloud to Protect Cloud Resources
- Windows Remote Desktop for Remote Access
- Command Line Interface (CLI) for System Management
- PowerShell for Automation and Configuration Management
- [NIST SP 800-53 Revision 5](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final) for Security Controls
- [NIST SP 800-61 Revision 2](https://www.nist.gov/privacy-framework/nist-sp-800-61) for Incident Handling Guidance

## Architecture Before Hardening / Security Controls
![Architecture Before](https://github.com/TylerDeaver/Azure-SOC/assets/149614301/935e08eb-845f-4f28-b47b-3e221d2b5d60)



The purpose of this phase was to monitor the attack patterns utilized by malicious actors in a "real-world" setting.

First, I deployed a virtual environment and exposed it to the public internet with minimal security controls, which allowed malicious actors to discover the environment and attempt a variety of cyber attacks. My environment consisted of a Windows 10 Virtual Machine hosting a SQL database, a Linux Virtual Machine, a blob storage account, and a key vault. The purpose of having all of these components was to increase my attack surface so I could monitor the different methods used by attackers. In this phase, the virtual machines had firewalls disabled and network security groups (NSGs) configured to allow all traffic. The other resources were deployed with public endpoints visible to the internet. After creating the environment, I utilized Log Analytics Workspace for log aggregation and Microsoft Sentinel for incident creation. Lastly, I created four workbooks in Microsoft Sentinel to plot malicious activity on a world map, providing a visual aid for the attacks. These results can be seen below.

## Attack Maps Before Hardening / Security Controls

<b>This attack map shows the attempts by malicious actors to brute force into the Windows Virtual Machine via Remote Desktop Protocol:</b>
![BEFORE windows-rdp-auth-fail](https://github.com/TylerDeaver/Azure-SOC/assets/149614301/73462eb0-22e4-472b-bf1e-e529e9e885ab)



<b>This attack map shows the attempts by malicious actors to brute force into the Linux Virtual Machine via Secure Shell:</b>
![BEFORE linux-ssh-auth-fail](https://github.com/TylerDeaver/Azure-SOC/assets/149614301/c0986de7-3c57-4af9-bd0b-92e92e62e6c1)



<b>This attack map shows the traffic allowed by Network Security Groups configured to allow all inbound traffic:</b>
![BEFORE nsg-malicious-allowed-in](https://github.com/TylerDeaver/Azure-SOC/assets/149614301/712cbc16-23f5-4a40-ab89-39c16c033adb)



<b>This attack map shows the attempts by malicious actors to brute force into the SQL Database:</b>
![BEFORE mssql-auth-fail](https://github.com/TylerDeaver/Azure-SOC/assets/149614301/24dd3e21-0ab0-408e-b9c5-842012f08cd2)


## Metrics Before Hardening / Security Controls

The following table shows the metrics measured in within the unsecured environment for 24 hours:

Start Time 11/7/2023 @ 3:12:57 PM

Stop Time 11/8/2023 @ 3:12:57 PM

| Metric                                                               | Count
| -------------------------------------------------------------------- | -----
| SecurityEvent (Windows Event Logs)                                   | 22456
| Syslog (Linux Event Logs)                                            | 1360
| SecurityAlert (Log Analytics Alerts Triggered)                       | 2
| SecurityIncident (Incidents created by Sentinel)                     | 153
| AzureNetworkAnalytics_CL (Malicious flows allowed into the honeynet) | 3600

## Architecture After Hardening / Security Controls
![Architecture After](https://github.com/TylerDeaver/Azure-SOC/assets/149614301/d0f77f64-fb28-4e64-a0c2-fc5fece96d5c)


The purpose of this phase was to harden the environment and implement security controls to become compliant with NIST SP 800-53 Revision 5 SC-7(3) Access Points. 

These hardening tactics included:
- Network Security Groups (NSGs)
  - NSGs were hardened by blocking all inbound and outbound traffic, with the exception of designated public IP addresses that required access to the virtual machines. This ensured only authorized traffic from trusted sources were allowed to access the virtual machines.

- Built-in Firewalls
  - Azure's built-in firewalls were configured on the virtual machines to restrict unauthorized access and protect the resources from malicious connections. This step involved fine-tuning the firewall rules based on the service and responsibilities of each virtual machine. This step significantly shrunk the attack surface of the environment.

- Private Endpoints
  - To enhance the security of Azure Key Vault and Storage Containers, I replaced the Public Endpoints with Private Endpoints. This ensured that access to these sensitive resources was limited to the virtual network and not the public internet.

## Attack Maps Before Hardening / Security Controls

```All map queries returned no results. This is due to there being no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics measured in within the secured environment for 24 hours:

Start Time 11/12/2023 @ 4:16:41 PM

Stop Time 11/13/2023 @ 4:16:41 PM

| Metric                                                               | Count
| -------------------------------------------------------------------- | -----
| SecurityEvent (Windows Event Logs)                                   | 7486
| Syslog (Linux Event Logs)                                            | 5
| SecurityAlert (Log Analytics Alerts Triggered)                       | 0
| SecurityIncident (Incidents created by Sentinel)                     | 0
| AzureNetworkAnalytics_CL (Malicious flows allowed into the honeynet) | 0

## Results from Hardening and Implementing Security Controls

The following table shows a quantitative comparison between the 24-hour period with the unsecured environment and the 24-hour period with the secured environment:

| Metric                                                               | Percentage Change
| -------------------------------------------------------------------- | -----
| SecurityEvent (Windows Event Logs)                                   | -66.66%
| Syslog (Linux Event Logs)                                            | -99.63%
| SecurityAlert (Log Analytics Alerts Triggered)                       | -100.00%
| SecurityIncident (Incidents created by Sentinel)                     | -100.00%
| AzureNetworkAnalytics_CL (Malicious flows allowed into the honeynet) | -100.00%


## Conclusion

In this project I created a mini, but effective, honeynet in Microsoft Azure and configured a Log Analytics workspace to ingest logs. I also used Microsoft Sentinel to trigger alerts and create incidents based on the ingested logs. Additionally, I measured several metrics in order to quantitatively compare the effectiveness of hardening and security controls. The result of hardening and security controls showed a 66% reduction in Windows Security Events, a 99% reduction in Linux Events, and a 100% reduction in security alerts, incidents, and malicious inbound network traffic. I would note the drastic decrease is largely due to not simulating regular user activity. In a real network, I would expect authorized users to generate security events and alerts, even though they may be false positives.

In the future, I plan to conduct this project again with the purpose of doing a deeper dive into Incident Response and implementing additiona NIST 800-53 controls. I would also like to allow the environment for longer than 24 hours to observe changes in the attacks utilizied by malicious actors.
