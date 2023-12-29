# Sherlock Scenario
It seems our precious technology has been leaked to the threat actor. Our head Elf, PixelPepermint, seems to think that there were some hard-coded sensitive URLs within the technology sent. 
Please audit our Sparky Cloud logs and confirm if anything was stolen! PS - Santa likes his answers in UTC...

# Solution
## Task 1 
Question: What is the MD5 sum of the binary the Threat Actor found the S3 bucket location in?
<br>Answer: `62d5c1f1f9020c98f97d8085b9456b05`

Check the MD5 sum of the binary file in OpTinsel-1

![aws1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/ae660d19-76bf-47a1-b589-0fd0e56677a0)

## Task 2  
Question: What time did the Threat Actor begin their automated retrieval of the contents of our exposed S3 bucket?
<br>Answer: `2023-11-29 08:24:07`

I was new at AWS cloudtrail forensics so I had to utilize a cheetsheet created by @njw on HTB Discord.

```
find . -type f -exec jq '.Records[] | [.eventTime, .sourceIPAddress, .userIdentity.arn, .eventName] | @tsv' {} ; | sort | grep -iE '(GetObject)'
```

![aws2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/707636b2-bcc7-42ad-ad8c-4db3d07a8c9a)

## Task 3 
Question: What time did the Threat Actor complete their automated retrieval of the contents of our exposed S3 bucket?
<br>Answer: `2023-11-29 08:24:16`

Check the end of Task 2 enumeration logs.

## Task 4 
Question: Based on the Threat Actor's user agent - what scripting language did the TA likely utilise to retrieve the files?
<br>Answer: `python`

I just guessed it, have no idea how to solve this.

## Task 5 
Question: Which file did the Threat Actor locate some hard coded credentials within?
<br>Answer: `claus.py`

By checking the binary file, we can find the AWS URL `https://papa-noel.s3.eu-west-3.amazonaws.com/`. In the AWS bucket, we find a commit (COMMIT_EDITMSG) mentioning that claus.py was modified to remove credentials. This suggests that the hidden credentials were indeed stored in the python script.

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/ca0a1d9f-0055-4345-b6df-71ad857f562e)

```
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Author:    Author Name <bytesparkle@papanoel.co.uk>
#
# On branch master
# Changes to be committed:
#	modified:   claus.py
#
Removed the sparkly creds from the script! How silly of me! Sometimes I'm about as useful as a screen saver on Santa's Sleigh!!!!!!
```

![aws5](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/57b7e287-5121-4698-b612-54802a75cc0a)

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/95dcdcb3-bdc0-4d54-848e-4ba9de1fc619)

## Task 6 
Question: Please detail all confirmed malicious IP addresses. (Ascending Order)
<br>Answer: `45.133.193.41, 191.101.31.57`

We know one of the IP address from Task 2, 192.101.31.57. Now we have to find the other, which can be found using the command 

```
find . -type f -exec jq '.Records[] | [.sourceIPAddress, .userIdentity.userName] | @tsv' {} \; | sort -u
```

![aws6](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/eca44655-1e08-447d-8fdd-8de5158b43d9)

## Task 7
Question: We are extremely concerned the TA managed to compromise our private S3 bucket, which contains an important VPN file. Please confirm the name of this VPN file and the time it was retrieved by the TA.
<br>Answer: `bytesparkle.ovpn, 2023-11-29 10:16:53`

Pretty straightforward, just grep vpn and get your answer.

```
find . -type f -exec jq '.Records[] | [.eventTime, .sourceIPAddress, .userIdentity.arn, .eventName, .requestParameters.key] | @tsv' {} ; | sort | grep -iE '(List|Get|Describe)' | grep "vpn"
```

![aws7](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/b2acc605-f292-4d13-9db4-4433c5df0877)

## Task 8
Question: Please confirm the username of the compromised AWS account?
<br>Answer: `elfadmin`

Similar to Task 6.

## Task 9
Question: Based on the analysis completed Santa Claus has asked for some advice. What is the ARN of the S3 Bucket that requires locking down?
<br>Answer: `arn:aws:s3:::papa-noel`

Similar to Task 5, checking the binary file we can find the ARM of the S3 Bucket or by using this command

```
find . -type f -exec jq '.Records[] | [.sourceIPAddress=="191.101.31.57", .eventName=="GetObject", .requestParameters.bucketName, .resources[0].ARN] | @tsv' {} \; | sort -u
```

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/2009aa6d-767d-4d0a-9847-3da431babbac)

![aws9](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/eccec6e4-0a3b-494d-b9cc-89502e05a328)
