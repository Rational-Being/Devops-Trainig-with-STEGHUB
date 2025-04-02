# Spoofing

Impersonation of a user or system
Aaronic Energy Ltd linkedin page is an unclaimed page ,this will allow an attacker to impersonate the company and trick partners and customers in scam jobs stock sles etc

https://www.linkedin.com/company/aaronic-energy-ltd/about/

 

### Assets Discovery:

Using the following website to discover subdomians/other assets Acronic has on the internet

https://securitytrails.com/list/apex_domain/aaronic.com.ng

I was able to discover the following:

webdisk.aaronic.com.ng
ftp.aaronic.com.ng
whm.aaronic.com.ng
mail.aaronic.com.ng
cpanel.aaronic.com.ng
aaronic.com.ng
webmail.aaronic.com.ng
www.aaronic.com.ng
http://cpcalendars.aaronic.com.ng/


### Port Scanning
naabu installation: Run this command in your terminal (make sure you have go/golang installed)
go install -v github.com/projectdiscovery/naabu/v2/cmd/naabu@latest    

The run the following commmand in yur terminal (make sure you also have nmpa installed):
 - `naabu -p - -host aaronic.com.ng -nmap-cli 'nmap -sV -oX nmap-output'`


PORT      STATE SERVICE      VERSION
 - 21/tcp    open  ftp          Pure-FTPd
 - 80/tcp    open  http
 - 110/tcp   open  pop3         Dovecot pop3d
 - 143/tcp   open  imap         Dovecot imapd
 - 443/tcp   open  ssl/https
 - 465/tcp   open  ssl/smtp     Exim smtpd 4.96.2
 - 587/tcp   open  submission?
 - 993/tcp   open  imaps?
 - 1624/tcp  open  ssh          OpenSSH 7.4 (protocol 2.0)
 - 2078/tcp  open  ssl/http     cPanel httpd (unauthorized)
 - 2082/tcp  open  infowave?
 - 2083/tcp  open  ssl/radsec?
 - 2086/tcp  open  gnunet?
 - 2095/tcp  open  nbx-ser?
 - 2096/tcp  open  ssl/nbx-dir?
 - 49925/tcp open  unknown


## Potential Exploitation possible leveraging the information gatherd during asset discovery and port scanning

1. cpcalendars.aaronic.com.ng returns `403 forbidden` http status code- 
Mitigation: It should taken off the internet for proper protection since we don't want it accessible in the first place.

2. Accessible administrator Cpanels - Though crediential stuffing and login bruteforce proofed in effective.
  - https://aaronic.com.ng:2083/
  - https://aaronic.com.ng:2082/

![image](https://github.com/user-attachments/assets/06d264f5-404f-4057-a732-ecd46dceb04d)

  - https://aaronic.com.ng:2078/ - This port uses **Authorization Bearer** as a means of authentication which is deprecated.
    
  ![image](https://github.com/user-attachments/assets/f7ed1aa6-7f73-4e4e-b415-f19c9c9a3a3e)


3. Open FTP port: The ftp port is opened on Port 21, anonymous user is configured (its password is not yet known) - this against security best practices
   
  ![image](https://github.com/user-attachments/assets/fd49ee80-2bb0-44e4-89cc-816f9ad17089)

4. While the SSH port is opened at an obscure port 1624, it was still easiliy fingerprinted using default nmap configurarion combined with naabu in the command presented in the asset discovery section
   An attacker could still connect over SSH as the anounymous user is enabled, though an attaempt to bruteforce the password proved unproductive
   
   ![image](https://github.com/user-attachments/assets/223ba0a5-4c1b-4792-ac75-66c47d0fd6a3)

   Mitigation: Avoid security bad practice i.e. security through obsurity
   
5. Also, attacker can connect over telnet to the POP3 service and send valid POP3 commands; again here authentication was not successful but exposure is a bad idea.
   
![image](https://github.com/user-attachments/assets/37cfa6e4-f334-4b02-85b0-c608566946a0)

In the screenshot above, the highlighted parts are the commands sent and their reponses form the server.
## Pop3 port 110 is opened, and it is connectable through telnet

```
telnet aaronic.com.ng 110                                 ─╯
Trying 188.165.52.14...
Connected to aaronic.com.ng.
Escape character is '^]'.
+OK Dovecot ready.
USER
+OK
PASS
-ERR [AUTH] Authentication failed.
USER
+OK
ADMIN
-ERR Unknown command.
PASS
-ERR [AUTH] Authentication failed.
-ERR Unknown comma 
-ERR Unknown command.
USER admin
+OK
PASS password
-ERR [AUTH] Authentication failed.
```

## Information Disclosure – Unauthorized access to sensitive data


When a user clicks on `GET STARTED` on the Homepage of the website, a form is presented to the user and the form is submitted to https://formspree.io/f/moqggaar which is third party website not owned by Aaronic. therefore, users PII (Personally Identifiable Information) is accessible by the adminstrors of formspree.io

In this case of supply chain attack on Aaronic or a direct breach at formspree, Aaronic will be impacted, therefore Aaronic should device a way to securely save there own users data.

The PII exposed are:
email
phoneNumber


# Denial of Service (DoS) – Disrupting system availability.

There is no rate limiting in place (both on the network level and applicaiton level (i.e. web service)) to throttle too much traffic from a single user, therefore an attacker could use a Botnet to DOS the server. An example of such tool is Burpsuite intruder.

![image](https://github.com/user-attachments/assets/55197344-174b-4f52-8f03-865ea2351fbb)


## Elevation of Privilege – Gaining unauthorized access or higher privileges.


As earlier mentioned in the asset discovery section, because the following ports opened, lateral movement is possible:
https://aaronic.com.ng:2083/
https://aaronic.com.ng:2087/

An attacker could perform credential stuffing or bruteforce attack to successful guess the password, takeover administrator accounts and modify resources on the website/web server.






