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
      <td>Can you find and connect to the SSH service on the 10.6.0.2 server?. **Hint: The username is admin.**</td>
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
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/10.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/11.png)
<br>
Although we successfully accessed the admin panel, there were no visible elements or functionalities allowing authentication with valid credentials. However, by re-examining the page using the **browser’s developer tools**, we find the second flag embedded in the front-end code.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/12.png)
## Flag #3: Connect to the TCP service
The third challenge involves retrieving a flag via direct interaction with a TCP service. According to the Nmap results, the host is running a service on port 8080. Using Ncat, we connect to this port with **nc IP PORT** and receive a prompt indicating that the keyword flag must be submitted to proceed. Given that this is a web service, we then send a manual HTTP GET request to /flag using nc, which returns the third flag.

![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/13.png)
## Flag #4: Connect to the SSL service
The fourth flag indicates that it is accessible through an SSL-enabled service. According to our Nmap scan, port 443 is open and running such a service, prompting us to access it via a browser. Upon connection, we're asked to accept an SSL certificate—likely self-signed or issued for a different domain than the target IP.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/14.png)
<br>
After accepting the SSL certificate, we observe that the service resembles the one previously accessed over TCP. We then attempt to access the /flag endpoint via the browser, which results in the successful retrieval of the fourth flag.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/15.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/16.png)
## Flag #5: Connect to the SSH service
Almost at the end, the challenge asks us to get the flag through SSH. This involves a brute-force approach, where we try different username and password combinations. While there are many tools to do this, we'll use the ssh_login module from Metasploit.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/17.png)
<br>
To proceed, we configure the required parameters and select a typical username such as **admin** or **root**, commonly present in SSH services. Upon running the attack with a password dictionary, we find that the **admin** user is using a default password, which is successfully discovered by the module.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/18.png)
<br>
By accessing the service via the command-line interface with the discovered credentials, we can successfully retrieve the flag.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/19.png)
## Flag #6: Connect to the FTP service
Finally, we need to retrieve the flag from an FTP service. While FTP typically runs on port 21 by default, it's always a good practice to confirm this using the Nmap results. We'll attempt to connect to the service via the CLI. As an initial test, we'll check whether the **anonymous** user is enabled, as many FTP servers are configured to allow this by default. After successfully authenticating as the **anonymous** user without a password, listing the server's contents reveals our final flag.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/20.png)
<br>
**This is everything, I hope you enjoyed the write-up and learned something new. Happy hacking! :)**
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/starter-ctf-challenge/21.png)
<br>
### Reference links
<li>https://security.stackexchange.com/questions/203263/tcp-scans-vs-syn-scans-in-port-scanning</li>
<li>https://www.digitalocean.com/community/tutorials/how-to-use-netcat-to-establish-and-test-tcp-and-udp-connections</li>
<li>https://superuser.com/questions/392509/open-a-test-tcp-connection-to-a-specified-ip-port</li>
<li>https://www.techtarget.com/whatis/definition/anonymous-FTP-File-Transfer-Protocol</li>
<li>https://www.offsec.com/metasploit-unleashed/scanner-ssh-auxiliary-modules/</li>
<li>https://blog.confirm.ch/tcp-connection-states/</li>
<li>https://explainshell.com/explain?cmd=nmap+-Pn+-sSV+-p-+--min-rate%3D5000+-oG+allports+--open+%24IP</li>
<li>https://www.cloudflare.com/learning/bots/what-is-robots-txt/</li>
