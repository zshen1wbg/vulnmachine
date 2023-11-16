[dc]
Moved dc01 by abusing unconstrained delegation
Abuse trust key to enumerate domain 2


[websvr]
Port
21: FTP
22: SSH
80: Nginx
3000: Chatjs(Intranet)
8080: noVNC
5902: VNC

Shell: noVNC -> kiosk escape
PrivEsc: root clones a repo and execute it. Sign in GITEA, modify the repo, wait for the execution.
Post-Exp: Found a ccache, can change his password without knowing original password.
According to chatjs, authenticated users should send email to a specific user

[gitsvr]
Port
22: SSH
80: Gitea

Shell: Control master from websvr to gitsvr
PrivEsc: A custom server application, using system(), which is vulnerable to command injection. The functionality is send file. Set SUID
Post-Exp: Get access to Gitea

[exgsvr]
Port
80: OWA
Shell: Accessed from wkt02
PrivEsc: Local admin cred in powershell history
Post-Exp: Unconstrained delegation to dc01




[wkt01] User: Helpdesk department user
Security Control: Windwos Hello, ASR, WDAC, AV, Disable CMD/PSH
Shell: Use changed credential to sign in owa, and send a phishing email to xxx. Automatic script open the attachment.
Post-Exp: Find an xls document, it is the result of password audit. User goffy uses his birthday as PIN. A user in domain 2, his password is leaked in a recent breach 


[wkt02] User: Mail Admin
Security Control: Windwos Hello, ASR, AV
Shell: RDP with goffy and PIN
Post-Exp: Have WinRM access to exgsvr



[db01]
Port
80: OWA
1433: MSSQL (A custom username can impersonate sa)

Shell: SQLi RCE
PrivEsc: SeImpersonatePrivilege(Shut down print service)
Post-Exp: Mimikatz dump cred, find db manager's account. Rabbit hole





[dc02]
Trusted by domain 1(Inbound trust)
ADCS configured




[wkt03]
Shell: Abuse trust account to have enumeration access, asreproasting the user to recover his password. The user is a member of 
