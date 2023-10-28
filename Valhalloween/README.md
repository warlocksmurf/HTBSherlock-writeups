# Explanation
We are given the following file:
* `Logs`: Directory containing various Windows XML EventLog (.evtx) files

In this challenge, we are given a series of questions that must be answered to obtain the flag. These answers can all be located in certain event log files provided by HTB, hence we selected the most important ones for further investigation.

<p align='center'>
  <img src='/images/win1.png' alt="VirusTotal">
</p>

# Solution
1. What are the IP address and port of the server from which the malicious actors downloaded the ransomware? (for example: 98.76.54.32:443)
* Answer: `103.162.14.116:8888`

To complete this question, we can analyze the `Security` log file and filter the logs with event ID 4688 which is normally logged in Event Viewer when a new process is created. After filtering the results, we find a Powershell script that was executed to download the ransomware ('mscalc.exe') from a malicious server with its IP address and port. Additionally, we now know the estimated time of the ransomware attack is around 11:03:24 AM on 20/9/2023.

<p align='center'>
  <img src='/images/win2.png' alt="VirusTotal">
</p>

2. According to the sysmon logs, what is the MD5 hash of the ransomware? (for example: 6ab0e507bcc2fad463959aa8be2d782f)
* Answer: `B94F3FF666D9781CB69088658CD53772`

To complete this question, we can analyze the `sysmon` log file and filter the logs with event ID 1 which is normally logged in Event Viewer when a new process is created. After filtering the results and since we know the Powershell script downloads the ransomware, we can attempt to find its child processes to locate the creation process of the ransomware. After analyzing the logs, we can find the ransomware with its MD5 hash.

<p align='center'>
  <img src='/images/win3.png' alt="VirusTotal">
</p>

3. Based on the hash found, determine the family label of the ransomware in the wild from online reports such as Virus Total, Hybrid Analysis, etc. (for example: wannacry)
* Answer: `lokilocker`

To complete this question, just put the ransomware's MD5 hash to any OSINT tool and check its family labels.

<p align='center'>
  <img src='/images/win4.png' alt="VirusTotal">
</p>

4. What is the name of the task scheduled by the ransomware? (for example: WindowsUpdater)
* Answer: `Loki`

To complete this question, we can analyze the `sysmon` log file and filter the logs with keyword 'schtasks' which is the name for task scheduling process. After filtering the results, we find a schtasks program with the parent process being the ransomware.

<p align='center'>
  <img src='/images/win5.png' alt="VirusTotal">
</p>

5. What are the parent process name and ID of the ransomware process? (for example: svchost.exe_4953)
* Answer: `powershell.exe_3856`

To complete this question, we can analyze the `sysmon` log file and check the ransomware process again. Viewing the XML format of the ransomware process, we can easily find the parent process name and ID of the ransomware process.

<p align='center'>
  <img src='/images/win6.png' alt="VirusTotal">
</p>

6. Following the PPID, provide the file path of the initial stage in the infection chain. (for example: D:\Data\KCorp\FirstStage.pdf)
* Answer: `C:\Users\HoaGay\Documents\Subjects\Unexpe.docx`

To complete this question, we can analyze the `Security` log file again and check the Powershell script process again. As the question suggests, we can use the PPID to retrace the steps to the initial stage in the infection chain. As shown in the pictures below, we then stumble upon a malicious `.docx` file

<p align='center'>
  <img src='/images/win7.png' alt="VirusTotal">
</p>

<p align='center'>
  <img src='/images/win8.png' alt="VirusTotal">
</p>

<p align='center'>
  <img src='/images/win9.png' alt="VirusTotal">
</p>

7. When was the first file in the infection chain opened (in UTC)? (for example: 1975-04-30_12:34:56)
* Answer: `2023-09-20_03:03:20`

To complete this question, we can just view the XML format of the `.docx` file and find the `TimeCreated SystemTime` row. ENSURE THE TIME FORMAT IS IN UTC!

<p align='center'>
  <img src='/images/win10.png' alt="VirusTotal">
</p>
