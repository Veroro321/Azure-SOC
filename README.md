# Building a SOC + Honeynet in Azure

## Introduction

In this project, I designed and implemented a mini honeynet infrastructure on the Azure platform, incorporating various log sources from different resources into a Log Analytics workspace. The resulting data was utilized by Microsoft Sentinel to construct attack maps, generate alerts, and create incidents. To evaluate the effectiveness of the implemented security measures, I collected security metrics in an unsecured environment for a duration of 24 hours. I then implemented specific security controls to harden the environment and measured the security metrics for an additional 24 hours. 

## Security Metrics Gathered

<ul>
  <li>SecurityEvent (Windows Event Logs)</li>
  <hr>
  <li>Syslog (Linux Event Logs)</li>
  <hr>
  <li>SecurityAlert (Log Analytics Alerts Triggered)</li>
  <hr>
  <li>SecurityIncident (Incidents created by Sentinel)</li>
  <hr>
  <li>AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)</li>
</ul>

![Azure](https://user-images.githubusercontent.com/130959114/232627898-afc073e9-d20c-4d2f-982c-ad01773fb2c5.jpg)

The results of this analysis are presented below.

## Architecture Before Hardening / Security Controls
![Untitled Diagram](https://user-images.githubusercontent.com/130959114/232668012-a8bd76b7-6a87-40e4-9885-8fd182473c11.jpg)

For the "BEFORE" metrics, all resources were initially deployed with unrestricted access to the internet. Specifically, the Virtual Machines had both their Network Security Groups and built-in firewalls configured with wide-open settings. Additionally, all other resources were deployed with publicly visible endpoints, rendering the use of Private Endpoints unnecessary.


## Architecture After Hardening / Security Controls
![Untitled Diagram drawio](https://user-images.githubusercontent.com/130959114/232681984-6a0555cb-90ca-45d0-8158-caed43232858.png)


The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

## Attack Maps Before Hardening / Security Controls
![Bnsg-malicious-allowed-in](https://user-images.githubusercontent.com/130959114/233216943-67edd823-93b7-4a55-8c4a-fd463c5957b3.png)

![Bsyslog-ssh-auth-fail](https://user-images.githubusercontent.com/130959114/233217429-5546009a-2bfc-44aa-942b-18941ea514ff.png)

![Bwindows-rdp-auth-fail](https://user-images.githubusercontent.com/130959114/233217188-2e958b6b-c3ee-472a-abcb-791afe860234.png)

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time	4/18/2023, 4:15:32 PM
Stop Time	4/19/2023, 4:15:32 PM

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 711
| Syslog                   | 79
| SecurityAlert            | 9
| SecurityIncident         | 161
| AzureNetworkAnalytics_CL | 48

BEFORE SECURING ENVIRONMENT	


## Attack Maps After Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

For the "AFTER" metrics, Network Security Groups were strengthened via implementing strict access controls that blocked all traffic, except for the one originating from my administrative workstation. Furthermore, all other resources were protected by their built-in firewalls, and Private Endpoints were utilized to secure communications between resources.

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-03-18 15:37
Stop Time	2023-03-19 15:37

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 8778
| Syslog                   | 25
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Simulated attacks

Simulated attacks were generated via PowerShell scripts or by manually triggering events.
* Brute Force Success (Windows)
* Linux Brute Force Attempt
* Privilege Escalation
* Windows Brute Force Success
* Malware Detection 

## Utilizing NSIT 800-61 Incident Controls 

![image](https://user-images.githubusercontent.com/130959114/233717915-4cff8ac7-e8ce-45b8-b06c-fcddf7b42cfe.png)

- Step 1: Preparation
Initiated by ingesting all of the logs into Log Analytics Workspace and Sentinel and configuring alert rules

- Step 2: Detection & Analysis 
Set Severity, Status, Owner
View Full Details 
Observe the Activity Log 
Observe Entities and Incident Timelines 
“Investigate” the incident and continue trying to determine the scope
Inspect the entities and see if there are any related events
Determine legitimacy of the incident (True Positive, False Positive, etc.)
If True Positive, continue, if False positive, close it out

- Step 3: Containment, Eradication, and Recovery
Use the simple Incident Response PlayBook
Brute Force Success (Windows)
Linux Brute Force Attempt
Privilege Escalation 
Windows Brute Force Success
Malware Detection 

- Step 4: Document Findings/Info and Close out the Incident in Sentinel
e.g., Brute Force attack: Upon reviewing the Sentinel dashboard, it was discovered that multiple failed login attempts had been made from a single IP address. Further investigation revealed that targeted user account has a potentially compromised system "windows-vm" due to possible overexposure to the internet.  

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastially reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
