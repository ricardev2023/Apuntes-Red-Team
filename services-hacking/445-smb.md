# 445 - SMB \(traducir\)

## SMB PROTOCOL

### **Introduction to SMB Protocol**

Server Message Block \(SMB\), the modern dialect of which was known as Common Internet File System, operates as an application-layer network protocol for file sharing that allows applications on a computer to read and write to files and to request services from server programs in a computer network. The SMB protocol can be used on top of its TCP/IP protocol or other network protocols. Using the SMB protocol, an application \(or the user of an application\) can access files or other resources at a remote server. This allows applications to read, create, and update files on the remote server. It can also communicate with any server program that is set up to receive an SMB client request

### **Working of SMB**

SMB functions as a request-response or client-server protocol. The only time that the protocol does not work in a response-request framework is when a client requests an opportunistic lock \(oplock\) and the server has to break an existing oplock because the current mode is incompatible with the existing oplock. Client computers using SMB connect to a supporting server using NetBIOS over TCP/IP, IPX/SPX, or NetBUI. Once the connection is established, the client computer or program can then open, read/write, and access files similar to the file system on a local computer.

### **Versions of Windows SMB**

CIFS: The old version of SMB, which was included in Microsoft Windows NT 4.0 in 1996.  
SMB 1.0 / SMB1: The version used in Windows 2000, Windows XP, Windows Server 2003 and Windows Server 2003 R2.  
SMB 2.0 / SMB2: This version used in Windows Vista and Windows Server 2008.  
SMB 2.1 / SMB2.1: This version used in Windows 7 and Windows Server 2008 R2.  
SMB 3.0 / SMB3: This version used in Windows 8 and Windows Server 2012.  
SMB 3.02 / SMB3: This version used in Windows 8.1 and Windows Server 2012 R2.  
SMB 3.1: This version used in Windows Server 2016 and Windows 10.  
Presently, the latest version of SMB is the SMB 3.1.1 which was introduced with Windows 10 and Windows Server 2016. This version supports AES 128 GCM encryption in addition to AES 128 CCM encryption added in SMB3, and implements pre-authentication integrity check using SHA-512 hash. SMB 3.1.1 also makes secure negotiation mandatory when connecting to clients using SMB 2.x and higher.

### **SMB Protocol Security**

The SMB protocol supports two levels of security. The first is the share level. The server is protected at this level and each share has a password. The client computer or user has to enter the password to access data or files saved under the specific share. This is the only security model available in the Core and Core plus SMG protocol definitions. User level protection was later added to the SMB protocol. It is applied to individual files and each share is based on specific user access rights. Once a server authenticates the client, he/she is given a unique identification \(UID\) that is presented upon access to the server. The SMB protocol has supported individual security since LAN Manager 1.0 was implemented.

## **SMB Enumeration**

To identify the following information of Windows or Samba system, every pentester go for SMB enumeration during network penetration testing.  
• Banner Grabbing  
• RID cycling  
• User listing  
• Listing of group membership information  
• Share enumeration  
• Detecting if a host is in a workgroup or a domain  
• Identifying the remote operating system  
• Password policy retrieval

Here you can observe, we are using nmap the most famous network scanning tool for SMB enumeration.  
`nmap -p 445 -A 192.168.1.101`

As a result, we enumerated the following information about the target machine:  
`Operating System: Windows 7 ultimate  
Computer Name & NetBIOS Name: Raj  
SMB security mode: SMB 2.02`  
There are so many automated scripts and tools available for SMB enumeration and if you want to know more about SMB Enumeration then read below.

### HOSTNAME

[https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/](https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/)

We will start the enumeration of the SMB by finding the hostname of the target machine. This can be done by various tools.

**nmblookup**

We started with nmblookup tool. It is designed to make use of queries for the NetBIOS names and then map them to their subsequent IP addresses in a network. The options allow the name queries to be directed at a particular IP broadcast area or to a particular machine. All queries are done over UDP.

For unique names:

 00: Workstation Service \(workstation name\)

 03: Windows Messenger service

 06: Remote Access Service

 20: File Service \(also called Host Record\)

 21: Remote Access Service client

 1B: Domain Master Browser – Primary Domain Controller for a domain

 1D: Master Browser

For group names:

 00: Workstation Service \(workgroup/domain name\)

 1C: Domain Controllers for a domain

 1E: Browser Service Elections

`nmblookup -A 192.168.1.17`

 **nbtscan**

 Moving Forward we used nbtscan tool. NBTscan is a program for scanning IP networks for NetBIOS name information. It sends NetBIOS status query to each address in supplied range and lists received information in human-readable form. For each responded host it lists IP address, NetBIOS computer name, logged-in user name and MAC address \(such as Ethernet\).

 **nbstat NSE Script**

This nmap script attempts to retrieve the targetâ€™s NetBIOS names and MAC address. By default, the script displays the name of the computer and the logged-in user; if the verbosity is turned up, it displays all names the system thinks it owns. It also shows the flags that we studied in nmblookup tool.

`nmap --script nbstat.nse 192.168.1.17`

**nbtstat** \(windows\)

This Windows command displays the NetBIOS over TCP/IP \(NetBT\) protocol statistics. It can read the NetBIOS name tables for both the local computer and remote computers. It can also read the NetBIOS name cache. This command allows a refresh of the NetBIOS name cache and the names registered with Windows Internet Name Service \(WINS\). When used without any parameters, this command displays Help Information. This command is available only if the Internet Protocol \(TCP/IP\) protocol is installed as a component in the properties of a network adapter in Network Connections.

**Ping**

We can also use the ping command to detect the hostname of an SMB server or machine. The -a parameter specifies reverse name resolution to be performed on the destination IP address. If this is successful, ping displays the corresponding hostname.

**smb-os-discovery NSE Script**

This NSE script attempts to determine the operating system, computer name, domain, workgroup, and current time over the SMB protocol \(ports 445 or 139\). It is achieved by initiating a session with the anonymous account \(or with a proper user account, if one is given; it likely doesnâ€™t make a difference\); in response to a session starting, the server will send back all this information.  
The following fields may be included in the output, depending on the circumstances \(e.g., the workgroup name is mutually exclusive with domain and forest names\) and the information available:  
• OS  
• Computer name  
• Domain name  
• Forest name  
• FQDN  
• NetBIOS computer name  
• NetBIOS domain name  
• Workgroup  
• System time

`nmap --script smb-os-discovery 192.168.1.17`

### SHARES AND NULL SESSION

[https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/](https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/)

As we discussed earlier that SMB works on sharing files and resources. In order to transfer these files or resources, there are data streams that are called shares. There are public shares that are accessible to everyone on the network and then there are the user-specific shares. Letâ€™s enumerate these shares.

**SMBMap**

SMBMap allows users to enumerate samba share drives across an entire domain. List share drives, drive permissions, share contents, upload/download functionality, file name auto-download pattern matching, and even execute remote commands. This tool was designed with pen testing in mind and is intended to simplify searching for potentially sensitive data across large networks.

Let’s enumerate a user-specific share using the credentials for that user.  We are enumerating the share for the user raj as shown in the image below.

`smbmap -H 192.168.1.17 -u raj -p 123`

**smbclient**

smbclient is samba client with an “ftp like” interface. It is a useful tool to test connectivity to a Windows share. It can be used to transfer files, or to look at share names. In addition, it has a nifty ability to ‘tar’ \(backup\) and restore files from a server to a client and vice versa. We enumerated the target machine and found the guest share using the smbclient directly. Then we connect to the guest share and see that there is a text file by the name of file.txt. We can download it using the get command.

`smbclient -L 192.168.1.40  
smbclient //192.168.1.40/guest  
get file.txt`

Now we enumerate the user-specific share. We connect to the SMB as user raj and find a share by the name of ‘share’. We reconfigured the smbclient command to access the share and we see that we find a file named raj.txt. Again, we can download this file as well as using the get command.

`smbclient -L 192.168.1.17 -U raj%123  
smbclient //192.168.1.17/share -U raj%123  
get raj.txt`

**smb-enum-shares NSE Script**

This NSE scirpt attempts to list shares using the srvsvc.NetShareEnumAll MSRPC function and retrieve more information about them using srvsvc.NetShareGetInfo. If access to those functions is denied, a list of common share names are checked. Calling NetShareGetInfo requires an administrator account on all versions of Windows up to 2003, as well as Windows Vista and Windows 7 and 10, if UAC is turned down. Even if NetShareEnumAll is restricted, attempting to connect to a share will always reveal its existence. So, if NetShareEnumAll fails, a pre-generated list of shares, based on a large test network, are used. If any of those succeed, they are recorded. After a list of shares is found, the script attempts to connect to each of them anonymously, which divides them into “anonymous”, for shares that the NULL user can connect to, or “restricted”, for shares that require a user account.

`nmap --script smb-enum-shares -p139,445 192.168.1.17`

**Net view** \(windows\)

Displays a list of domains, computers, or resources that are being shared by the specified computer. Used without parameters, net view displays a list of computers in your current domain. This time we are on the Windows machine. We used the net view with the /All parameter to list all the shares on the target machine.

`net view \\192.168.1.17 /All`

**CrackMapExec** \(postexploitation\)

CrackMapExec \(a.k.a CME\) is a post-exploitation tool that helps automate assessing the security of large Active Directory networks. Built with stealth in mind, CME follows the concept of “Living off the Land”: abusing built-in Active Directory features/protocols to achieve its functionality and allowing it to evade most endpoint protection/IDS/IPS solutions. CrackMapExec can Map the network hosts, Generate Relay List, enumerate shares and access, enumerate active sessions, enumerate disks, enumerate logged on users, enumerate domain users, Enumerate Users by bruteforcing RID, enumerate domain groups, Enumerate local groups etc.

`crackmapexec smb 192.168.1.17 -u 'raj' -p '123' --shares`

### USERS

[https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/](https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/)

**Impacket: Lookupsid**

A Security Identifier \(SID\) is a unique value of variable length that is used to identify a user account. Through a SID User Enumeration, we can extract information about what users exist and their data. Lookupsid script can enumerate both local and domain users. There is a Metasploit module too for this attack. If you are planning on injecting a target server with a golden or a silver ticket then one of the things that are required is the SID of the 500 user. Lookupsid.py can be used in that scenario. When we provide the following parameters to the Lookupsid in such a format as shown below.

Requirements:  
• Domain  
• Username  
• Password/Password Hash  
• Target IP Address

`python3 lookupsid.py DESKTOP-ATNONJ9/raj:123@192.168.1.17`

### VULNERABILITIES

[https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/](https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/)

**smb-vuln NSE Script**

Nmap in past used to have a script by the name of smb-check-vulns. It used to scan the target server for the various vulnerabilities such as:  
• conficker  
• cve2009-3103  
• ms06-025  
• ms07-029  
• regsvc-dos  
• ms08-067

Then the script was divided into single vulnerability checks that can run individually such as smb-vuln-ms08-067. Hence to check all SMB vulnerabilities available in the Nmap Scripting Engine we use the \* with the script.

`nmap --script smb-vuln* 192.168.1.16`  


## OVERALL SCAN

[https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/](https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/)

Enum4linux

is a tool that is designed to detecting and extracting data or enumerate from Windows and Linux operating systems, including SMB hosts those are on a network. Enum4linux is can discover the following:  
• Domain and group membership  
• User listings  
• Shares on a device \(drives and folders\)  
• Password policies on a target  
• The operating system of a remote target

We start to normal scan using enum4linux. It extracts the RID Range, Usernames, Workgroup, Nbtstat Information, Sessions, SID Information, OS Information.

