[Spanish](https://emersontech.github.io/index.html)|[Home](https://emersontech.github.io/en/index.html)|[Articles](https://emersontech.github.io/en/nav/page1.html)|[Author](https://emersontech.github.io/en/nav/about.html)|[Linkedin](https://www.linkedin.com/in/emersontech/)

# Learn How to Find Hidden Directories on the WebSites

## What is the Content Discovery?
First of all, we must ask ourselves, in the context of web application security, what is content? Content can be many things,
a file, a video, an image, a backup, a website feature. When we talk about content discovery, we are not referring to the obvious things that we can see on a website; they are the things that are not immediately presented to us and that were not always intended for public access.
This content could be, for example, pages or portals intended for staff use, previous versions of the website, backup files, configuration files, administration panels, etc.
There are three main ways to discover content on a website that we will cover. Manually, Automated and OSINT (Open-Source Intelligence).

## Manual Discovery - Robots.txt
There are several places we can manually check on a website to start discovering more content, such as the Robots.txt file.

The robots.txt file is a document that tells search engines which pages they can and cannot show in their results.
search engine or prohibit specific search engines from crawling the website entirely. It may be common practice to restrict certain
areas of the website from being displayed in search engine results. These pages can be areas such as administration portals or
files intended for website customers. This file gives us a big list of locations on the website that owners don't want
that we discover as penetration testers.

## Manual Discovery - Favicon
The favicon is a small icon displayed in the browser's address bar or tab that is used to bookmark a website.
Sometimes when using frameworks to create a website, there is a leftover favicon that is part of the setup, and if the website developer doesn't
replace with a custom one, this can give us a clue as to which framework is in use. OWASP hosts a database of common frame icons that
you can use to compare them to the target favicon https://wiki.owasp.org/index.php/OWASP_favicon_database. Once we know the framework stack,
we can use external resources to discover more about it (see the next section).

## Manual Discovery - Sitemap.xml
Unlike the robots.txt file, which restricts what search engine crawlers can see, the sitemap.xml file provides a
list of all the files that the website owner wants to include in a search engine. Sometimes these may contain areas of the website in
the ones that are a bit harder to navigate or even list some old web pages that the current site no longer uses but still work behind
scene.

## Manual Discovery - HTTP Headers
When we make requests to the web server, the server returns various HTTP headers. These headers can sometimes contain useful information,
such as the web server software and possibly the programming/script language in use. In the following example, we can see that the web server is
NGINX version 1.18.0 and running PHP version 7.4.3. Using this information, we could find vulnerable versions of the software that is being used. Try running the following curl command on the web server, where the -v switch enables verbose mode, which will output the headers (might be something interesting!).

## Manual Discovery - Framework Stack
Once you have established the framework of a website, either from the favicon example above or by looking for clues in the page source such as comments, copyright notices, or credits, you can locate the framework website. From there, we can learn more about the software and other information, possibly leading to more content for us to discover.

## OSINT - Google Hacking/Dorking
There are also external resources available that can help you discover information about your target website; these resources are often called OSINT or (Open Source Intelligence) as they are freely available tools that collect information:

Google Hacking / Dorking
Google hacking / Dorking uses the advanced features of the Google search engine, which allow you to select personalized content. You can, for example, select results from a certain domain name using the site: filter, for example (site:tryhackme.com) and then you can match this to certain search terms, say for example the word admin (site :tryhackme.com admin) this would only return results from the tryhackme.com website that contain the word admin in their content. You can also combine multiple filters. Here is an example of more filters you can use:

| Filter | Example | Description |
| ------------- | ------------- | ------------- |
| website | site:linkedin.com | returns results only from the specified website address |
| inrule | inurl:admin | returns results that have the specified word in the URL |
| filetype | filetype:pdf | returns results that are a particular file extension |
| untitled | intitle:admin | returns results that contain the specified word in the title |

## Automated Discovery
*What is automated discovery?*
Automated discovery is the process of using tools to discover content instead of doing it manually. This process is automated as it typically contains hundreds, thousands, or even millions of requests to a web server. These requests check to see if a file or directory exists on a website, giving us access to resources we previously didn't know existed. This process is possible through the use of a resource called word lists.

*What are word lists?*
Word lists are just text files that contain a long list of commonly used words; they can cover many different use cases. For example, a list of password words would include the most used passwords, whereas in our case we are looking for content, so we would need a list containing the most used file and directory names. An excellent resource for word lists that is pre-installed in THM AttackBox is https://github.com/danielmiessler/SecLists, which is curated by Daniel Miessler.

*Automation tools*.
Although there are many different content discovery tools available, all with their features and flaws, we are going to cover three that are pre-installed on our attack box, *ffuf, dirb, and gobuster.*

*Using ffff*
```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://<target-ip>/FUZZ
```
* ffuf = Name of the tool
* -w = Specify path of our wordlist, can be from SecList or Dirbuster file
* -u = Specify the URL (Uniform Resource Locator)

*Using dirb*
```bash
dirb http://<target-ip>/ /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```
* dirb = tool name

*Using gobuster*
```bash
gobuster dir --url http://<target-ip>/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```
* gobuster = Name of the tool
* dir --url = Specify the URL
* -w = Specify path of our wordlist, can be from SecList or Dirbuster file

*Using Wfuzz (my personal recommendation)*
```bash
wfuzz -c --hc=404 -t200 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt http://<target-ip>/FUZZ
```
* wfuzz = Name of the tool
* -c = Output (output) better representative, the output is more readable
* --hc=404 = To hide status codes (hide code), in this case, for example 404
* -t 200 = 200 thread timer

### Social Media

![img](/img/linkedin.png)|[Linkedin](https://www.linkedin.com/in/emersontech/)|![img](/img/youtube.png)|[Youtube](https://www.youtube.com/channel/UChNTj2xNpEQiliMv-IJbWvQ)|![img](/img/github.png)|[Github](https://github.com/emersontech)
