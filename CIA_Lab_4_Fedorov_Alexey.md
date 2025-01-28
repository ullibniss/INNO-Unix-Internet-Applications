![image](https://github.com/user-attachments/assets/0e843ff3-fc37-4bf4-8293-8124840d842b)# Lab 4. Web Servers and Directory Service

## Done by Fedorov Alexey

# Task 1. Install & Configure Virtual Hosts

# 1.1 Download, verify, build and install the webserver daemon from the source. Note: Some features of your web server may be built-in or modularized. Enable at least SSL/TLS during your installation.

I obtained the source code archive, along with its signature and public keys, from the official website at `http://nginx.org/en/download.html`.

![image](https://github.com/user-attachments/assets/f709a0ed-3eca-4bef-8938-4a0a8995bb8d)

Next, I imported the public keys and proceeded to verify the archive's authenticity using the gpg tool.

![image](https://github.com/user-attachments/assets/33b387cb-5925-4114-ba0f-609ad70eb97c)

![image](https://github.com/user-attachments/assets/641d428a-6da6-487c-ae71-636d967f2bc0)

Once the verification was successfully completed, I began the installation process. First, I prepared the configuration files using the `./configure` program. Since I needed to enable TLS support, I included the following flags:

- `--with-http_ssl_module`: This flag activates the HTTP SSL/TLS module in NGINX, which is essential for handling HTTPS traffic.
- `--prefix=/usr/local/nginx`: This flag sets the installation directory for NGINX to `/usr/local/nginx`.

![image](https://github.com/user-attachments/assets/cc60ef2e-c346-4d67-a24d-cb0c51d0c7da)

![image](https://github.com/user-attachments/assets/e01f21ad-381e-49bd-a867-30547b6f8d9c)

Then I successfully compiled nginx.

![image](https://github.com/user-attachments/assets/74c47533-84ab-40a7-8573-628e60441cbe)

![image](https://github.com/user-attachments/assets/4b61399f-e616-43e4-8b83-f69c317d54e9)

![image](https://github.com/user-attachments/assets/a704b1b9-3157-497c-8797-6cecb2444274)

![image](https://github.com/user-attachments/assets/1081df52-a5bc-42d5-89f6-b41ce48c3c07)


# 1.2. Define the root directory and then two virtual hosts (and configure DNS records or wildcard accordingly): aaa.stX.sne22.ru, bbb.stX.sne22.ru

I decided to start with DNS. I added 2 records in my zone st17.sne24.ru

![image](https://github.com/user-attachments/assets/83721768-8c45-4d04-8fb1-e7754ad942c2)

![image](https://github.com/user-attachments/assets/690544e1-4899-4d1f-868b-cea188eb49ce)

Then I create directory for each server in nginx.

![image](https://github.com/user-attachments/assets/b389b7d7-0ded-4243-a5e1-39fbb0cff2a2)

# 1.3. Create a simple, unique HTML page for each virtual host to make sure that the server can correctly serve it.

For each virtual host, I created an `index.html` file containing the following content:  

`<h1>Welcome to {virtual host name}</h1>`.

![image](https://github.com/user-attachments/assets/01ed1f5b-1b3f-45b6-9c80-b813136602d9)

Next I implemented nginx.conf to serve two different sites



