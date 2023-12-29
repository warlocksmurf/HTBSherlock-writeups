# Explanation
We are given the following file:
* `payload.bat`: Malicious bat file.

As we can see from the following image, the bat script contains many random variables that are assigned random values. In a batch script (a .bat or .cmd file), the set command defines and manipulates environment variables which store information that can be used by the script or other programs running in the same environment (the Command Prompt session).

![bat](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/0fd21a7a-8f4c-46c9-a985-1570a57f694c)

# Solution

First, the bat file can be analyzed using `Any.Run` which is a malware analyzer tool.
We will get the following result if we upload the sample on the aforementioned site.

![bat2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/0e1c32ed-4f98-4d8b-8f06-350efa1761e3)

By analyzing the behavior graph, we notice three actions made:
1. The attacker can be seen using `cmd` to copy PowerShell to a different path with a random name (Earttxmxaqr.png). 
1. The attacker then uses cmd again to rename the downloaded bat file and also change it's extension to `.png.bat` to avoid detection.
1. The attacker executes the base64 encoded string using the renamed PowerShell executable (`-enc` argument in Powershell is used to pass a base64 encoded command).

![bat3](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/66bc25ac-e13b-427b-917c-b51767202b0b)

By decoding the string, the flag can be retrieved.

![bat4](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/ff75d596-8d18-4edc-ad2d-925625bd205d)
