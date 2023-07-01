## Search
### nmap
- command  
`sudo nmap -Pn -A -T4 10.10.10.175`
`nmap --script smb-vuln* -p 445 10.10.10.175`

- result

<details><summary>summary</summary>

<pre>

┌──(kali㉿kali)-[~/Documents/Get-LAPSPasswords]
└─$ sudo nmap -Pn -A -T4 10.10.10.175
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-01 05:05 EDT
Nmap scan report for 10.10.10.175
Host is up (0.18s latency).
Not shown: 988 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Egotistical Bank :: Home
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-07-01 16:06:07Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: EGOTISTICAL-BANK.LOCAL0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: EGOTISTICAL-BANK.LOCAL0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host
Network Distance: 2 hops
Service Info: Host: SAUNA; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-07-01T16:06:29
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required
|_clock-skew: 7h00m00s

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   212.66 ms 10.10.14.1
2   190.92 ms 10.10.10.175

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 93.49 seconds

┌──(kali㉿kali)-[~/Documents/Get-LAPSPasswords]
└─$ nmap --script smb-vuln* -p 445 10.10.10.175

Starting Nmap 7.93 ( https://nmap.org ) at 2023-07-01 05:22 EDT
Nmap scan report for 10.10.10.175
Host is up (0.18s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: Could not negotiate a connection:SMB: Failed to receive bytes: ERROR

Nmap done: 1 IP address (1 host up) scanned in 15.34 seconds


</pre>

</details>
