# Explanation
We are given the following file:
* `index.html`: HTML page containing the phishing mechanism.

Analyze its source code using any text editor, in my case Visual Studio Code, and notice how there are several encoded codes in the tags highlighted.

<p align='center'>
  <img src='/images/phishing1.png' alt="VirusTotal">
</p>

Let us start analyzing the `<script>` tag in HTML is used to include JavaScript code in a web page. 
1. `data`: - This specifies that the URL is a data URI. 
2. `text/javascript;base64`: - This part of the URL indicates that the data is encoded as Base64 and represents `JavaScript` code.

Hence, the whole section can be decoded from Base64.

<p align='center'>
  <img src='/images/phishing.png' alt="VirusTotal">
</p>

The script code can then be analyzed further.

<p align='center'>
  <img src='/images/phishing3.png' alt="VirusTotal">
</p>

Notice how the script initiates two variables, `nn` and `aa`. These variables contain the result of the `decodeHex` function given the two hidden values as parameter each time, after the result of the `atob` function (atob decodes data that has been encoded with base64). 

# Solution
One way is to analyze the `decodeHex` function. By concatenating the two hidden strings in the html file and decoding it with Base64 first and then hex, the flag can be retrieved.

<p align='center'>
  <img src='/images/phishing4.png' alt="VirusTotal">
</p>

Another way is to just enter the website as it will redirect us to an error page with the flag present in its URL. However, this was just an error from HTB after getting confirmation from a HTB staff on Discord.

<p align='center'>
  <img src='/images/phishing5.png' alt="VirusTotal">
</p>

<p align='center'>
  <img src='/images/phishing6.png' alt="VirusTotal">
</p>

<p align='center'>
  <img src='/images/writeup.png' alt="VirusTotal">
</p>
