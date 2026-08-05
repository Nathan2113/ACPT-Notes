Share enumeration
`nxc smb <IP> -u <user> -p <pass> --shares`

Selecting a share for operation
`nxc smb <IP> -u <user> -p <pass> --share C$`

Listing share content
`nxc smb <IP> -u <user> -p <pass> --share C$ --dir <filepath>`

File upload and download
- upload
	- `--put-file <local path> <remote path>`
- download
	- `--get-file <remote path> <local path>`
- remote path should prepend the share name

Enumerating larger shares
- spider_plus module
	- outputs JSON file containing directory structure
	- can do exclusions on file extensions and shares (e.g. print$ and IPC$)
	- optionally download all files (use only on small shares)
- MANSPIDER
	- search options 
		- `-f` search for filenames
		- `-e` search for extensions
		- `-c` search contents of files
	- `manspider <IP> -u <user> -p <pass> --share <share> -c cred password pwd ConvertTo-SecureString ...`


## Learning Objective 8
![](assets/Pasted%20image%2020260710102300.png)

Enumerate SMB shares using NetExec and download share files
`nxc smb <CIDR> -u <user> -aesKey <key> --shares`
![](assets/Pasted%20image%2020260710102629.png)
- IT-Department is interesting

`nxc smb <IP> -u <user> -aesKey <key> --share IT-Department`

- CHECK SLIDES FOR DOWNLOAD/UPLOAD


Enumerate SMB shares using the spider_plus module
- `nxc smb <IP> -u <user> -p <pass> -M spider_plug -o PASSWORDPOOLICY=true EXCLUDE_FILTER=IPC4,print$,NETLOGON,SYSVOL`
![](assets/Pasted%20image%2020260710103303.png)

Enumerate SMB shares using Manspider
`manspider <IP> -u <user> -p <pass> --share <share> -c password passwd ConterTo-SecureString "AsPlainText"`

