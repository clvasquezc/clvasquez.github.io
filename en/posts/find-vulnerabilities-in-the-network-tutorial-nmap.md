[Spanish](https://emersontech.github.io/index.html)|[Home](https://emersontech.github.io/en/index.html)|[Articles](https://emersontech.github.io/en/nav/page1.html)|[Author](https://emersontech.github.io/en/nav/about.html)|[Linkedin](https://www.linkedin.com/in/emersontech/)

# Find Network Vulnerabilities // Nmap Tutorial for Hackers // EP 1
Nmap is the number one tool used by hackers, it is used to identify and scan systems on the network. It is an important part of network diagnostics and evaluation of network-connected systems. In this video, you will learn the basics of this tool and how it can be efficiently used to map the internal network by identifying active hosts and port scanning, service enumeration, and operating system detection.

<iframe width="560" height="315" src="https://www.youtube.com/embed/zoOAnbVplSI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard- write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Nmap CheatSheed
Below you will find a cheat sheet on some auditing techniques and recommendations for using the tool in C.T.F environments.

### Basic Scan.
```bash
nmap -oA <file-name> <target-ip>
```

### Scan All Ports.
```bash
nmap -p- <target-ip>
```

### Scan to Specific Ports.
```bash
nmap -p<ports> -oA <file-name> <target-ip>
```

### Port Range Scan.
```bash
nmap -p<from_number>,<to_number> <target-ip>
```

### Top 100 Port Scanning
```bash
nmap -F <target-ip>
```

### Scan All Ports.
```bash
nmap -p- -oA <file-name> <target-ip>
```

### Save scans in all formats (-oN, -oG, -oX).
```bash
nmap -oA <file-name> <target-ip>
```

### Scan Optimization Techniques.
```bash
nmap -n -T5 --open <target-ip>
```

### Scans For C.T.F - First Scan // Basic.
```bash
nmap --open -T5 -v -n <ip-target> -oA/oN/oG/oX <file-name>
```

### Scans To C.T.F - Second Scan // Scan To All Ports.
```bash
nmap -p- --open -T5 -v -n <ip-target> -oA/oN/oG/oX <file-name>
```

### Scans For C.T.F - Third Scan // Advanced Scan With More Details.
```bash
nmap -p- -sS --min-rate 5000 --open -vvv -n <ip-target> -oA/oN/oG/oX <file-name>
```

### Scans For C.T.F - Fourth Scan // Exploiting Service Versions And Their Vulnerabilities
```bash
nmap -sCV -p<port-number,range> -oA/oN/oG/oX <file-name>
```

### Social Media

![img](/img/linkedin.png)|[Linkedin](https://www.linkedin.com/in/emersontech/)|![img](/img/youtube.png)|[Youtube](https://www.youtube.com/channel/UChNTj2xNpEQiliMv-IJbWvQ)|![img](/img/github.png)|[Github](https://github.com/emersontech)
