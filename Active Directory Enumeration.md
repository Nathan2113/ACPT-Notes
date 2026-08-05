# LDAP Protocol

LDAP Query
example query
- `"(&(objectClass=user) (aAMAccounName=username) )"`

using netexec for manual queries
`nxc ldap <dc-ip> -u <user> -p <pass> --query <query>`
- `" (& (sAMAccountName=adm*) (description=*password*))" ``
- `"sAMAccountName description"`
![](assets/Pasted%20image%2020260710093832.png)


LDAPSearch
- use in the event that netexec breaks
- `ldapsearch -LLL -x -z max -H ldap://<IP> -D <user>@<DOMAIN> -w <pass> -b "DC=<DOMAIN>,DC=<DOMAIN>,DC=<DOMAIN>" "sAMAccountName=<user>"`


### Password Hunting
some useful netexec modules
- get-desc-users
- get-info-users
- get-userPassword

list module options
- `nxc ldap -M get-desc-users --options`
![](assets/Pasted%20image%2020260710094532.png)
- PASSWORDPOLICY rules out all embedded strings, makes search much faster

module options can also be applied with
- `-o KEY=VALUE KEY2=VALUE2`
- for example, enabling PASSWORDPOLICY
	- `-o PASSWORDPOLICY=True`
	- enabled AD password complexity rules (1 upper, 1 lower..., min length = 6)
- full command example
	- ` ldap <dc-ip> -u <user> -p <password> -M get-descusers -o PASSWORDPOLICY=True`

### Learning Objective 7
![](assets/Pasted%20image%2020260710094803.png)

Authenticate to the domain by abusing Pass-The-Key
- `nxc ldap <IP> -u <user> -aesKey <key>`
![](assets/Pasted%20image%2020260710095124.png)


Enumerate the auto.corp domain
- users
	- `nxc ldap <IP> -u <user> -aesKey <key> --users`
- computers
	- `nxc ldap <IP> -u <user> -aesKey <key> --computers`
- groups
	- `nxc ldap <IP> -u <user> -aesKey <key> --groups`
- domain administrators
	- `nxc ldap <IP> -u <user> -aesKey <key> --groups "Domain Admins"`



Build LDAP queries for manual enumeration
- `nxc ldap <IP> -u <user> -aesKey <key> --query "(adminCount=1)" "sAMAccountName admincount"`
	- gives all users with the adminCount bit set, filters by sAMAccountName and admincount
![](assets/Pasted%20image%2020260710100633.png)

`nxc ldap <IP> -u <user> -aesKey <key> --query "(&(objectClass=user)(description=*password*))" "displayname description"`
![](assets/Pasted%20image%2020260710101031.png)

Enumerate user description field for clear-text password
- `nxc ldap <IP> -u <user> -aesKey <key> -M get-desc-users -o PASSWORDPOLICY=True`
	- gets all descriptions and filters description strings by password policy compliance
![](assets/Pasted%20image%2020260710100835.png)


