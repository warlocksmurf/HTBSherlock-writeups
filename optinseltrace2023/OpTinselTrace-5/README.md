# Sherlock Scenario
You'll notice a lot of our critical server infrastructure was recently transferred from the domain of our MSSP - Forela.local over to Northpole.local. We actually managed to purchase some second hand servers from the MSSP who have confirmed they are as secure as Christmas is! It seems not as we believe christmas is doomed and the attackers seemed to have the stealth of a clattering sleigh bell, or they didn’t want to hide at all!!!!!! We have found nasty notes from the Grinch on all of our TinkerTech workstations and servers! Christmas seems doomed. Please help us recover from whoever committed this naughty attack!

# Solution
## Task 1 
Question: Which CVE did the Threat Actor (TA) initially exploit to gain access to DC01?
<br>Answer: `CVE-2020-1472`

Using Hayabusa, the evtx directory can be scanned to determine the vulnerability using rules.

```
.\hayabusa-2.11.0-win-x64.exe csv-timeline -d 'C:\HTB\Optinseltrace\optinseltrace5\DC01.northpole.local-KAPE\uploads\auto\C%3A\Windows\System32\winevt' -o results.csv
```

![win1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/6122cd2d-9ea2-4987-9111-6e23fbb4cd42)

![win2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/0ebb55bd-a1e0-4838-aff4-14f41c07d7f6)

## Task 2 
Question: What time did the TA initially exploit the CVE? (UTC)
<br>Answer: `2023-12-13 09:24:23`

Reading this [blog](https://0xbandar.medium.com/detecting-the-cve-2020-1472-zerologon-attacks-6f6ec0730a9e) about the CVE, it mentioned an easy way to detect the exploit using the Security.evtx.

> Successful exploitation resulting in a rest of the computer account password and it will be shown in security logs with event id 4742 “A computer account was changed”, password last set change, performed by Anonymous Logon.


![win2 1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/8307d503-2500-485b-9f84-9f20da3d1fed)

## Task 3
Question: What is the name of the executable related to the unusual service installed on the system around the time of the CVE exploitation?
<br>Answer: `hAvbdksT.exe`

Since we know the timeline of compromise in Task 2, the executable related to the "vulnerable_to_zerologon" service can be easily found in the System.evtx by filtering the time and eventID.

![win3](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/e54e82e9-45ae-4e87-b078-7484cfeb94d0)

## Task 4 
Question: What date & time was the unusual service start?
<br>Answer: `2023-12-13 09:24:24`

Similar to Task 3, just look for "vulnerable_to_zerologon" service starting.

![win4](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/6dc67f43-deb9-487f-bdd1-4f8b53488d6b)

## Task 5 
Question: What was the TA's IP address within our internal network?
<br>Answer: `192.168.68.200`

Using Hayabusa, we can find out the logon attempts and since we know there ANONYMOUS LOGON was one of them from Task 2, we can easily see the IP address.

```
.\hayabusa-2.11.0-win-x64.exe logon-summary -d 'C:\Users\ooiro\Documents\shared\HTB\Optinseltrace\optinseltrace5\DC01.northpole.local-KAPE\uploads\auto\C%3A\Windows\System32\winevt\Logs'
```

![win5](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/354b98c3-4f46-4448-b0e3-254c443e2244)

## Task 6 
Question: Please list all user accounts the TA utilised during their access. (Ascending order)
<br>Answer: `Administrator, Bytesparkle`

Using Hayabusa, we can find out which account was compromised using the IP address from Task 5.

![win6](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/676b3958-cdf9-4c7e-9a14-0a66b0fd443a)

## Task 7 
Question: What was the name of the scheduled task created by the TA?
<br>Answer: `svc_vnc`

Just look for the Microsoft-Windows-TaskScheduler%254Operational.evtx log file and filter by eventID 106 (Task registered). Two schedule tasks was created and one of them was the answer.

![win7](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/f517c46e-1397-4c9d-9741-706dc1b7a367)

## Task 8 
Question: Santa's memory is a little bad recently! He tends to write a lot of stuff down, but all our critical files have been encrypted! Which creature is Santa's new sleigh design planning to use?
<br>Answer: `Unicorn`

Initially, we were given several encrypted files and the encryption program (splunk_svc.dll). So it is obvious we have to perform reverse engineering on splunk_svc.dll to decrypt the files. Using ghidra, I managed to find the malware function that encrypted the files (FUN_180001330). 

Analyzing the function, the encryption method was discovered to be a simple XOR with the key "EncryptingC4Fun!". So we can use CyberChef to decrypt the files easily and discover Santa' plan.

![win8](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/a665d12f-a6a4-46cd-8463-bf0f89f06fe7)

![win8 1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/fbb5dac6-a162-421f-891b-63dcfd1c6267)

![win8 2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/4137037d-0797-4c1e-919b-7be3bded88b2)

## Task 9 
Question: Please confirm the process ID of the process that encrypted our files.
<br>Answer: `5828`

Using EVTXCmd, we can search for the file extension of encrypted files (.xmax). We discover that they were stored in the Microsoft-Windows-UAC-FileVirtualization/Operational channel. Hence, we can analyze the Microsoft-Windows-UAC-FileVirtualization/Operational.evtx file to obtain the process ID.

![win9](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/c1c564be-85e9-4850-9c1b-34527c3094d2)

![win9 1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/7c5b26f8-53db-4cef-af70-f228c2ccd7c7)


