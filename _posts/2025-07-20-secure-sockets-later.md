---
layout: post
title: SSL (Secure Sockets Later)
tags: CTF, Deloitte, Portal.Hackazon, SSL
date: 2025-07-20
---
<br>

## Overview
**SSL (Secure Sockets Later)e** Welcome to another post in our series of beginner-friendly challenges—we're getting close to the end! This time, we’ll cover essential OpenSSL concepts, how to connect using the tool, and most importantly, how it all works. And as always, we’ll stick to The Principle of Parsimony as our go-to method for solving challenges on this blog
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
      <td>SSL (Secure Sockets Later)</td>
      <td>We identified a strange service. Can you identify the flag in the service response?<br>
      The system can be reached on 10.6.0.2.<br>
      hint: you may need to replace two characters ;)</td>  
    </tr>
  </tbody>
</table>
## Tools
<ul>
<li>Kali linux (optional)</li>
<li>Nmap</li>
<li>openssl</li>
</ul>
## Enumeration
After establishing the VPN connection, properly enumerating the available protocols and services will be essential. Here are the key points to keep in mind:
<li>To list files and directories (using ffuf, wfuzz, feroxbuster, etc).</li>
<li>To check if the web application has a robots.txt file.</li>
<li>To review commented or hidden content within the front-end.</li>
<li>Test connections with other services.</li>

<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/secure-sockets-later/1.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/secure-sockets-later/2.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/secure-sockets-later/3.png)
We checked the main directories and important sections of the web application but didn’t find anything that stood out. Let’s move on to analyzing the challenge.

## Flag: SSL (Secure Sockets Later)
Based on the challenge name and the data collected during the reconnaissance phase, our approach should focus on interacting with an SSL service. The initial step is to test the connection using the **openssl** utility, specifically the **s_client** option, by providing the target IP address and port.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/secure-sockets-later/4.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/secure-sockets-later/5.png)
<br>
After connecting to the server with OpenSSL, we notice something unusual in the certificate—it contains the acronym 'CTF'. The challenge hints at replacing two characters, so we copy the string and swap '/' for '{}', which gives us the flag.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/secure-sockets-later/6.png)
**This is everything, I hope you enjoyed the write-up and learned something new. Happy hacking! :)**
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/secure-sockets-later/7.png)
<br>
### Reference links
<li>https://www.liquidweb.com/blog/how-to-test-ssl-connection-using-openssl/</li>
<li>https://medium.com/@MakeComputerScienceGreatAgain/demystifying-openssl-how-it-works-to-secure-your-data-ba2792474e1e</li>
