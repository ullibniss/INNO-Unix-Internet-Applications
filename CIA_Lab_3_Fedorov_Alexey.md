# Lab 3. MTA

## Done by Fedorov Alexey

---

## Task 1. Installation

### 1.1.a

```
First make sure that your system does not contain a pre-installed version of the MTA
of your choice, if so, remove it before you continue.
```

I tried to find **Exim** service on my Ubuntu linux:

![image](https://github.com/user-attachments/assets/63514132-9657-44fe-b505-d15d83167fa2)

There were no results.

### 1.1.b

I decided to use [Exim repository on Github](https://github.com/Exim/exim) to retreive source code of server. I've chosen latest version - [4.98](https://github.com/Exim/exim/releases/tag/exim-4.98)

![image](https://github.com/user-attachments/assets/952ae871-ed47-4268-973d-bb73e5f0cf7a)

### 1.1.c

I downloaded gpg key from https://github.com/Exim/exim/wiki/EximSecurity. Let's import it

```
gpg --import ~/Downloads/Exim-Maintainers-Keyring.asc
```

![image](https://github.com/user-attachments/assets/b8351f82-de58-4754-89e1-7feacce22794)

Now I verify source code

![image](https://github.com/user-attachments/assets/d5ab3b8c-3981-49d0-a27c-8c84a49bf798)


### 1.1.d

Then I build and intalled exim. Now I have a systemctl service.

![image](https://github.com/user-attachments/assets/a24f5501-af82-43b6-9a05-d8b58f543ce3)

### 2.1

I created user on my machine

![image](https://github.com/user-attachments/assets/deb02cdf-49a9-445a-82c7-27416fe20222)

Then i tried to send mail to it

```
echo "Test message for local account" | mail -s "Local Mail Test" test-exim
```

Let's check `test-exim` user's mail

![image](https://github.com/user-attachments/assets/f1062855-7a91-4de6-b172-66622503e86a)

### 2.2

Log of mailbox is:

![image](https://github.com/user-attachments/assets/29db4e85-8c48-4754-a71b-e78717687f9f)

### 2.3

Lets make alias for mail server and update them

```
sudo nano /etc/aliases
sudo newaliases
```

![Uploading image.png…]()

Sending test mail

```
echo "Message for postmaster" | mail -s "Postmaster Test" postmaster@st17.sne24.ru
```


