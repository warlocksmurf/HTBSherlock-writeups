# Explanation
We are given the following file:
* `index.html`: HTML page containing the phishing mechanism.

Analyze its source code using any text editor, in my case Visual Studio Code, and notice how there are several encoded codes in the tags highlighted.

![phishing1](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/1ebe9c1b-8863-4f9f-8857-1f7acf25225c)

Let us start analyzing the `<script>` tag in HTML is used to include JavaScript code in a web page. 
1. `data`: - This specifies that the URL is a data URI. 
2. `text/javascript;base64`: - This part of the URL indicates that the data is encoded as Base64 and represents `JavaScript` code.

Hence, the whole section can be decoded from Base64.

![phishing2](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/e0e99d27-1cb9-4b36-afad-601c6f7acb1a)

The script code can then be analyzed further.

![phishing3](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/87d1c2fb-6543-48b1-b91b-41a9616c0d66)

Notice how the script initiates two variables, `nn` and `aa`. These variables contain the result of the `decodeHex` function given the two hidden values as parameter each time, after the result of the `atob` function (atob decodes data that has been encoded with base64). 

# Solution
One way is to analyze the `decodeHex` function. By concatenating the two hidden strings in the html file and decoding it with Base64 first and then hex, the flag can be retrieved.

![phishing4](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/873aede9-98fb-4940-9904-289ab896607b)

Another way is to just enter the website as it will redirect us to an error page with the flag present in its URL. However, this was just an error from HTB after getting confirmation from a HTB staff on Discord.

![phishing5](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/d4ee4177-0f75-4ab2-bc25-410acb1aea53)

![phishing6](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/723920c1-bf8f-4184-9cd5-fccb27e6756f)

![writeup](https://github.com/warlocksmurf/hacktheboo-forensics/assets/121353711/889eb205-f5a0-4ec4-86bc-9794bb8c3454)
