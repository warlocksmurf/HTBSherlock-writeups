# Sherlock Scenario
Printers are important in Santa’s workshops, but we haven’t really tried to secure them! The Grinch and his team of elite hackers may try and use this against us! Please investigate using the packet capture provided! The printer server IP Address is 192.168.68.128

# Solution
## Task 1 
Question: The performance of the network printer server has become sluggish, causing interruptions in the workflow at the North Pole workshop. Santa has directed us to generate a support request and examine the network data to pinpoint the source of the issue. He suspects that the Grinch and his group may be involved in this situation. Could you verify if there is an IP Address that is sending an excessive amount of traffic to the printer server?
<br>Answer: `172.17.79.133`

We are given a pcap and reading the scenario, the printer server's IP address is 192.168.68.128. So the first step is to find out which IP address sent the most requests to this IP.

![wire1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/2a4f0852-d3f7-477a-b8cc-0390c2ac67cb)

## Task 2 
Question: Bytesparkle being the technical Lead, found traces of port scanning from the same IP identified in previous attack. Which port was then targeted for initial compromise of the printer?
<br>Answer: `9100`

By filtering the malicious IP address, we can see the IP targetting a specific port using TCP.

![wire2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/6dc93f89-b99a-4c19-8e30-d318dca4dfc4)

## Task 3 
Question: What is the full name of printer running on the server?
<br>Answer: `Northpole HP LaserJet 4200n`

After filtering, the TCP streams can be followed to read its content. At TCP stream 28, we can find readable strings of text.

![wire3](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/9c9db392-68ea-454c-9f96-475e327b7724)

## Task 4 
Question: Grinch intercepted a list of nice and naughty children created by Santa. What was name of the second child on the nice list?
<br>Answer: `Douglas Price`

Similar to Task 3, just analyze the contents in TCP stream 28.

![wire4](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/b81ea456-440e-44f1-83c6-e41a38995e0f)

## Task 5
Question: The Grinch obtained a print job instruction file intended for a printer used by an employee named Elfin. It appears that Santa and the North Pole management team have made the decision to dismiss Elfin. Could you please provide the word for word rationale behind the decision to terminate Elfin's employment?
<br>Answer: `The addressed employee is confirmed to be working with grinch and team. According to Clause 69 , This calls for an immediate expulsion.`

Similar to Task 3 and 4, just analyze the contents in TCP stream 28.

![wire5](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/7f7ab97f-18be-4ff4-9323-0214271d7ec1)

## Task 6 
Question: What was the name of the scheduled print job?
<br>Answer: `MerryChristmas+BonusAnnouncment`

After fully analyzing the contents in TCP stream 28, I analyzed other streams and found TCP stream 46 to have strings of text too.

![wire6](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/46a837c1-8d89-4976-bccc-6ddf85025d8d)

## Task 7
Question: Amidst our ongoing analysis of the current packet capture, the situation has escalated alarmingly. Our security system has detected signs of post-exploitation activities on a highly critical server, which was supposed to be secure with SSH key-only access. This development has raised serious concerns within the security team. While Bytesparkle is investigating the breach, he speculated that this security incident might be connected to the earlier printer issue. Could you determine and provide the complete path of the file on the printer server that enabled the Grinch to laterally move to this critical server?
<br>Answer: `/Administration/securitykeys/ssh_systems/id_rsa`

Similar to Task 6, just analyze the contents in TCP stream 46.

![wire7](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/a5b2b32a-f7b3-416b-b1b6-ef3c6b141a18)

## Task 8
Question: What is size of this file in bytes?
<br>Answer: `1914`

Similar to Task 7, the file size can be found.

## Task 9
Question: What was the hostname of the other compromised critical server?
<br>Answer: `christmas.gifts`

Similar to Task 6 and 7, the hostname can be found in the comments.
```
#This is a backup key for christmas.gifts server. Bytesparkle recommended me this since in christmas days everything gets mixed up in all the chaos and we can lose our access keys to the server just like we did back in 2022 christmas.
```

## Task 10
Question: When did the Grinch attempt to delete a file from the printer? (UTC)
<br>Answer: `2023-12-08 12:18:14`

After fully analyzing the contents in TCP stream 46, I analyzed the final TCP stream 71. After finding the delete file packet, I just analyze the arrival time of the packet.

![wire10](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/ebdbf240-a5de-4016-8639-74a72a2a088e)

![wire10 1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/77339bc2-9e79-49ca-a486-6c9a345291de)
