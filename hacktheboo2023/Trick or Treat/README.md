# Explanation
We are given the following file:
* `capture.pcap`: Packet capture file
* `trick_or_treat.lnk`: The malicious file

Since the malicious file is provided by HTB, we can first gain additional information on it through `VirusTotal`, a famous OSINT website. After scanning, the file shows that it is indeed malicious and it connects to a malicious domain called `windowsliveupdater.com`. We also can notice that the file is connected to a few IP addresses, mainly `209.197.3.8` which is the malicious one.

<p align='center'>
  <img src='/images/ctf2.png' alt="VirusTotal">
</p>

Analyzing further, we can understand what kind of processes are executed by the malicious file and how it attacks a system.

As shown in the picture below, the malicious file seems to download malicious data from `http://windowsliveupdater.com` using a random User-Agent for HTTP requests to essentially mask its identity on the network. It then sets the downloaded data into a variable (`$vurnwos`) and processes the characters in pairs and converts them from hexadecimal representation to their actual characters. It then performs a bitwise XOR operation with 0x1d on each character and the output is appended to the `$vurnwos` string. Finally, it executes the variable using `Invoke-Command`. It also attempts to execute an empty variable (`$asvods`).

<p align='center'>
  <img src='/images/ctf3.png' alt="Process">
</p>

After knowing what the malicious file does, the packet capture file can be analyzed using Wireshark to find the downloaded content. Since we know that the malicious file requested data from a website, we can filter `HTTP` packets only.

<p align='center'>
  <img src='/images/ctf4.png' alt="Wireshark">
</p>

Going through the HTTP packets, we find a packet that shows the victim sending a GET request to `http://windowsliveupdater.com`. Analyzing the `User-Agent`, we can see that it is one of the randomized user agents set in the malicious file.

<p align='center'>
  <img src='/images/ctf5.png' alt="Wireshark2">
</p>

Now we know that that the IP address `77.74.198.52` is responsible for the malicious file execution, we can check its HTTP response. Notice that the HTTP response packet has a cleartext data that is truncated because it is too long for Wireshark. The data is our key to getting the flag and it must be extracted using the decoding method specified in the malicious file.

<p align='center'>
  <img src='/images/ctf6.png' alt="Wireshark3">
</p>

<p align='center'>
  <img src='/images/ctf7.png' alt="datapacket">
</p>

# Solution
Since we know that the downloable content is encoded in hex and also XOR'ed, we can use `CyberChef` to extract the content.

<p align='center'>
  <img src='/images/flag.png' alt="flag">
</p>
