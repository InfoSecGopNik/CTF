---
layout: post
title: Call an Ambulance
tags: CTF, Deloitte, Portal.Hackazon, WebApp, SSL
date: 2025-06-08
---

## Overview
**Call an Ambulance** challenge focuses on exploiting outdated SSL protocol configurations. This easy challenge will allow us to analyze and take advantage of a known SSL vulnerability. We will also explore how to use Metasploit modules and known exploits to carry out the attack.
<br>
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
      <td>Call an Ambulance</td>
      <td>Our server is vulnerable to a well known attack. What was it called? Shellshock? Poodle?. The system can be accessed at 10.6.0.2.</td>
    </tr>
  </tbody>
</table>
## Tools
<ul>
<li>Nmap</li>
<li>SSLScan</li>
<li>Metasploit</li>
<li>Searchexploit</li>
<li>Kali linux (optional)</li>
<li>Browser (optional)</li>
</ul>
## Enumeration
We will start by enumerating protocols and services, focusing on the following points:
<li>To list directories (using ffuf, wfuzz, feroxbuster, etc).</li>
<li>To check if the web application has a robots.txt file.</li>
<li>To review commented or hidden content within the front-end.</li>
<li>List vendors and software versions.</li>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/1.png){: .align-center}
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/2.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/3.png)

## Flag: Call an Ambulance
After completing the enumeration phase and reviewing the web application, we didn’t find anything noteworthy. However, the challenge description mentions several SSL/TLS configuration vulnerabilities.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/4.png)
<br>
To check if the challenge is related to SSL/TLS protocol vulnerabilities, let's run sslscan and review the application's configuration.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/5.png)
<br>
Indeed, our web application supports weak protocols and ciphers such as TLS 1.0, TLS 1.1, and SSLv3. We have confirmed that the application is not vulnerable to Shellshock. Possible remaining options include POODLE and Heartbleed—both are known vulnerabilities that affect obsolete protocols. Let’s verify whether the server is vulnerable to Heartbleed using a Metasploit auxiliary module.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/6.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/7.png)
<br>
When running the auxiliary module in SCAN mode, we observe that the web application is vulnerable to Heartbleed. Next, we switch the module to DUMP mode, which allows us to perform a memory dump and retrieve sensitive information from the application. By running the memory dump, we are able to extract the flag string
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/8.png)

## Manual exploitation
As we have seen, it is possible to exploit Heartbleed using Metasploit modules. But what if we want to exploit it manually? To do so, we can search for available exploits using searchsploit (if we are on Kali Linux) or by checking online resources (see reference links). When performing the search, we find several options, but the one we are most interested in is “Memory Disclosure.” We execute it by passing the target IP address and port as parameters, and it returns a memory dump containing the flag.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/9.png)

## Understanding Heartbleed
We’ve already seen how to detect and confirm the presence of the Heartbleed vulnerability using Metasploit or known exploits. But do you know why this vulnerability exists? What is the origin of Heartbleed?. Well, now we’re going to explain how Heartbleed originated and what key components make it so dangerous.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/10.png){: .align-center}
<br>
Heartbleed is believed to have existed before 2012, but it was Google researcher **Neel Mehta** who confirmed that the vulnerability could be used to access private data even in updated systems. Heartbleed, identified as **CVE-2014-0160**, is a vulnerability that allows an attacker to read up to 64 KB of memory per request from any connected client or server. The name **“Heartbleed”** comes from the fact that the flaw lies in the OpenSSL implementation of the Heartbeat extension for the TLS and DTLS protocols, as defined in **RFC 6520**.

Let’s analyze the structure of the vulnerability using a TLS (Transport Layer Security) communication channel between a client and a server as an example.
The client sends a message **«Hello»** along with a piece of data that specifies the length of the message, in this case, **«5»** characters.
The server receives this information and responds with the same message, as expected. This is a typical Heartbeat communication.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/11.png)

Now we’re going to try to corrupt memory by modifying the message size to read **«23»** characters **«18»** more than the actual **«Hello»** message.
Due to a flaw in the code, the server accepts the message and does not validate the declared size in the response. As a result, it replies with the original **«Hello»** message plus 18 additional bytes from whatever happens to be in memory at that moment.
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/12.png)

## What versions of the OpenSSL are affected?
<ul>
<li>OpenSSL 1.0.1 through 1.0.1f (inclusive) are vulnerable.</li>
<li>OpenSSL 1.0.1g is NOT vulnerable.</li>
<li>OpenSSL 1.0.0 branch is NOT vulnerable.</li>
<li>OpenSSL 0.9.8 branch is NOT vulnerable.</li>
</ul>

**This is everything, I hope you enjoyed the write-up and learned something new. Happy hacking! :)**
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/call_an_ambulance/13.png)
<br>
### Reference links
<li>https://www.exploit-db.com/</li>
<li>https://heartbleed.com/</li>
<li>https://nordvpn.com/blog/what-is-heartbleed-bug/</li>
<li>https://blog.httpcs.com/en/heartbleed-openssl-explanation-and-exploit/</li>
<li>https://www.pcmag.com/news/heartbleed-https://www.invicti.com/blog/web-security/the-heartbleed-bug/how-it-works</li>
<li>https://www.invicti.com/blog/web-security/the-heartbleed-bug/</li>
<li>https://github.com/adamalston/Heartbleed</li>
