[Spanish](https://emersontech.github.io/index.html)|[Home](https://emersontech.github.io/en/index.html)|[Articles](https://emersontech.github.io/en/nav/page1.html)|[Author](https://emersontech.github.io/en/nav/about.html)|[Linkedin](https://www.linkedin.com/in/emersontech/)

# Solving the HackTheBox Tabby Machine

![cover](/img/tabby/0.png)

## Summary
Tabby is an easy difficulty Linux machine. Website enumeration reveals a second website that is hosted on the same server under a different vhost. This website is vulnerable to Local File Inclusion. Knowledge of the operating system version is used to identify the location of the tomcat-users.xml file. This file produces credentials for a Tomcat user who is authorized to use the /manager/text interface. This is leveraged to implement a war file and load a webshell, which is in turn used to get a reverse shell. File system enumeration reveals a password-protected zip file, which can be downloaded and decrypted locally. The cracked password can be used to log into the remote machine as a low privileged user. However, this user is a member of the LXD group, which allows privilege escalation by creating a privileged container, in which the host file system resides. mounted. Eventually, access to the remote machine is gained as root using SSH.

### Recognition Phase
We start the recognition phase by launching a ping towards the target IP address (10.10.10.190) to check if there is a stable connection through an ICMP trace.

![img](/img/tabby/Untitled.png)

The machine throws a time to live = 63 (64) which indicates that we are facing a linux machine. If we perform a ping together with a traceroute, we verify that through an intermediary node -1 unit is subtracted from the TTL, making it 63.

![img](/img/tabby/Untitled (1).png)

Once the communication against our victim is verified, we carry out an enumeration with the nmap tool and pass the following parameters:

![img](/img/tabby/Untitled (2).png)

Continuing with the enumeration phase with the nmap tool, we launch reconnaissance scripts to have greater depth of information collection and launch them directly towards the open ports (80,22,8080):

![img](/img/tabby/Untitled (3).png)

We launch a whatweb, which is like a wappalyzer, which works by console to be able to analyze what technologies the web server is running:

![img](/img/tabby/Untitled (4).png)

If we analyze the previous screenshot we can see a domain name “megahosting.com” “megahosting.htb” which we are interested in investigating in greater depth. If we perform a ping we can see that it does not establish a connection:

![img](/img/tabby/Untitled (5).png)

It tells us that the name or service is unknown. This can happen because virtual hosting is being applied, which we can solve by modifying the /etc/hosts address:

![img](/img/tabby/Untitled (6).png)

> Note: This is important to do, since thanks to this we can achieve a successful path traversal, since the URL will have a valid content request to achieve the path traversal.

![img](/img/tabby/Untitled (7).png)

This is ideal to do, since it is not the same to access by domain than by IP address.

Since port 80 is open, this indicates that there is HTTP communication, which, when entering the IP address in the browser, we can enter the **MEGA-HOSTING website.**

![img](/img/tabby/Untitled (8).png)

Again we launch a whatweb directly specifying port 8080 to check the headers and we can see that an apache tomcat server is running:

![img](/img/tabby/Untitled (9).png)

Once again we return to the web to check which places within it we can navigate and discover attack vectors such as user input, configurations or de-sanitized code.

If we go to the same IP address and specify port 8080 we can see more content from the tomcat web:

![img](/img/tabby/Untitled (10).png)
![img](/img/tabby/Untitled (11).png)

Parallel to the previously found directory, we know that there is usually a login directory located in manager/html which we enter in the URL and successfully give a user input:

![img](/img/tabby/Untitled (12).png)
![img](/img/tabby/Untitled (13).png)

We try to test different login credentials to check for weak credentials, such as:

-admin:admin
- admin:admin123
- admin:password
- admin:password

But none reveal successful login.

Navigating in the "news" section we can see a message that talks about a data leak, if we analyze the URL (Uniform Resource Locator) we can notice the following:

![img](/img/tabby/Untitled (14).png)

The php code shows us the **file=statement** section, which when modified reveals a **LFI (local file inclusion)** vulnerability. As we are dealing with an LFI, we can navigate through the server's file system through a directory path traversal:

![img](/img/tabby/Untitled (14).png)
![img](/img/tabby/Untitled (15).png)

If we analyze the text in "Source View" mode we can see the data more clearly:

![img](/img/tabby/Untitled (17).png)

When reading the content we can notice a user named **“ash” and “root”.**

If we try to make a **path traversal** with the username of ash or root we do not successfully reach the directory, this could be because they are protected by passwords.

Browsing the file system, we don't find much content.

A search on google reveals us different paths to find the files we are looking for for the credentials and we successfully lead to the correct credentials:

![img](/img/tabby/Untitled (18).png)

When making a "source View Page" to the web page we can notice that the hidden source code (commented) user access credentials:

![img](/img/tabby/Untitled (19).png)
![img](/img/tabby/Untitled (20).png)

Again we return to the previous login panel to enter the credentials found **(tomcat:$secureP4s5w0rd123!)**, but it gives us an error code 403 - access denied.

Error 403 Forbidden is an HTTP status code that indicates that the server denies the requested action, web page, or service. In other words, the server could be contacted, and received a valid request, but denied access to the requested action.

![img](/img/tabby/Untitled (21).png)
![img](/img/tabby/Untitled (22).png)
![img](/img/tabby/Untitled (23).png)

Since the credentials we just entered **ARE CORRECT** but the server is not able to connect us, we perform an external google search to check alternative ways.

A search in google gives us quite useful information where we find a forum with the same problem and its solution:

![img](/img/tabby/Untitled (24).png)

[https://www.certilience.fr/2019/03/tomcat-exploit-variant-host-manager/](https://www.certilience.fr/2019/03/tomcat-exploit-variant-host-manager /)

![img](/img/tabby/Untitled (25).png)

The previous text is summarized in that we must modify the login URL and with the same credentials we should gain access to the tomcat panel:

![img](/img/tabby/Untitled (26).png)
![img](/img/tabby/Untitled (27).png)

At this point, our goal at this point is to find some way to introduce files to be able to introduce a **malicious application (.war)** in order to achieve a reverse shell.

As the web portal itself does not allow us to introduce any type of external file, we could do it through the console. To do this, we first list the existing applications:

![img](/img/tabby/Untitled (28).png)

So our objective now is to be able to upload our malicious application to get a reverse shell, for this, we use the msfvenom command in the console and ask it to give us a list of payloads where we are interested in getting a reverse shell. Keep in mind that this command helps us to list the existing payloads that could be used for our attacks:

![img](/img/tabby/Untitled (29).png)

Once our payload of interest is found, in this case it is java/jsp_shell_reverse_tcp, we generate our .war file, selecting our destination IP address and local port:

![img](/img/tabby/Untitled (30).png)
![img](/img/tabby/Untitled (31).png)

Since we cannot upload the files directly to the website, we perform an external search on google to find out how to enter external files through the console.

![img](/img/tabby/Untitled (32).png)

Again, if we list our existing applications, we can notice that our application with the name **“nuestropayload.war”** malicious now exists within the internal files:

![img](/img/tabby/Untitled (33).png)

If now in the URL we open our "revershell" file and establish communication with a netcat, we will obtain a successful communication:

![img](/img/tabby/Untitled (34).png)
![img](/img/tabby/Untitled (35).png)

Our next step is to do a TTY treatment. This is useful when it comes to winning a reverse shell on any machine as it helps us to manipulate the remote terminal in a much more efficient and comfortable way. To do this we will use the following commands:

> Note: More information to manipulate a TTY [Here](https://github.com/emersontech/tratamiento-de-tty/blob/main/tty.md)

### 1) Check that we have the connection established. Running the *whoami* command should return the hostname.
```bash
whoami
```
### 2) Launch an interactive bash (a pseudo console)

```bash
script /dev/null -c bash
```

### 3) Send the process that is running to the background

```bash
[CTRL + Z]
```

### 4) Resume the process previously left in the background (3)

```bash
stty raw -echo; fg
```

### 5) Restart the current configuration of the terminal (after this step we should already

### Social Media

![img](/img/linkedin.png)|[Linkedin](https://www.linkedin.com/in/emersontech/)|![img](/img/youtube.png)|[Youtube](https://www.youtube.com/channel/UChNTj2xNpEQiliMv-IJbWvQ)|![img](/img/github.png)|[Github](https://github.com/emersontech)
