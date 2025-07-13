---
layout: post
title: Call an Ambulance
tags: CTF, Deloitte, Portal.Hackazon, WebApp, SSL
date: 2025-06-08
---
<br>

## Overview
**Starter CTF challenge** Continuing the series that launched this blog, we're introducing another beginner-level challenge titled Starter CTF Challenge. In this exercise, we'll apply the concepts covered in Web Exploitation – First Steps and expand our focus to analyzing other protocols, such as SSL, SSH, and FTP. As always, we'll rely on The Principle of Parsimony as our guiding methodology to approach and solve every challenge we share on this blog.
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
