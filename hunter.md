# Sherlock Scenario
A SOC analyst received an alert about possible lateral movement and credential stuffing attacks. The alerts were not of high confidence and there was a chance of false positives as the SOC was newly deployed. 
Upon further analysis and network analysis by senior soc analyst it was confirmed that an attack took place. As part of incident response team you are assigned the incident ticket. The network capture device had some performance issues from some time so we unable to capture all traffic. 
You are provided with the Artifacts acquired from the endpoint and the limited network capture for analysis. Now it's your duty to conduct a deep dive with the provided data sources to understand how did the incident occured.

# Solution
## Task 1
What is the mitre technique ID of the tactic used by the attacker to gain initial access to the system?

Answer: `eM client`

## Task 2
When did attacker gain a foothold on the system? (UTC)

Answer: `eM client`

## Task 3
What's the SHA1 hash of the exe which gave remote access to the attacker?

Answer: `eM client`

## Task 4
When was whoami command executed on the system by the attacker? (UTC)

Answer: `eM client`

## Task 5
We believe the attacker performed enumeration after gaining a foothold. They likely discovered a PDF document containing RDP credentials for an administrator's workstation. 
We believe the attacker accessed the contents of the file and utilised them to gain access to the endpoint. Find a way to recover contents of the PDF file and confirm the password.

Answer: `eM client`

## Task 6
At what time did the adversary initially authenticate utilizing RDP? (UTC)

Answer: `eM client`

## Task 7
The security team have located numerous unusual PowerShell scripts on the host. We believe the adversary may have downloaded the tooling and renamed it to stay hidden. 
Please confirm the original name of the malicious PowerShell script utilised by the attacker.

Answer: `eM client`

## Task 8
We believe the attacker enumerated installed applications on the system and found an application of interest. We have seen some alerts for a tool named Process Hacker. Which application were they interested it?

Answer: `eM client`

## Task 9
What was the name of the initial dump file?

Answer: `eM client`

## Task 10
The attackers downloaded a custom batch script from their C2 server. What is the full C2 domain url from where it was downloaded?

Answer: `eM client`

## Task 11
Whats the MD5 hash of the batch script?

Answer: `eM client`

## Task 12
The attackers tried to exfiltrate the data to their FTP server but couldn't connect to it. The threat intelligence team wants you to collect more TTPs (Tactics, Techniques, and Procedures) and IOCs (Indicators of Compromise) related to the adversary. 
It would be really helpful for the TI team if you could provide some useful information regarding the attacker's infrastructure being used. Can you find the domain name and the password of their FTP server?

Answer: `eM client`

## Task 13
Upon failing their initial attempt to exfiltrate data, the SOC team observed further FTP data being sent to a cloud environment. 
It is believed that the attackers spun up an instance on the cloud and ran another FTP server hastily to exfiltrate the collected data. 
Please try to find more information regarding the adversary's infrastructure, so the Threat Intel team can better understand which group might be behind this attack. What is the remote path on the adversary's server where they stored the exfiltrated data?

Answer: `eM client`

## Task 14
For how long did the tool used for exfiltrating data, run before being closed? (Answer in seconds)

Answer: `eM client`

## Task 15
The security team highlighted that information pertaining to a sensitive project may have been exfiltrated by the attackers and are now worried about the threat of extortion. Which directory did the attacker manage to stage and then exfiltrate?

Answer: `eM client`

## Task 16
What specific CVE did the attacker exploit to gain access to the sensitive contents?

Answer: `eM client`

## Task 17
Find a way to access the sensitive information. The information was related to development of an internal application. What is the suggested name for this app?

Answer: `eM client`

## Task 18
SSN were also part of the sensitive project which was exfiltrated by the attacker. What is the SSN number of Arthur Morgan from zeeindustries?

Answer: `eM client`

## Task 19
We believe the domain admin credentials have been leaked in this incident. Please confirm the Domain Admin password?

Answer: `eM client`
