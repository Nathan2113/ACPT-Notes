**Active Sessions**
- RDP sessions with qwinsta
`nxc smb <IP> -u <user> -p <pass> --qwinsta`

- SMB
`nxc smb <IP> -u <user> -p <pass> --loggedon-users`

- remote registry
	- can be done w/o admin
`nxc smb <IP> -u <user> -p <pass> --reg-sessions`


**Learning Objective 12**
Enumerate active logon sessions on DESIGNSRV01 using
- qwinsta
`proxychains -q nxc smb <IP> -u <user> -H <hash> --local-auth --qwinsta`
- Workstation Remote Service Protocol (WRST)
`proxychains -q nxc smb <IP> -u <user> -H <hash> --local-auth --loggedon-users`
- remote registry service
`proxychains -q nxc smb <IP> -u <user> -H <hash> --local-auth --reg-sessions`



**Impersonation**
`nxc smb <IP> -u <user> -p <pass> -M schtask_as -o USER=<impersonated_user> CMD=whoami`
- get local admin > impersonate a domain user
	- Defender and other AVs looks for the way nxc retrieves the output
		- to get around this, just ping our machine to see if execution worked

