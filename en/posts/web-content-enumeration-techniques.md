# Learn to Find Hidden Directories on the Web

## What is Content Discovery?
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
