---
layout: post
title: Web explotation - first steps
tags: CTF, Deloitte, Portal.Hackazon, WebApp
date: 2024-09-01
---
<br>

## Overview
**Web exploitation - First Steps** is a beginner's challenge for people taking their first steps in the world of the CTF. The challenge is not hard, but having a basic knowledge of web applications and browser tools would be helpful. In this challenge of the **Hackazon Cyber Academy**, we’re going to introduce concepts about debugging in the browser. In addition, we will introduce the concept of **the principle of parsimony**, which will be our methodology and guide to solving any challenge we share in this blog.
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
      <td>FIND THE HIDDEN FLAG #1</td>
      <td>There is a flag hidden somewhere on the page, can you find it?</td>
    </tr>
        <tr>
      <td>FIND THE HIDDEN FLAG #2</td>
      <td>There is a flag hidden somewhere in the server respons, can you find it? </td>
    </tr>
        <tr>
      <td>ACCESS THE SIGNUP FUNCTION</td>
      <td>It seems that the signup button is disabled, can you manage to click it any way?</td>
    </tr>
        <tr>
      <td>LOGIN TO FRED'S ACCOUNT</td>
      <td>Can you manage to login to Fred's account?</td>
    </tr>
        <tr>
      <td>BECOME AN ADMINISTRATOR</td>
      <td>Can you get access to the admin page?</td>
    </tr>
  </tbody>
</table>
<br>

## Tools
<ul>
<li>Browser</li>
<li>CyberChef (https://gchq.github.io/CyberChef/) (Not mandatory)</li>
<li>Burp Suite or Owasp Zap (optional)</li>
</ul>
<br>
## Enumeration
As this challenge has a web application-oriented focus, one of the first steps in listing web-based services would be to review the following points:

<li>To list files and directories (using ffuf, wfuzz, feroxbuster, etc).</li>
<li>To test for Default Credentials (such as admin:admin).</li>
<li>To check if the web application has a robots.txt file.</li>
<li>To review commented or hidden content within the front-end.</li>
<li>To search for information that may be useful in files such as JS, txt, old, bak, etc.</li>
<li>After going through the page, we will test with default credentials and see that there are no default users.</li>
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/1.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/2.png)

## Flag #1: FIND THE HIDDEN FLAG #1
The objective of this challenge will be to find the flag hidden somewhere in the application. If we apply what we have seen in **Enumeration**, one of the first things we could check would be the **page source** of our application or use **web developer tools** like **Inspector** to inspect and modify the front-end web elements. Resulting in the first flag as part of a hidden comment.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/3.png)

## Flag #2: FIND THE HIDDEN FLAG #2
The objective of this challenge is to find the hidden Flag but in this case, in a more specific location such as the HTTP Response. HTTP messages are how data is exchanged between a server and a client. There are two types of messages: 
<li>HTTP request: sent by the client to trigger an action on the server.</li>
<li>HTTP response: that are the answers from the server.</li>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/4.png)

Using the **Web developer tools**, we can see in the **Network** tab how the browser communicates with the server along with the headers that are part of the communication. Within these headers is our second Flag.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/5.png)

## Flag #3: ACCESS THE SIGNUP FUNCTION
We have already solved the easiest part so now we are going to add a degree of difficulty to our challenges. If we look at the title of the challenge we will see that the objective is to reach the SIGNUP function but from the portal, we see that the function is not accessible.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/6.png)

Using the **Web developer tools**, we see that the signup functionality is set with the attribute **disabled**. This means that it is not available at the front-end level.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/7.png)

If we remove the value of disabled from the signup functionality we see that from the front end, the option is accessible and when entering we get our third flag.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/8.png)

##  Flag #4: LOGIN TO FRED'S ACCOUNT
Our next challenge indicates that we need to authenticate with Fred's account but we are not sure what Fred's credentials are. However, by using the **Page source** we can find Fred's password.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/9.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/10.png)

When authenticating with the credentials, it tells us that the password is incorrect but the user is valid. Even if we try to decode the password using **Base64** or **HTML**, the result is the same.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/11.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/12.png)

Inside our **Web developer tools**, let's use the **Debugger tab** and analyze the login flow separating it into 3 blocks with the help of breakpoints.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/13.png)

```html
function decode(s) {
            s = s.replace(/[a-zA-Z]/g,function(c){return String.fromCharCode((c<="Z"?90:122)>=(c=c.charCodeAt(0)+13)?c:c-26);});
            s = atob(s);
            s = s.split("").reverse().join("");
            return s;
        }
        function signup() {
            window.location = decode("pUIhM2ymK3EypzAypj==");
            return true;
       }
       function checkLogin() {
            var valid = {'fred': 'MUWiq3AmLKOsqTIlL2ImK3yg'};
            var username = document.getElementById("username").value;
            var password = document.getElementById("password").value;
            var good_pw = valid[username];
            if (! good_pw) {
                alert("Unknown user");
                return false;
            }

            good_pw = decode(good_pw);

            if (good_pw == password) {
                window.location = '?password='+password;
                return false;
            } else {
                alert("Invalid password");
                return false;
            }
        }
```
Setting the breakpoints in the first block, we see that while debugging the variables username and password they are set with the values we have entered by keyboard while “good_pw” takes the value of valid which has the username and a hash.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/14.png)
<br>
Let's analyze the decode function of our second block which receives the hash “MUWiq3AmLKOsqTIlL2ImK3yg” inside the **s** parameter. In line n° 5 we see the value of the parameter **s** using the **ROT13 cipher**. Resulting in: 
<li>s = ZHJvd3NzYXBfdGVyY2VzX3lt</li>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/15.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/16.png)
<br>
Then in line n° 6 we see how the new value of **s** is passed by the **atob() function**. **The WindowBase64.atob() function** decodes a data string that had been encoded using base64. You can use the **window.btoa() method** to encode and transmit data that might otherwise cause communication problems. After being transmitted you can use the window.atob() method to decode the data again. Resulting in:
<li>s = drowssap_terces_ym</li>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/17.png)
<br>
Finally, we see how the function takes our string and applies a transformation to it. First, it converts the string into an array of characters with **split(“”)**, then it inverts the array with **reverse()**, and finally, it joins the characters back into a string with **join(“”)**. The result is:
<li>s = my_secret_password</li>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/18.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/19.png)
<br>
If we observe the last block, we can see how the value of “good_pw” is validated with the value of our password. If they are different, the function returns a message Invalid password.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/20.png)
<br>
If we modify the password value to “my_secret_password”, we see that the password is valid and allows us to authenticate with the user Fred resulting in the fourth flag of our series of challenges.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/21.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/22.png)

## Flag #5: BECOME AN ADMINISTRATOR
As a last challenge, we are going to rely on all the tools and methodologies already seen to become an administrator user. If we go to the admin portal, it returns an access denied message and, at the same time, the admin portal does not have a login then our authentication approach should be re-oriented.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/23.png)
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/24.png)

If we use the **Web developer tools**, in the **Storage** tab we have all the cookies used by our web applications. In the list of cookies, there is one called admin whose value is False.
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/25.png)

As we saw in the result of the third flag, it is possible to modify or delete the value of our attributes and cookies are no exception. By modifying the value of our cookie to True and then refreshing the page, we see that it was possible to access the admin portal and get the last flag of our series of challenges.
<br>
This is everything, I hope you enjoyed the write-up and learned something new. 
Happy hacking! :)
<br>
<br>
![](https://raw.githubusercontent.com/InfoSecGopNik/CTF/main/_posts/images/Web_exploitation_first_steps/26.png)


### Reference links
<li>https://developer.mozilla.org/en-US/docs/Web/HTTP/Messages</li>
<li>https://developer.chrome.com/docs/devtools/network/reference</li>
<li>https://javascript.info/debugging-chrome</li>
<li>https://dev.to/xmohammedawad/simplifying-text-encryption-with-rot13-algorithm-in-javascript-2am2</li>
<li>https://www.freecodecamp.org/news/what-is-base64-encoding/</li>
