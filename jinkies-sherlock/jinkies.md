# Sherlock Scenario
You're a third-party IR consultant and your manager has just forwarded you a case from a small-sized startup named cloud-guru-management ltd. They're currently building out a product with their team of developers, but the CEO has received word of mouth communications that their Intellectual Property has been stolen and is in use elsewhere. The user in question says she may have accidentally shared her Documents folder and they have stated they think the attack happened on the 6th of October. The user also states she was away from her computer on this day. There is not a great deal more information from the company besides this. An investigation was initiated into the root cause of this potential theft from Cloud-guru; however, the team has failed to discover the cause of the leak. They have gathered some preliminary evidence for you to go via a KAPE triage. It's up to you to discover the story of how this all came to be. Warning : This sherlock requires an element of OSINT and players will need to interact with 3rd party services on internet.

We are given a KAPE triange data of a compromised machine.

## Task 1 
Question: Which folders were shared on the host? (Please give your answer comma separated, like this: c:\program files\share1, D:\folder\share2)

Answer: `C:\Users\Velma\Documents, C:\Users`

Doing some research on shared folders, I stumbled upon this [Microsoft article](https://learn.microsoft.com/en-us/troubleshoot/windows-client/networking/saving-restoring-existing-windows-shares) on finding existing Windows shares. The article mentioned using the registry key `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\Shares` to find what folders were shared among users. After extracting SYSTEM from `\Jinkies_KAPE_output\TriageData\C\Windows\system32\config`, we can analyze it via Registry Explorer. The key values will have the name of the two shared folders.

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/2de754db-e596-44d8-9b64-24a05fceb721)

## Task 2
Question: What was the file that gave the attacker access to the users account?

Answer: `bk_db.ibd`

Since the shared folders are identified, we can analyze them to find suspicious files or executables. Using $MFT, we can filter paths of the shared folders and find a suspicious .ibd file in the `Documents`.

![Task 2,3,5 Credential file](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/c872fe34-4289-4970-883a-ac9f1fd40512)

## Task 3
Question: How many user credentials were found in the file?

Answer: `216`

Checking out the .ibd file, it seems that it is a MySQL table created by the InnoDB database engine. The database seems to store the credentials weirdly as `password,email,name,user`.

```
└─$ strings bk_db.ibd           
infimum
supremum
aaron
judith
infimum
supremum
Qaaron
@Ec&Gx2ce4b#aaron.Johnson@gmail.comTerriTerri Wallace
Yabigail
IH%3Io9nba)5abigail.Alexander@gmail.cLindsayLindsay Gillesp
Lalan
T@Vlu!4toa0Zalan.Jones@gmail.comDianeDiane Chaney
Nalex
F(06YTlbyi^valex.Brown@gmail.comVanessaVanessa Lang
Walexander
$amHVu))2B7aalexander.Hines@gmail.comAlyssaAlyssa Bryan
Palexis
#V&XfqwNh23Ealexis.Andrade@gmail.comJesusJesus Mays
Xallison
$GD4U@l1v)H%allison.Potts@gmail.comReginaldReginald Mitche
Uamanda
27+MkKYoYaP3amanda.Hughes@gmail.comMelindaMelinda Suarez
Mamber
%MWaRCbX4YQ4amber.Yates@gmail.comMeganMegan Smith
Mamy
...
```

Using the output of the strings command previously, I made a regex (with some ChatGPT) to find the amount of credentials stored.

```
└─$ grep -Pow '\b[\w.%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}\b' credentials.txt | sed -E 's/.*@//' | grep -c '[[:alpha:]]\{1,\}'
216
```

## Task 4
Question: What is the NT hash of the users password?

Answer: `967452709ae89eaeef4e2c951c3882ce`

This one is pretty straightforward, NT hash can be found in SAM databases, so just use tools like `Impacket` or `Mimikatz` to crack it.

```
└─$ impacket-secretsdump -sam "/Jinkies_KAPE_output/TriageData/C/Windows/system32/config/SAM" -system "/Jinkies_KAPE_output/TriageData/C/Windows/system32/config/SYSTEM" LOCAL
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Target system bootKey: 0x0fcab1a9630872ac6f23b1d98a3d9ed6
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:b47a9f2da3e6d7b88213822b52232627:::
test:1001:aad3b435b51404eeaad3b435b51404ee:3dbde697d71690a769204beb12283678:::
Velma:1002:aad3b435b51404eeaad3b435b51404ee:967452709ae89eaeef4e2c951c3882ce:::
[*] Cleaning up...
```

## Task 5
Question: Does this password match that found in the previous file? (Yes or No)

Answer: `Yes`

Just find Velma's password in the .idb file and convert it to NT hash.

```
└─$ cat credentials.txt | grep velma -A 5 -B 5  
!&l5X@FykY9Rtrevor.Pratt@gmail.comVincentVincent Marquez
Vtyler
ff3ptOzm!Z05tyler.Richardson@gmail.coNatalieNatalie Wilson
Uvalerie
q10CSKAb&MXlvalerie.Coleman@gmail.comLynnLynn Dominguez
Tvelma
peakTwins2023fcvelma.dinkley@gmail.comvelmavelma dinkley
Vvickie
$$YVca@Ip9*Fvickie.Wise@gmail.comFranciscoFrancisco Hudso
Svictor
YyBgN4f9^4Zavictor.Soto@gmail.comMaureenMaureen Turner
Uvincent
```

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/18e0bc79-8e59-42f7-bd2a-2a33b3c74a2f)

## Task 6
Question: What was the time the attacker first interactively logged on to our users host?

Answer: `2023-10-06 17:17:23`

Reading the question, it seems that I can finally start analyzing the event logs to find user logons using the Security evtx log file. Reading online on logon types, I found this [blog](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4624) that mentioned type 3 being related to `Network (i.e. connection to shared folder on this computer from elsewhere on network)`. Hence, I filtered the event log using Event ID `4624` and `Logon Type 3`. Out of the three events, only one had a logon attempt on Velma.

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/6ee10da5-b6fd-41c6-aeab-062b4b2d72b3)

## Task 7
Question: What's the first command the attacker issues into the Command Line?

Answer: `whoami`

I was stuck with this question for awhile, but then I noticed the triange data provided us sysmon logs that can be super beneficial in the investigation. So I made my search easier by filtering the event log using the word `cmd` and the time when the attacker logged on as Velma.

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/25bec6ec-bf56-4826-802d-53fade5ec98e)

Looking at the second event, it seems that the attacker ran the simple command `whoami`.

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/465e5153-df52-4b40-bf92-0c5f13d8714b)

## Task 8
Question: What is the name of the file that the attacker steals?

Answer: `Version-1.0.1 - TERMINAL LOGIN.py`

Going through the sysmon logs, it seems that the attacker stole a python script located in `C:\Users\Velma\Desktop\cloud-gurustuff\official guru terminal aws script\`

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/ced6f01b-3c9e-4697-b9cf-9937b15a0f0f)

## Task 9
Question: What's the domain name of the location the attacker ex-filtrated the file to?

Answer: `pastes.io`

Initially, I thought the attacker connected to a C2 server so I spent hours looking for network information. However, there were no signs of C2 server connections. After spending some time browsing artifacts, I found suspicious browser history in his Chrome browser. It seems that `pastes.io` was indeed the platform used to leak the pythons script he stole.

![Task 9 Pastes Google](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/43295246-6648-4121-9367-95a975747540)

## Task 10
Question: What is the link to the stolen file?

Answer: `https://pastes.io/tqrzylp1aa`

Exploring `pastes.io`, I noticed a page called `Archive` where several scripts of different programming languages are stored in. Navigating to the Python section, I followed the timeline of events (around October 2023) and found the file that was leaked by the attacker: `leaked docs cloud guru mgmt`.

![Task 10 Pastes Link](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/c87208d6-4f14-4712-96e4-c19268f15fe8)

## Task 11
Question: What is the handle of the attacker?

Answer: `pwnmaster12`

This question was pretty vague where I had to ask certain HTB players about what I should be doing. @tmechen mentioned that the attacker actually left a note for Velma after exfiltrating the data, so I assumed the note must be recovered to analyze its content. First I had to identify the note, going through the sysmon logs again (without cmd filter), I found out that the note must be called `learn.txt` located in `C:\Users\Velma\Pictures\`.

![Handle1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/c54e4269-943a-4b6a-b8fe-90f365ebc9a2)

So I went on to use `MFTEcmd` to carve out the contents of this note using its offset that can be obtained in the MFT. Inside the note, the attacker's handle can be obtained.

![Task 12 Handle2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/6718bfb5-441f-4c32-984d-bc942cd29df5)

![Task 11 Handle](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/5e625d17-9109-45d3-9a71-86a993570c17)

![Task 11 Handle3](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/fe4312dc-9dc2-4607-a50c-2e675bf7cf05)

