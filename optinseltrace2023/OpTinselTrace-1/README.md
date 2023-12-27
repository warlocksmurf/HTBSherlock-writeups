# Sherlock Scenario
An elf named "Elfin" has been acting rather suspiciously lately. He's been working at odd hours and seems to be bypassing some of Santa's security protocols. 
Santa's network of intelligence elves has told Santa that the Grinch got a little bit too tipsy on egg nog and made mention of an insider elf! 
Santa is very busy with his naughty and nice list, so he’s put you in charge of figuring this one out. Please audit Elfin’s workstation and email communications.

# Solution
## Task 1 
Question: What is the name of the email client that Elfin is using?
<br>Answer: `eM client`

Pretty straightforward, just analyze files inside the user Elfin.

## Task 2  
Question: What is the email the threat is using?
<br>Answer: `definitelynotthegrinch@gmail.com`

One easy way to solve this whole Sherlock is to just utilize the email client (eM client) to search for clues about Elfin and the TA.
After importing the xml file, we can see the Grinch sending an email to Elfin!

## Task 3 
Question: When does the threat actor reach out to Elfin?
<br>Answer: `2023-11-27 17:27:26`

Same answer from Task 2, just open up the replies (make sure the time is converted to UTC).

## Task 4 
Question: What is the name of Elfins boss?
<br>Answer: `elfuttin bigelf`

Pretty straightforward, just look through the emails.

## Task 5 
Question: What is the title of the email in which Elfin first mentions his access to Santas special files?
<br>Answer: `Re: work`

Pretty straightforward, just look through the emails and focus on the subject line.

## Task 6 
Question: The threat actor changes their name, what is the new name + the date of the first email Elfin receives with it?
<br>Answer: `wendy elflower, 2023-11-28 10:00:21`

Look through the emails for suspicious users and open up the replies (make sure the time is converted to UTC).

## Task 7
Question: What is the name of the bar that Elfin offers to meet the threat actor at?
<br>Answer: `SnowGlobe`

Pretty straightforward, just look through the emails from Elfin and wendy elflower.

## Task 8
Question: When does Elfin offer to send the secret files to the actor?
<br>Answer: `2023-11-28 16:56:13`

Pretty straightforward, just look through the emails from Elfin and wendy elflower (make sure the time is converted to UTC).

## Task 9
Question: What is the search string for the first suspicious google search from Elfin? (Format: string)
<br>Answer: `how to get around work security`

Since I did not have a Google History viewer tool, I just manually analyze using Notepad 💀
The path ``\optinseltrace1\TriageData\C\users\Elfin\Appdata\Local\Google\Chrome\User Data\Default\History``

Using Notepad, I managed to find suspicious search results.

## Task 10
Question: What is the name of the author who wrote the article from the CIA field manual?
<br>Answer: `Joost Minnaar`

Similar to Task 9, you can find the article search results.

## Task 11
Question: What is the name of Santas secret file that Elfin sent to the actor?
<br>Answer: `santa_deliveries.zip`

The secret file can be found in Elfin's user directory.

## Task 12
Question: According to the filesystem, what is the exact CreationTime of the secret file on Elfins host?
<br>Answer: `2023-11-28 17:01:29`

One way to always find the exact CreationTime is to just use the $MFT data and Timeline Explorer.

## Task 13
Question: What is the full directory name that Elfin stored the file in?
<br>Answer: `C:\users\Elfin\Appdata\Roaming\top-secret`

Similar to Task 11.

## Task 14
Question: What is the name of the bar that Elfin offers to meet the threat actor at?
<br>Answer: `Greece`

Similar to Task 9, you can find suspicious search results about flying to Greece from North Pole.

## Task 15
Question: What is the email address of the apology letter the user (elfin) wrote out but didn’t send?
<br>Answer: `Santa.claus@gmail.com`

Using eM Client, check Drafts.

## Task 16
Question: The head elf PixelPeppermint has requested any passwords of Elfins to assist in the investigation down the line. What’s the windows password of Elfin’s host?
<br>Answer: `Santaknowskungfu`

Impacket (SAM) and MD5 cracker ftw.
