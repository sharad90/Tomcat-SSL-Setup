# Tomcat-SSL-Setup
 Quick intro to Secure Sockets Layer (SSL) and how to implement SSL in Apache Tomcat Server.
 
###### I have implemented SSL on many occasions in numerous Tomcat App hosted web applications. In this guide we will learn about SSL and how to enable/implement SSL in Apache Tomcat Server.

## SECURE SOCKETS LAYER

## 1.	DEFINITION

SSL (Secure Sockets Layer) is a standard security technology for establishing an encrypted link between a server and a client, typically a web server (website) and a browser or a mail server and a mail client (e.g. Outlook).
SSL allows sensitive information such as credit card numbers, social security numbers, and login credentials to be transmitted securely. Normally, data sent between browsers and web servers is sent in plain text leaving you vulnerable to eavesdropping. If an attacker is able to intercept all data being sent between a browser and a web server they can see and use that information.
More specifically, SSL is a security protocol. Protocols describe how algorithms should be used. In this case, the SSL protocol determines variables of the encryption for both the link and the data being transmitted. 

## 2.	 HOW SSL WORKS

The SSL protocol includes two sub-protocols:  the record protocol and the handshake protocol. These protocols allow a client to authenticate a server and establish an encrypted SSL connection. In what's referred to as the "initial handshake process," a server that supports SSL presents its digital certificate to the client to authenticate the server's identity. Server certificates follow the X.509 certificate format that is defined by the Public-Key Cryptography Standards (PKCS). The authentication process uses public-key encryption to validate the digital certificate and confirm that a server is in fact the server it claims to be. Once the server has been authenticated, the client and server establish cipher settings and a shared key to encrypt the information they exchange during the remainder of the session. This provides data confidentiality and integrity. This whole process is invisible to the user. For example, if a webpage requires an SSL connection, the URL will change from HTTP to HTTPS and a padlock icon appears in the browser once the server has been authenticated.
<p align="center">
  <img src="/how-ssl-works.png">
</p> 
The handshake also allows the client to authenticate itself to the server. In this case, after server authentication is successfully completed, the client must present its certificate to the server to authenticate the client's identity before the encrypted SSL session can be established.

## 3.	WHAT IS A SSL CERTIFICATE

All the modern browsers have the capability to interact with secured web servers using the SSL protocol. However, the browser and the server need what is called a SSL Certificate to be able to establish a secure connection.

**3.1 HOW SSL CERTIFICATE WORKS**

SSL Certificates have a key pair: a public and a private key. These keys work together to establish an encrypted connection. The certificate also contains what is called the “subject” which is the identity of the certificate/website owner.
To get a certificate, you must create a Certificate Signing Request (CSR) on your server. This process creates a private key and public key on your server. The CSR data file that you send to the SSL Certificate issuer (called a Certificate Authority or CA) contains the public key. The CA uses the CSR data file to create a data structure to match your private key without compromising the key itself. The CA never sees the private key.
Once you receive the SSL Certificate, you install it on your server. You also install an intermediate certificate that establishes the credibility of your SSL Certificate by tying it to your CA’s root certificate. The instructions for installing and testing your certificate will be different depending on your server.
The most important part of an SSL Certificate is that it is digitally signed by a trusted CA like GSK CA, DigiCert and Comodo etc. Anyone can create a certificate, but browsers only trust certificates that come from an organization on their list of trusted CAs. Browsers come with a pre-installed list of trusted CAs, known as the Trusted Root CA store. In order to be added to the Trusted Root CA store and thus become a Certificate Authority, a company must comply with and be audited against security and authentication standards established by the browsers.
An SSL Certificate issued by a CA to an organization and its domain/website verifies that a trusted third party has authenticated that organization’s identity. Since the browser trusts the CA, the browser now trusts that organization’s identity too. The browser lets the user know that the website is secure, and the user can feel safe browsing the site and even entering their confidential information.
<p align="center">
  <img src="/what-is-a-ssl-cert.png">
</p> 

**3.2 HOW SSL CERTIFICATE CREATE A SECURE CONNECTION**

Whenever a browser attempts to access a website that is secured by SSL, the browser and the web server establish an SSL connection using a process called an “SSL Handshake” (see diagram below). Note that the SSL Handshake is invisible to the user and happens instantaneously.
<p align="center">
  <img src="/how-ssl-cert-create-secure-connection.png">
</p> 
Essentially, three keys are used to set up the SSL connection: the public, private, and session keys. Anything encrypted with the public key can only be decrypted with the private key, and vice versa.
Because encrypting and decrypting with private and public key takes a lot of processing power, they are only used during the SSL Handshake to create a symmetric session key. After the secure connection is made, the session key is used to encrypt all transmitted data.

1.	Browser connects to a web server (website) secured with SSL (https). Browser requests that the server identify itself.

2.	Server sends a copy of its SSL Certificate, including the server’s public key.

3.	Browser checks the certificate root against a list of trusted CAs and that the certificate is 
unexpired, unrevoked, and that its common name is valid for the website that it is connecting to. If the browser trusts the certificate, it creates, encrypts, and sends back a symmetric session key using the server’s public key.

4.	Server decrypts the symmetric session key using its private key and sends back an acknowledgement encrypted with the session key to start the encrypted session.

5.	Server and Browser now encrypt all transmitted data with the session key.

## 4.	SSL CONFIGURATION IN WEB APPLICATIONS

In general the way SSL is configured is entirely dependent on the technology in which Web Application under consideration is developed/running on. Primarily Java based applications running on various servers like Apache Tomcat, JBOSS & Glassfish etc have their own specification of configuring SSL. Also .Net based applications often running on IIS has its own specification of configuring SSL depending upon the IIS version. It is beyond the scope of this paper to cover each & every server specific steps to setup SSL. However steps to configure SSL & install Certificates on Java based Web Application running with Apache Tomcat Server are described in following section.  

**4.1 STEPS TO CONFIGURE SSL IN APACHE TOMCAT SERVER**

Setting up an https (SSL) connection in Web Server can often be tricky hence the steps described below should be tested before deploying on Production Server:

| STEPS | OUTPUT |
| --- | --- |
| **1: BACKUP THE EXISTING SERVER.XML FILE:-** <br>A backup copy of server.xml should be kept available at a directory on server before proceeding for further steps. Currently server.xml file can be located in following directory \TomCat\conf\server.xml”| server.xml file should be copied & made available to a directory of your choice |
|**2: CREATE KEYSTORE FILE ON SERVER:-** <br>1. Open a command prompt (click Start - Run - and type in cmd and click OK) <br>2. Change directory to the location of the Java executables (e.g. 'C:\Program Files (x86)\Java\jre6\bin' <br>3. Use the keytool command to create a new keystore:<br>keytool -genkey -alias [youralias] -keyalg RSA -keystore [/preferred/keystore/path]. <br>After executing the above command it will ask for password for keystore, please provide a password & record it safely. <br>After this you will be asked to enter some required information for the certificate. <br>First and last Name: <br>Organizational unit: <br>Organizational: <br>City or Locality: <br>State or Province: <br>Two-letter country code: <br>Leave blank the Key Password, just press enter & it will use previous password.| youralias.keystore file should be created in the directory: [/preferred/keystore/path] |
|**3: CREATE CERTIFICATE SIGNING REQUEST:-**<br>We have keystore file created above, now we will create the certificate signing request(CSR) to have it sent to the Certificate Authority for signing. Create the CSR for Webserver:<br>•	Log on to the server <br>•	Open a command prompt (start->run->cmd)<br>•	Change directory to the location of the Java executables (e.g. 'C:\Program Files (x86)\Java\jre6\bin'<br>•	Use the keytool command again to create the certificate request:<br>keytool -certreq -keyalg RSA -alias [youralias] -file [yourcertificatname].csr -keystore [path/to/your/keystore]|yourcertificatname.csr file should be created |
|**4: GENERATE SSL CERTIFICATE FOR SERVER:-**<br>Use the Certificate Request file from the previous step yourcertificatename.csr, which you can submit to the CA you've chosen via the process they provide on their website. Using this file, they will generate a custom certificate for your server, which you can download according to the instructions they provide on their website.	| A zip file containing .cer (Root) & .crt (Server Certificate) file should be downloaded and placed in the server.|
|**5: IMPORT ROOT CERTIFICATE TO WEB SERVER KEYSTORE:-**<br>We have now Root and Server SSL Certificate generated in above step. Let’s import & trust the root Certificate(.cer) on server:<br>•	Log on to the server <br>•	Open a command prompt (start->run->cmd)<br>•	Change directory to the location of the Java executables (e.g. 'C:\Program Files (x86)\Java\jre6\bin' <br>Use the keytool command again to import & trust *.sha1.cer certificate:<br>keytool -import -alias root -keystore [path/to/your/keystore] -trustcacerts -file [path/to/the/root_certificate] <br>Now the root certificate should be imported to your keystore. Please verify using below command:<br>keytool -list -v -keystore [path/to/your/keystore] | Root certificate should be imported successfully.|
