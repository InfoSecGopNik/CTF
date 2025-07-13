---
layout: post
title: Starter CTF challenge
tags: CTF, Deloitte, Portal.Hackazon, WebApp, SSL, TCP, SSH, FTP
date: 2025-07-12
---
<br>
## Overview
**Starter CTF challenge** Continuing the series that launched this blog, we're introducing another beginner-level challenge. In this exercise, we'll apply the concepts covered in Web Exploitation – First Steps and expand our focus to analyzing other protocols, such as SSL, SSH, and FTP. As always, we'll rely on The Principle of Parsimony as our guiding methodology to approach and solve every challenge we share on this blog.
<br>
## Challenge Info
<table>
  <thead>
    <tr>
      <th>Flag name </th>
      <th>Challenge description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Website login</td>
      <td>Can you login to the website on the 10.6.0.2 server?</td>
    </tr>
        <tr>
      <td>Admin panel</td>
      <td>Can you find the admin panel on the 10.6.0.2 server?</td>
    </tr>
        <tr>
      <td>Connect to the TCP service</td>
      <td>Can you find and connect to the TCP service on the 10.6.0.2 server?</td>
    </tr>
        <tr>
      <td>Connect to the SSL service</td>
      <td>Can you find and connect to the SSL service on the 10.6.0.2 server?</td>
    </tr>
        <tr>
      <td>Connect to the SSH service</td>
      <td>Can you find and connect to the SSH service on the 10.6.0.2 server? Hint: The username is admin.</td>
    </tr>
        <tr>
      <td>Connect to the FTP service</td>
      <td>Can you find and connect to the FTP service on the 10.6.0.2 server?</td>
    </tr>
  </tbody>
</table>
<br>
## Tools
<ul>
<li>Browser</li>
<li>Nmap</li>
<li>Ncat</li>
<li>Kali linux (optional)</li>
<li>CyberChef (https://gchq.github.io/CyberChef/)</li>
<li>Metasploit (optional)</li>
<li>SecLists (https://github.com/danielmiessler/SecLists)</li>  
</ul>
<br>
## Enumeration
For this challenge, the key will be thorough enumeration at the protocol and service level. To achieve this, we must take the following points into account:
<li>To list files and directories (using ffuf, wfuzz, feroxbuster, etc).</li>
<li>To test for Default Credentials (such as admin:admin).</li>
<li>To check if the web application has a robots.txt file.</li>
<li>To review commented or hidden content within the front-end.</li>
<li>Test connections with other services.</li>
<li>List vendors and software versions.</li>
<br>
As a first step, we'll perform a port scan using Nmap with the following command:
```html
nmap -sSV -p- –min-rate 5000 <IP>
```
The -sS option performs a SYN scan, which—unlike a full TCP connect scan—does not complete the three-way handshake. The -sV option enables service version detection through banner grabbing. Since both are part of the -s family of options, Nmap allows them to be combined as -sSV. Additionally, the -p- flag tells Nmap to scan all 65,535 TCP ports. Finally, the --min-rate 5000 option is used to send packets as quickly as possible, which is especially useful when scanning all ports. If you're only targeting specific ports or using a common port list, the impact of this option is minimal.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/1.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/2.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/3.png){: .align-center}
## Flag #1: Website login
In the first challenge, we’re presented with a web application that asks for a username, a password, and a value called 'Secret'. However, the 'Secret' field is not available for the user to fill in.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/4.png)
<br>
Using the **browser's developer tools**, we'll inspect the 'Secret' field and observe a string that is likely encoded.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/5.png)
<br>
After copying the encoded value, we analyze it with CyberChef and determine that it was encoded using Base64.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/6.png)
<br>
Now that we’ve obtained the 'Secret' value, we’ll enable the input field by removing the 'disabled' attribute in the front end, and then test the form using any username and password along with the decoded Secret.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/7.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/8.png)
<br>
We successfully captured the flag for the first challenge because the form only checks the 'Secret' value and ignores the username and password.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/9.png)
## Flag #2: Admin panel
The objective of the second challenge is to access the admin panel. From our prior enumeration, we identified a potentially interesting directory disclosed in the robots.txt file. We then copy the path and navigate to it directly.
