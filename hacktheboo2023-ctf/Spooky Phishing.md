# Explanation
We are given the following file:
* `index.html`: HTML page containing the phishing mechanism.

Analyze its source code using any text editor, in my case Visual Studio Code, and notice how there are several encoded codes in the tags highlighted.

![phishing1](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/1666ccad-4bf5-44bf-9589-e6d1740d82fa)

Let us start analyzing the `<script>` tag in HTML is used to include JavaScript code in a web page. 
1. `data`: - This specifies that the URL is a data URI. 
2. `text/javascript;base64`: - This part of the URL indicates that the data is encoded as Base64 and represents `JavaScript` code.

Hence, the whole section can be decoded from Base64 and the script code can then be analyzed further.

![phishing2](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/1d141eed-f3f9-43a8-beb5-68442c9baffa)

Notice how the script initiates two variables, `nn` and `aa`. These variables contain the result of the `decodeHex` function given the two hidden values as parameter each time, after the result of the `atob` function (atob decodes data that has been encoded with base64). 

![phishing3](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/02f2d840-8370-4073-a19c-d30d0dd6f6a2)

# Solution
One way is to analyze the `decodeHex` function. By concatenating the two hidden strings in the html file and decoding it with Base64 first and then hex, the flag can be retrieved.

![phishing4](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/61292495-bd0a-44f2-b61e-28baf9658ece)

Another way is to just enter the website as it will redirect us to an error page with the flag present in its URL. However, this was just an error from HTB after getting confirmation from a HTB staff on Discord.

![phishing5](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/b53a02cc-f233-49cf-add4-51b69a782d3b)

![phishing6](https://github.com/warlocksmurf/HTB-writeups/assets/121353711/9af66095-5825-4d1f-be28-05b5a727cba0)
