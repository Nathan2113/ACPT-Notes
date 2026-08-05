**Enumerating ADCS**
enumeration can be done with certipy or nxc's "certipy-find" module
`nxc ldap <dc-ip> -u <user> -p <pass> -M certipy-find`
![](assets/Pasted%20image%2020260724094048.png)

enumerate all certificate templates that are enabled
`nxc ldap <dc-ip> -u <user> -p <pass> -M certipy-find -o VULN=False ENABLED=True`
