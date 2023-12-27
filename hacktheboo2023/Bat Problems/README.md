# Explanation
We are given the following file:
* `payload.bat`: Malicious bat file.

As we can see from the following image, the bat script contains many random variables that are assigned random values. In a batch script (a .bat or .cmd file), the set command defines and manipulates environment variables which store information that can be used by the script or other programs running in the same environment (the Command Prompt session).

<p align='center'>
  <img src='/images/anyrun5.png' alt="VirusTotal">
</p>

# Solution

First, the bat file can be analyzed using `Any.Run` which is a malware analyzer tool.
We will get the following result if we upload the sample on the aforementioned site.

<p align='center'>
  <img src='/images/anyrun1.png' alt="VirusTotal">
</p>

By analyzing the behavior graph, we notice three actions made:
1. The attacker can be seen using `cmd` to copy PowerShell to a different path with a random name (Earttxmxaqr.png). 
1. The attacker then uses cmd again to rename the downloaded bat file and also change it's extension to `.png.bat` to avoid detection.
1. The attacker executes the base64 encoded string using the renamed PowerShell executable (`-enc` argument in Powershell is used to pass a base64 encoded command).

<p align='center'>
  <img src='/images/anyrun3.png' alt="VirusTotal">
</p>

By decoding the string, the flag can be retrieved.

<p align='center'>
  <img src='/images/anyrun4.png' alt="VirusTotal">
</p>
