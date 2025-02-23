# Lab 3. MTA

## Done by Fedorov Alexey

---

## Task 1. Installation

### 1.1.a First make sure that your system does not contain a pre-installed version of the MTA of your choice, if so, remove it before you continue.

First of all I tried to execute binaries called `postman` && `exim`

![image](https://github.com/user-attachments/assets/a1d1d57a-e127-4435-8461-8ad4e9b5fbe5)

I tried to find **Exim** or **Postman** service on my Ubuntu linux:

![image](https://github.com/user-attachments/assets/63514132-9657-44fe-b505-d15d83167fa2)

![image](https://github.com/user-attachments/assets/b8f955d4-56f5-4575-a073-128604299707)

There were no results.

### 1.1.b Make sure the source code is retrieved from a secure location. Use the official website for the MTA of your choice.

Downloaded postfix source code, signature and gpg public key from offical website `http://955a.fr/ftp.porcupine.org/mirrors/postfix-release/`.

![image](https://github.com/user-attachments/assets/7b98ee24-d488-4fc0-b6f9-fb6605fb0c73)

### 1.1.c Because it is important that an MTA be correct and secure it is often signed using a digital PGP signature. If your MTA is signed then make sure you have downloaded the correct sources by checking the validity of the key and the signature

I downloaded gpg key from https://github.com/Exim/exim/wiki/EximSecurity. Let's import it.

```
gpg --import wietse.pgp
```

![image](https://github.com/user-attachments/assets/82631544-c0d2-42a4-ba78-f9ecd4cb7c8c)

Then verify source code.

```
gpg --verify postfix-3.9.1.tar.gz.gpg2 postfix-3.9.1.tar.gz
```

![image](https://github.com/user-attachments/assets/bb954447-492a-4921-b16b-2d9829ff0599)

### 1.1.d There are a number of options that you will have to enter before compilation, so that the functionality can be compiled into the program. Make sure the basic install holds all the necessary functionality. Show the options you configured.

Let's start build. As written in documentation, I need to run make to build one Makefile. After I collected dependecies from the whole internet it worked.

```
 make -f Makefile.init makefiles
```

![image](https://github.com/user-attachments/assets/35e11f68-c163-4823-86a1-34c22219f1ef)

![image](https://github.com/user-attachments/assets/cbccd101-2c21-41a7-9197-8fd1e9b7d440)

Next step is real make, to build binary file. 

```
make
```

![image](https://github.com/user-attachments/assets/484ed938-4b6c-4563-99d2-1d73ce6b8e52)

![image](https://github.com/user-attachments/assets/871226c6-f26b-4445-9db8-a2d2f0f4c703)

And finally installation.

```
sudo make install
```
![image](https://github.com/user-attachments/assets/3dabb016-afdd-4ade-876d-fd51249f874f)

![image](https://github.com/user-attachments/assets/33bf1e2b-042f-41e7-8386-64d0d9174ec9)

There wee options during install. I stayed them default.  

![image](https://github.com/user-attachments/assets/02dec8f9-10cb-4281-816e-edd53be75b87)

Postfix installed!

## 2 Most of the options for an MTA can be found in a configuration file that will be loaded when the MTA starts. It is recommended to start with an example configuration that looks a lot like what you need for now. Show how you adapt it to your needs.

Here is my configuration `/etc/postfix/main.cf`:

![image](https://github.com/user-attachments/assets/cd0d6376-449d-4251-a42b-aa2a2d5b4f9b)

I reconfigured mynetworks and mydestination.

## 3.1 Add a local account on your experimental machine and make sure that the MTA can deliver mail to it. Show the required configuration.

I created user on my machine.

![image](https://github.com/user-attachments/assets/000c81df-13da-47c9-a817-a9baaa05354c)

Then i tried to send mail to it.

```
echo "Test message for local account" | mail -s "Local Mail Test" test-postfix
```

Let's check `test-postfix` user's mail.

![image](https://github.com/user-attachments/assets/83cb36f3-350e-46ad-98d1-3123b0b73f4e)

## 3.2 Add to your log an email received by this account. Do not forget the full headers!

Here is message with all headers:

![image](https://github.com/user-attachments/assets/06dd848b-8b34-44d1-976c-d0aa4137ed32)


## 3.3 Also make sure that any email intended for postmaster@st<X>.sne23.ru is delivered to this account. Show the full email as delivered to the new account and the required configuration.

Lets make alias for mail server and update them.

```
sudo nano /etc/aliases
sudo newaliases
```

![image](https://github.com/user-attachments/assets/f9edc2a2-6d23-4f1a-bd86-9fd0b9ec896b)


Sending test mail.

```
echo "Message for postmaster" | mail -s "Postmaster Test" postmaster@st17.sne24.ru
```

![image](https://github.com/user-attachments/assets/4e66d46b-1be8-4657-962b-b433dc05e8ba)

# 2. Mail backup

```
Remark: we did this part of lab with Niyaz at November. This is reason why here is another environment and ip adresses.
```

## 4.a. Adapt the DNS information for your domain, so that the backup MTA on your partner’s server can be found.

We reconfigured DNS for mail backup.

![image](https://github.com/user-attachments/assets/54157a0a-0227-40f4-8e59-1bdb0ec8a8a1)

## 4.b Validate by shutting your service down and sending a message to your domain

I shutted down my mail service.

![image](https://github.com/user-attachments/assets/067ded87-0b30-4a0b-bb79-33e6a213ae82)

Niyaz sent me several emails, and they were redirected to his backup service. Emails were stored in queue.

![image](https://github.com/user-attachments/assets/3b9eb716-b413-4e2a-ad73-5ca8a201b045)

## 4.c. Bring your service back up and wait.

I started my email service again.

![image](https://github.com/user-attachments/assets/5e278102-5ce9-4ac6-aab7-99f37f1eeb48)

After waiting for 8-10 minunes I got Niyaz messages.

![image](https://github.com/user-attachments/assets/07c42e7f-786d-4e30-acce-c526bc25f73d)

To force backup server to send messages to original server we can use `flush` command.

![image](https://github.com/user-attachments/assets/32a863fb-e87b-425b-88f3-9ed7d0ba8e32)

## 5.a. Make your MTA act as a backup for your partner’s domain.

We reconfigured DNS server for Niyaz is backup.

![image](https://github.com/user-attachments/assets/112ef509-a7c8-4171-9782-e10817567441)

## 5.b. Show the logs while doing your mate’s acceptance test and show where the message is temporarily stored.

I sent to Niyaz several emails. Here is logs:

![image](https://github.com/user-attachments/assets/a7012bbc-9e03-4e74-a92b-25585fb1b914)

Messages are also stored in queue:

![image](https://github.com/user-attachments/assets/0e5a20fd-8b05-42c6-a31b-c16d432d3231)

## 5.c. Once your partner’s MTA is back online, eventually force an immediate delivery and show your mail logs.

Niyaz is back online and forced email send with `flush` command. It flushed queue by sending messages that are there.

![image](https://github.com/user-attachments/assets/f7abaa82-779c-4939-90ed-782b774e8c24)


# 3. Transport Encryption

## 6.a. Which one is better, SSL/TLS or STARTTLS, why?

**SSL/TLS** - are cryptographic protocols designed to provide secure communication over a computer network. Typically used to secure web traffic, email, and other types of data transfers. SSL/TLS establishes a secure connection from the beginning of the communication. The client and server negotiate a secure connection before any data is exchanged.

**STARTTLS** - protocol command used to upgrade an existing insecure connection to a secure one using SSL/TLS. It is commonly used in email protocols (SMTP, IMAP, POP3) to secure communications. STARTTLS starts with an insecure connection and then upgrades it to a secure one. The client and server initially connect in plaintext, and then the STARTTLS command is issued to switch to a secure connection.

As a result, use SSL/TLS if you want a straightforward, secure connection from the start and don't need to support insecure connections on the same port, as it establishes encryption immediately and is simpler to configure. On the other hand, use STARTTLS if you need the flexibility to support both secure and insecure connections on the same port, or if you are working with legacy systems that require this approach, as it allows upgrading an existing insecure connection to a secure one.

## 6.b. Which one is actually in use for SMTP?

**STARTTLS** is the most used method for securing SMTP in modern email systems. It is the standard recommended by organizations like the IETF and is widely supported by email servers and clients.

## 7.a. Add transport encryption to your MTA.

To add encryption for `postfix` we need to generate SSL certificates and keys. I generated it with `openssl` tools. To configure mail server we need to edit `/etc/postfix/main.cf`.

Generated certificates and keys:

![image](https://github.com/user-attachments/assets/d987c42a-1e9e-482a-8630-ff46bd5e61d7)

Configuration:

![image](https://github.com/user-attachments/assets/2799eb74-5f29-4de3-91f1-20b5bccb2935)

## 7.b. Eventually force the transport to be encrypted only (refuse non encrypted transport).

To force SMTP to refuse non encrypted transport, we need to configure `/etc/postfix/master.cf`:

![image](https://github.com/user-attachments/assets/2e96f3be-b836-4807-91ba-8f8d7c22fe7b)

We forces to use encryption on 25 port and also open 587 (STARTTLS port) for encrypted connection.

## 7.c Proceed with validation (proof or acceptance testing), as usual.

We used Wireshark to test encryption. Niyaz sent me email.

![image](https://github.com/user-attachments/assets/18f51c19-a903-4209-87f6-f97531568c67)

As we can see, server uses STARTTLS. It starts with unencrypted connection and encrypts it later.

# References

- https://www.postfix.org/documentation.html
- https://wiki.dieg.info/postfix_backup_mx
- https://support.top.host/help/what-are-the-differences-between-ssl/tls-vs-starttls/?lang=en#:~:text=STARTTLS%20differs%20from%20SSL%20and,using%20SSL%20or%20TLS%20protocol.
