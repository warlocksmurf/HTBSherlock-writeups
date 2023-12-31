# Sherlock Scenario
Oh no! Our IT admin is a bit of a cotton-headed ninny-muggins, ByteSparkle left his VPN configuration file in our fancy private S3 location! The nasty attackers may have gained access to our internal network. We think they compromised one of our TinkerTech workstations. Our security team has managed to grab you a memory dump - please analyse it and answer the questions! Santa is waiting…

# Solution
## Task 1 
Question: What is the name of the file that is likely copied from the shared folder (including the file extension)?
<br>Answer: `present_for_santa.zip`

Reading the scenario, I tried scanning the files in the memory dump with grep santa. A suspicious zip file can be located in santa claus's Desktop.
```
python3 vol.py -f /mnt/hgfs/shared/HTB/Optinseltrace/optinseltrace3/santaclaus.bin windows.filescan | grep "santa"
```

![vol1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/c23ab528-f609-4a33-823f-b068b74895cb)

## Task 2 
Question: What is the file name used to trigger the attack (including the file extension)?
<br>Answer: `click_for_present.lnk`

By dumping and extracting the zip file, we can find a lnk file and a vbs file.
```
python3 vol.py -f /mnt/hgfs/shared/HTB/Optinseltrace/optinseltrace3/santaclaus.bin windows.dumpfiles --virtaddr 0xa48df8fb42a0
```

![vol2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/f9a3dade-1ca9-4092-b706-704adb4c1f3c)

## Task 3 
Question: What is the name of the file executed by click_for_present.lnk (including the file extension)?
<br>Answer: `present.vbs`

Check the metadata of the lnk file.

![vol3](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/29d8688d-8c0b-4b84-99e0-d17812179f3d)

## Task 4 
Question: What is the name of the program used by the vbs script to execute the next stage?
<br>Answer: `powershell.exe`

Using [VirusTotal](https://www.virustotal.com/gui/file/78ba1ea3ac992391010f23b346eedee69c383bc3fd2d3a125ede6cba3ce77243/behavior), we can easily find the processes of this vbs script.

![vol4](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/5586e600-cbf8-4606-b503-0955c8de4e92)

## Task 5 
Question: What is the name of the function used for the powershell script obfuscation?
<br>Answer: `WrapPresent`

Using [VirusTotal](https://www.virustotal.com/gui/file/78ba1ea3ac992391010f23b346eedee69c383bc3fd2d3a125ede6cba3ce77243/behavior), we can easily find the fuction.

![vol5](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/0610eae6-e86d-47c9-a0a4-411b3f1ac6fa)

## Task 6
Question: What is the URL that the next stage was downloaded from?
<br>Answer: `http://77.74.198.52/destroy_christmas/evil_present.jpg`

Using [VirusTotal](https://www.virustotal.com/gui/file/78ba1ea3ac992391010f23b346eedee69c383bc3fd2d3a125ede6cba3ce77243/behavior), we can easily find the HTTP request.

![vol6](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/57a16134-2c24-4673-a358-22e24d588f74)

## Task 7
Question: What is the IP and port that the executable downloaded the shellcode from (IP:Port)?
<br>Answer: `77.74.198.52:445`

Using [VirusTotal](https://www.virustotal.com/gui/file/31ef280a565a53f1432a1292f3d3850066c0ae8af18a4824e59ac6be3aa6ea9c/behavior), we can easily find the HTTP request.

## Task 8
Question: What is the process ID of the remote process that the shellcode was injected into?
<br>Answer: `724`

Since we know what IP address is associated with the vbs script, we can use netstat to discover its process.
```
python3 vol.py -f /mnt/hgfs/shared/HTB/Optinseltrace/optinseltrace3/santaclaus.bin windows.netstat
```

![vol7](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/6a6915aa-6f9f-4284-bd68-bc25dfbf837d)

## Task 9
Question: After the attacker established a Command & Control connection, what command did they use to clear all event logs?
<br>Answer: `Get-EventLog -List | ForEach-Object { Clear-EventLog -LogName $_.Log }`

At this point, I was stuck and required hints from @tmechen on HTB Discord. He gave a hint on evtx logs and hence I dumped the Powershell evtx log since the script runs on powershell.
Task 9-13 can be solved with the evtx log.

```
python3 vol.py -f /mnt/hgfs/shared/HTB/Optinseltrace/optinseltrace3/santaclaus.bin windows.filescan | grep "evtx"
```

![vol8](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/997a7aae-f953-400f-83d4-34f0e8c104d1)

## Task 10
Question: What is the full path of the folder that was excluded from defender?
<br>Answer: `C:\users\public`

![vol9](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/3f393955-7268-4542-ad79-7e5aed5cdce5)

## Task 11
Question: What is the original name of the file that was ingressed to the victim?
<br>Answer: `procdump.exe`

Since the question wants the original name, the PresentForNaughtyChild.exe file has to be extracted via Volatility first before analyzing it on [VirusTotal](https://www.virustotal.com/gui/file/337c24c2e6016a9bdca30f2820df9c1dae7b827ad73c93a14e1dc78906b63890).

![vol10](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/668649dc-da33-4e1c-8e4c-55d5d88b677f)
![vol11](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/2ff6fc76-efc0-4414-9182-336c8408e026)

## Task 12
Question: What is the name of the process targeted by procdump.exe?
<br>Answer: `lsass.exe`

The powershell command from Task 11 shows the targeted process.
