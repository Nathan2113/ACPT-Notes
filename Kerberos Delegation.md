can request tickets for other users
- for example, a web frontend needs to access a database
	- user forwards its ticket to the frontend > frontend authenticates in the context of the user 
	- what if the user initially authenticated with NTLM?


**S4U2Self Extension**
- request a service ticket for itself on behalf of any user
	-  omputer can request a service ticket for Domain Administrator for itself and is therefore automatically admin on itself
`nxc smb <IP> -u <computer> -p <pass> --delegate Administrator --self`
- works from all machine accounts, even if RBCD is not configured
- useful to become administrator on the machine if we have NOT compromised the machine yet