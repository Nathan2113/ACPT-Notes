**Linux Tools**
- ntlmrelayx
- netexec
- smbclient-ng
- certipy
- impacket
- responder
- bloodhound



**NetExec**
- interact via protocols

basic syntax
`nxc <protocol> <IP> -u <user> -p <pass>`


command line flags
- `--shares`
- `--users`
- `--groups`
- `--interfaces`
- ...


CONFIG FILE
- located in `~/.nxc/nxc.conf`
- recommended to turn "log_mode" and "check_guest_account" to true

list modules with `<protocol> -L`
- split by privilege categories
![](assets/Pasted%20image%2020260703084025.png)


modules specified with `-M`
- `enum_av`
- `change-password`
- `vnc`
- ...


**List Shares**
low privs
`nxc smb <IP> -u <user> -p <pass> --shares`


**List Network Interfaces with Admin Privs**
`nxc smb <IP> -u <user> -p <pass> --interfaces`


**Command Execution with SMB and Admin Privs**
`nxc smb <IP> -u <user> -p <pass> -x <command>`
![](assets/Pasted%20image%2020260703084659.png)


