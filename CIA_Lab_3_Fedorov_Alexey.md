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

I downloaded gpg key from https://github.com/Exim/exim/wiki/EximSecurity. Let's import it

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


# 3. Configure

## 3.1 Add a local account on your experimental machine and make sure that the MTA can deliver mail to it. Show the required configuration.

I created user on my machine

![image](https://github.com/user-attachments/assets/000c81df-13da-47c9-a817-a9baaa05354c)

Then i tried to send mail to it

```
echo "Test message for local account" | mail -s "Local Mail Test" test-postfix
```

Let's check `test-postfix` user's mail

![image](https://github.com/user-attachments/assets/83cb36f3-350e-46ad-98d1-3123b0b73f4e)

## 3.2 Add to your log an email received by this account. Do not forget the full headers!

Here is message with all headers:

![image](https://github.com/user-attachments/assets/06dd848b-8b34-44d1-976c-d0aa4137ed32)


## 3.3 Also make sure that any email intended for postmaster@st<X>.sne23.ru is delivered to this account. Show the full email as delivered to the new account and the required configuration.

Lets make alias for mail server and update them

```
sudo nano /etc/aliases
sudo newaliases
```

![image](https://github.com/user-attachments/assets/f9edc2a2-6d23-4f1a-bd86-9fd0b9ec896b)


Sending test mail

```
echo "Message for postmaster" | mail -s "Postmaster Test" postmaster@st17.sne24.ru
```

![image](https://github.com/user-attachments/assets/4e66d46b-1be8-4657-962b-b433dc05e8ba)

