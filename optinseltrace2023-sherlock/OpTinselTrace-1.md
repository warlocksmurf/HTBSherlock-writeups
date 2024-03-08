# Sherlock Scenario
An elf named "Elfin" has been acting rather suspiciously lately. He's been working at odd hours and seems to be bypassing some of Santa's security protocols. 
Santa's network of intelligence elves has told Santa that the Grinch got a little bit too tipsy on egg nog and made mention of an insider elf! 
Santa is very busy with his naughty and nice list, so he’s put you in charge of figuring this one out. Please audit Elfin’s workstation and email communications.

## Task 1 
Question: What is the name of the email client that Elfin is using?
<br>Answer: `eM client`

Since the question is asking for Elfin's email client, it is probably a program downloaded in his machine. Hence, I went ahead to `\optinseltrace1\TriageData\C\users\Elfin\Appdata` and looked around both the Local and Roaming directory. In the Roaming directory, eM client was found.

![elf1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/e5c6d693-0ff3-4c2c-baba-a171661c3781)

## Task 2  
Question: What is the email the threat is using?
<br>Answer: `definitelynotthegrinch@gmail.com`

One easy way to solve this whole Sherlock is to just utilize the email client to search for clues about Elfin and the TA.
After importing the configuration files, we can see the Grinch sending an email to Elfin!

![elf2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/6caa0bd8-5b3f-4f0f-9469-95befbb47208)

## Task 3 
Question: When does the threat actor reach out to Elfin?
<br>Answer: `2023-11-27 17:27:26`

Same answer from Task 2, just open up the replies (make sure the time is converted to UTC).

![elf3](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/d25e4cc9-6ff3-4da9-9a1d-0c9e5ca04bda)

## Task 4 
Question: What is the name of Elfins boss?
<br>Answer: `elfuttin bigelf`

Pretty straightforward, just look through the emails.

![elf4](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/6ec4bac0-1d22-46ac-afc8-3f702a62a5c1)

## Task 5 
Question: What is the title of the email in which Elfin first mentions his access to Santas special files?
<br>Answer: `Re: work`

Pretty straightforward, just look through the emails and focus on the subject line.

![elf5](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/23ac4ce4-cbc9-44eb-bde2-7cf41a50597c)

## Task 6 
Question: The threat actor changes their name, what is the new name + the date of the first email Elfin receives with it?
<br>Answer: `wendy elflower, 2023-11-28 10:00:21`

Look through the emails for suspicious users and open up the replies (make sure the time is converted to UTC).

![elf6](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/323d2e59-0b8f-4060-b8ef-35afd75f9a78)

## Task 7
Question: What is the name of the bar that Elfin offers to meet the threat actor at?
<br>Answer: `SnowGlobe`

Pretty straightforward, just look through the emails from Elfin and wendy elflower.

![elf7](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/efd3097c-2dd3-4fb5-b1d8-4fda193d1a6c)

## Task 8
Question: When does Elfin offer to send the secret files to the actor?
<br>Answer: `2023-11-28 16:56:13`

Pretty straightforward, just look through the emails from Elfin and wendy elflower (make sure the time is converted to UTC).

![elf8](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/4c464425-3d39-4ee4-9174-2975c2c268a6)

## Task 9
Question: What is the search string for the first suspicious google search from Elfin? (Format: string)
<br>Answer: `how to get around work security`

Since the question asked about Google search, I assume we have to analyze the Elfin's browser history on Google. So I extracted several Chrome artifacts located in `\optinseltrace1\TriageData\C\users\Elfin\Appdata\Local\Google\Chrome\User Data\Default\` and analyzed them using DB Browser. There were several suspicious search results found in the `History` file.

![elf9](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/310bac04-9440-41d5-88f3-57cc8ca9568c)

## Task 10
Question: What is the name of the author who wrote the article from the CIA field manual?
<br>Answer: `Joost Minnaar`

Similar to Task 9, you can find the article search results.

![elf10](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/a77cb620-31ea-4402-903d-b2d2df597fe8)
![elf10 1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/6e956313-279f-4e3b-8f78-2025d5d4fee8)

## Task 11
Question: What is the name of Santas secret file that Elfin sent to the actor?
<br>Answer: `santa_deliveries.zip`

The secret file can be found in Elfin's machine located in `\optinseltrace1\TriageData\C\users\Elfin\Appdata\Roaming\top-secret\`

## Task 12
Question: According to the filesystem, what is the exact CreationTime of the secret file on Elfins host?
<br>Answer: `2023-11-28 17:01:29`

One way to find the exact 'CreationTime' is to use the $MFT artifact. This can be done using MFTECmd and Timeline Explorer.

![elf12](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/ffe00ae6-7e0b-4fe5-b87d-fc6846ff5797)

## Task 13
Question: What is the full directory name that Elfin stored the file in?
<br>Answer: `C:\users\Elfin\Appdata\Roaming\top-secret`

Similar to Task 11.

## Task 14
Question: What is the name of the bar that Elfin offers to meet the threat actor at?
<br>Answer: `Greece`

Similarly, you can find suspicious search results about flying to Greece from North Pole on Google Chrome.

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/f51eb564-1125-4591-9e94-d27b03dcef27)

## Task 15
Question: What is the email address of the apology letter the user (elfin) wrote out but didn’t send?
<br>Answer: `Santa.claus@gmail.com`

Using eM Client, check Drafts.

![elf15](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/792a9fd6-34ca-44dc-8d0a-ffe9ec719181)

## Task 16
Question: The head elf PixelPeppermint has requested any passwords of Elfins to assist in the investigation down the line. What’s the windows password of Elfin’s host?
<br>Answer: `Santaknowskungfu`

Impacket (SAM) and MD5 cracker ftw.
By using impacket, we can extract the SAM database in the System32 directory to crack passwords.
<br>Path: ``\optinseltrace1\TriageData\C\Windows\system32\config\SAM``

![elf16](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/215eb681-5d52-4834-b92e-15b6cd56ece1)

Command: 
```
python3 secretsdump.py -sam /optinseltrace1/TriageData/C/Windows/system32/config/SAM -system /optinseltrace1/TriageData/C/Windows/system32/config/SYSTEM LOCAL
```

![elf16 1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/4d58923a-6fd8-4df2-b181-01422cf5191d)

