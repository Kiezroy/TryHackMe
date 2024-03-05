Room: https://tryhackme.com/room/easyctf

Title: EasyCTF

Target IP: 

**NOTE:** Keep in mind this walkthrough was made throughout several days, so the target IP will appear different in the screenshots. Please use your own target IP provided with TryHackMe.

1. How many services are running under port 1000?

    - Let's first attempt an nmap scan to see what ports are open and what services are running!
    - ```nmap -sC -sV [target IP]```
    - -sC enables the use of default scripts
    - -sV performs service version detection
  
    ![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/646a609e-2862-45d0-9457-9608017a498c)

    - We see that three ports open and two are under port 1000, 21 for ftp, 80 for http, and 2222 for ssh
    - Answer: **2**
  
2. What is running on the higher port?

    -Answer: **ssh**

3. What's the CVE you're using against the application?

   - Let's use a gobuster command for website enumeration and see what we can find

   - Use ```gobuster dir -w [path to wordlist] -u [target IP]```
   - Note: You will have to use ```sudo apt install gobuster``` and ```sudo apt install wordlists``` if your VM doesn't have it yet

   ![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/aa60c046-59bd-4a84-8f30-1502892c7a52)

   - Status code 403 = forbidden
   - Status code 200 = success
   - Status code 301 = redirect
     
   - Let's navigate to the last URL, [target IP]/simple/

    ![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/ce0f9b02-83e5-4bf1-90f0-37dec06ba73c)

   - Scrolling down to the bottom of the webpage, we find the version number. Hmmm, this looks interesting! Let's do a quick google search
     
    ![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/bc2fce3b-75c2-4ae7-9ccc-ac202ad911b2)

   ![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/65e3967f-f7de-4b4e-891f-239c1c2a49af)

   - This leads us to the exploit database, and upon clicking on the CVE number, we find it on the NIST website!
  
   ![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/62ce08cb-1508-4b92-acf8-ef4341a2ed7b)

   - Answer: **CVE-2019-9053**

4. To what kind of vulnerability is the application vulnerable?

    - Answer: **sqli**
    - Short for SQL Injection
  
5. What's the password?

   - Let's go ahead and download the python script we found earlier from the exploit database!
   - Use the command ```python2.7 46635.py -u http://[target IP]/simple -c -w [Path to wordlist]```


     
