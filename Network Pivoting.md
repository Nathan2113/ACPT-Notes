**Network Interfaces**
ALWAYS look to see if the device you're on has multiple interfaces
- `ip a`
- `ipconfig`


![](assets/Pasted%20image%2020260703103922.png)


**Network Pivoting**
- multiple options
	- SOCKS
		- manual
		- dynamic
	- Routing
- Tools
	- SSH
		- extremely easy
	- Ligolo-ng
		- will use later in the course
	- Metasploit
	- proxychains



**SSH**
`ssh <user>@<IP> -D 127.0.0.1:<port>`
- can add `-q` to suppress warnings and errors





**SOCKS**
`proxychains <command> <flags>`
- routes traffic through SOCKS proxy
- access and enumerate internal network by prepending `proxychains` to commands
	- `proxychians nmap 172.16.2.0/24 <options>`
	- `proxychains nxc smb <IP>`
- modify proxychains timeout timers to ~200
- change to socks5
- example command for discovery with proxychains that won't take all day
	- `sudo proxychains -q nmap 172.16.2.0/24 -Pn -sT -p 22,135,139,445,3389,5985 --open`
