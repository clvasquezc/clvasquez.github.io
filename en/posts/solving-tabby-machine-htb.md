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
