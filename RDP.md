screenshot RDP connection with NLA disabled
`nxc rdp <IP> --nla-screenshot`
- if NLA is disabled, can screenshot the connection to see who has logged in
- pass the hash only possible if "Restricted Admin Mode" is enabled (reg key "DisabledRestrictedAdmin" is set to 0)


### Learning Objective 23
**Identify NLA of RDP in cap.corp domain and take a screenshot of insecure RDP**
find RDP instances - nxc will tell you if NLA is disabled
`nxc rdp 172.16.1-26`
![](assets/Pasted%20image%2020260724111648.png)

get a screenshot of the connections (will need to increase timeout)
`nxc rdp 172.16.13.1-30 --nla-screenshot --rdp-timeout 10`
![](assets/Pasted%20image%2020260724111717.png)
![](assets/Pasted%20image%2020260724111833.png)
- can see that sophie.anderson tried to login recently
- good for enumerating usernames/older sessions
	- if you have creds for any of the users, then you know you can login


**Access the target machine via RDP using the compromised credentials**
`xfreerdp3 /u:car\ryan.young /p:<pass> /v:172.16.13.26 +clipboard /dynamic-resolution`

![](assets/Pasted%20image%2020260724111954.png)

