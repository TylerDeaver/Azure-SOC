# Building a Honeynet and SOC in Azure with Real-World Cyber Attacks
![Honeynet and SOC Overview](https://github.com/TylerDeaver/Azure-SOC/assets/149614301/ae193d44-fe1f-4204-b494-cab85c5ee980)


## Summary and Purpose

In this project, a honeynet and Security Operations Center (SOC) were constructed within Azure. The objective was to capture logs from several sources, analyze the logs, and consolidate them within a Log Analytics workspace. Microsoft Sentinel was deployed and configured to read the logs and generate attack maps, trigger alerts, and create incidents. After the honeynet was configured, the first deployment phase was initiated and an unsecured version of the environment was exposed to the internet for a 24-hour period. After the first deployment, several security controls were implemented to harden the environment. Once the environment was secure, the second deployment phase was initiated and the environment was once again exposed to the internet for a 24-hour period. The project concluded when the metrics from both 24-hour periods were logged and compared. 

The metrics analyzed were:
- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into the honeynet)

## Technologies, Azure Components, and Regulations Used
- Azure Virtual Network (VNet)
- Azure Network Security Groups (NSG)
- Virtual Machines (Windows VM and Linux VM)
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

This phase began with the deployment of the virtual environment and its subsequent exposure to the public internet with minimal security controls. The use of minimal security controls in this phase allowed malicious actors to discover the environment and attempt a variety of cyber attacks. The environment consisted of a Windows 10 Virtual Machine hosting a SQL database, a Linux Virtual Machine, a blob storage account, and a key vault. The purpose of having all of these components was to increase the attack surface and could monitor the different methods used by attackers. 

In order to guarantee the environment would be discoverable, the virtual machines had firewalls disabled and the network security groups (NSGs) were configured to allow all inbound traffic. The storage account and key vault were deployed with public endpoints visible to the internet. After the creation of the environment, Log Analytics workspace was set up for log aggregation and Microsoft Sentinel was in turn used for incident creation. Lastly four workbooks with custom queries were created in Microsoft Sentinel to plot the recorded malicious activity on a world map, with the purpose of providing a visual aid for the attacks. 

The results of these real-world attacks can be seen in the following section.

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

The following table shows the metrics measured within the unsecured environment over its 24-hour exposure to the internet:

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


The purpose of this phase was to harden the environment and implement security controls to ensure compliance with NIST SP 800-53 Revision 5 SC-7(3) Access Points. 

These hardening tactics included:
- Network Security Groups (NSGs)
  - NSGs were hardened by blocking all inbound and outbound traffic, with the exception of designated public IP addresses that required access to the virtual machines. This ensured only authorized traffic from trusted sources were allowed to access the virtual machines.

- Built-in Firewalls
  - Azure's built-in firewalls were configured on the virtual machines to restrict unauthorized access and protect the resources from malicious connections. This step involved fine-tuning the firewall rules based on the service and responsibilities of each virtual machine. This step significantly shrunk the attack surface of the environment.

- Private Endpoints
  - To enhance the security of Azure Key Vault and Storage Containers, the Public Endpoints were replaced with Private Endpoints. This ensured access to these sensitive resources was limited to the virtual network and not the public internet.

## Attack Maps Before Hardening / Security Controls

```All map queries returned no results. This is due to there being no instances of malicious activity for the 24-hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics measured within the secured environment over its 24-hour exposure to the internet:

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

In this project a small, but effective, honeynet was built in Microsoft Azure and Log Analytics workspace was used to ingest logs from the environment's resources. Microsoft Sentinel was then configured to trigger alerts and create incidents based on the ingested logs. Additionally, several metrics were recorded in order to quantitatively compare the effectiveness of hardening and security controls. The implementation of the hardening and security controls showed a 66% reduction in Windows Security Events, a 99% reduction in Linux Events, and a 100% reduction in security alerts, incidents, and malicious inbound network traffic. While the results show a significant improvement, the 100% reduction in three categories is largely due to not simulating regular user activity. In a real network, authorized users would likely generate security events and alerts to some degree, even if these triggers may be false positives.


## Reflection
This project taught me a lot and put a lot of my foundational knowledge into practice. Configuring the honeynet and implementing a small-scale SOC gave me valuable hands-on experience with a variety of tools. In the future, I plan to conduct this project again with the purpose of doing a deeper dive into Incident Response and implementing additional NIST 800-53 controls. I would also like to allow the environment for longer than 24 hours to observe changes in the attacks utilized by malicious actors.
