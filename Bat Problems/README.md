# Explanation
We are given the following file:
* `payload.bat`: Malicious bat file.

As we can see from the following image, the bat script contains many random variables that are assigned random values. In a batch script (a .bat or .cmd file), the set command defines and manipulates environment variables which store information that can be used by the script or other programs running in the same environment (the Command Prompt session).

![anyrun5](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/7acc46d7-37c0-4cc5-86e1-c2216d4b985b)

# Solution

First, the bat file will be analyzed using `any.run` which is a malware analyzer tool.
We will get the following result if we upload the sample on the aforementioned site.

![anyrun1](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/35aac92c-2d76-4f1c-be72-1b4e3c9b8b33)

By analyzing the behavior graph, we notice three actions made:
1. The attacker can be seen using `cmd` to copy PowerShell to a different path with a random name (Earttxmxaqr.png). 
1. The attacker then uses cmd again to rename the downloaded bat file and also change it's extension to `.png.bat` to avoid detection.
1. The attacker executes the base64 encoded string using the renamed PowerShell executable (`-enc` argument in Powershell is used to pass a base64 encoded command).

![anyrun3](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/e9f54e7f-f9a9-414a-87c5-2fc660892202)

By decoding the string, the flag can be retrieved.

![anyrun4](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/095b5204-fe1d-42e5-8bea-4488911d55e3)
