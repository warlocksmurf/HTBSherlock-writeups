# An unusual sighting 
Question: As the preparations come to an end, and The Fray draws near each day, our newly established team has started work on refactoring the new CMS application for the competition. However, after some time we noticed that a lot of our work mysteriously has been disappearing! We managed to extract the SSH Logs and the Bash History from our dev server in question. The faction that manages to uncover the perpetrator will have a massive bonus come competition!

```
└─$ nc 94.237.52.91 54396

+---------------------+---------------------------------------------------------------------------------------------------------------------+
|        Title        |                                                     Description                                                     |
+---------------------+---------------------------------------------------------------------------------------------------------------------+
| An unusual sighting |                        As the preparations come to an end, and The Fray draws near each day,                        |
|                     |             our newly established team has started work on refactoring the new CMS application for the competition. |
|                     |                  However, after some time we noticed that a lot of our work mysteriously has been disappearing!     |
|                     |                     We managed to extract the SSH Logs and the Bash History from our dev server in question.        |
|                     |               The faction that manages to uncover the perpetrator will have a massive bonus come the competition!   |
|                     |                                                                                                                     |
|                     |                                            Note: Operating Hours of Korp: 0900 - 1900                               |
+---------------------+---------------------------------------------------------------------------------------------------------------------+


Note 2: All timestamps are in the format they appear in the logs

What is the IP Address and Port of the SSH Server (IP:PORT)
> 100.107.36.130:2221
[+] Correct!

What time is the first successful Login
> 2024-02-13 11:29:50
[+] Correct!

What is the time of the unusual Login
> 2024-02-19 04:00:14
[+] Correct!

What is the Fingerprint of the attacker's public key
> OPkBSs6okUKraq8pYo4XwwBg55QSo210F09FCe1-yj4
[+] Correct!

What is the first command the attacker executed after logging in                                                                                                                           
> whoami                                                                                                                                                                                   
[+] Correct!
                                                                                                                                                                                           
What is the final command the attacker executed before logging out                                                                                                                         
> ./setup                                                                                                                                                                                  
[+] Correct!
                                                                                                                                                                                           
[+] Here is the flag: HTB{B3sT_0f_luck_1n_th3_Fr4y!!}                                                                                                                                      
```

1. What is the IP Address and Port of the SSH Server (IP:PORT)

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/cd680426-f59d-4c4b-a659-e459860387cf)

2. What time is the first successful Login

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/094ce796-d822-41cb-b922-23f045bcb613)

3. What is the time of the unusual Login

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/04a12099-d036-4aae-abc8-e52caa7aac27)

4. What is the Fingerprint of the attacker's public key'

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/505f0b2f-459c-4fb5-9cd1-6a12cd4f3112)

5. What is the first command the attacker executed after logging in

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/d9d7b4c5-f950-46b3-9780-e3aa28788a0d)

6. What is the final command the attacker executed before logging out

![image](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/b89ace7a-7634-431b-b9d3-40898d66417f)
