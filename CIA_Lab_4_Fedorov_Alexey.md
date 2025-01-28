# Lab 4. Web Servers and Directory Service

## Done by Fedorov Alexey

# Task 1. Install & Configure Virtual Hosts

## 1.1 Download, verify, build and install the webserver daemon from the source. Note: Some features of your web server may be built-in or modularized. Enable at least SSL/TLS during your installation.

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


## 1.2. Define the root directory and then two virtual hosts (and configure DNS records or wildcard accordingly): aaa.stX.sne22.ru, bbb.stX.sne22.ru

I decided to start with DNS. I added 2 records in my zone st17.sne24.ru

![image](https://github.com/user-attachments/assets/83721768-8c45-4d04-8fb1-e7754ad942c2)

![image](https://github.com/user-attachments/assets/690544e1-4899-4d1f-868b-cea188eb49ce)

Then I create directory for each server in nginx.

![image](https://github.com/user-attachments/assets/b389b7d7-0ded-4243-a5e1-39fbb0cff2a2)

## 1.3. Create a simple, unique HTML page for each virtual host to make sure that the server can correctly serve it.

For each virtual host, I created an `index.html` file containing the following content:  

`<h1>Welcome to {virtual host name}</h1>`.

![image](https://github.com/user-attachments/assets/01ed1f5b-1b3f-45b6-9c80-b813136602d9)

Next I implemented `nginx.conf` to serve two different sites.

![image](https://github.com/user-attachments/assets/c1fd1050-8d23-46bc-a2f7-1556fc294904)

## 1.4 Check the configuration syntax, start the daemon and enable it at boot time.

Let's test it. I will use `curl` for this purpose. But firstly I need to check syntax and up nginx.

`sudo ./nginx -t`

![image](https://github.com/user-attachments/assets/66ebc21d-9192-4fcc-a6ae-4ea6d2074e32)

Syntax is correct. Let's boot.

![image](https://github.com/user-attachments/assets/e09317c8-fb5a-4abd-9582-33d2bd8f73bd)

Nginx started successfully. To enable it, I need to create systemd service unit in `/etc/systemd/system/nginx.service`. Let's implement it.

![image](https://github.com/user-attachments/assets/bae66d3d-5339-4faa-ad08-7d5f97992d0c)

After this I have to enable service.

![image](https://github.com/user-attachments/assets/44a909a9-686b-479d-bd88-4eb870ffb691)

Done! Nginx will start with boot.

# 1.5 Use curl to display the contents of a full HTTP/1.1 session served by your server.

Now I did 2 requests to `server1.st17.sne24.ru` and `server2.st17.sne24.ru`.

![image](https://github.com/user-attachments/assets/14617b51-9e87-4f9a-94f6-e496942b5216)

![image](https://github.com/user-attachments/assets/877ee90b-14e2-4f42-af87-7e1426648d7f)

Everyting works!

## 1.6 Explain the meaning of each request and reply header.

### Paraphrased Explanation:

#### **Request Headers:**
1. **GET / HTTP/1.1**:  
   - **GET**: The HTTP method used to retrieve data from the server.  
   - **/**: The path of the resource being requested.  
   - **HTTP/1.1**: The version of the HTTP protocol used for the request.  

2. **Host: <>.st11.sne24.ru**:  
   - Specifies the domain name or IP address of the server. This is crucial for virtual hosting, as it helps identify which website to serve.  

3. **User-Agent: curl/8.5.0**:  
   - Identifies the client (e.g., software or tool) making the request.  

4. **Accept: *\***:  
   - Indicates that the client can accept any type of content in the server's response.  

#### **Response Headers:**
1. **HTTP/1.1 200 OK**:  
   - Indicates the HTTP protocol version (1.1) and a successful response with the status code **200 OK**.  

2. **Server: nginx/1.26.2**:  
   - Specifies the server software (nginx) and its version handling the response.  

3. **Date: Mon, 25 Nov 2024 22:14:19 GMT**:  
   - The timestamp (in GMT) when the response was generated.  

4. **Content-Type: text/html**:  
   - The MIME type of the response body, indicating it contains an HTML document.  

5. **Content-Length: 45**:  
   - The size of the response body in bytes.  

6. **Last-Modified: Mon, 25 Nov 2024 22:02:37 GMT**:  
   - The timestamp of the last modification of the requested resource.  

7. **Connection: keep-alive**:  
   - Indicates that the connection will remain open for additional requests, improving efficiency.  

8. **ETag: "6744f3fd-2d"**:  
   - A unique identifier for the resource version, used for caching and conditional requests.  

9. **Accept-Ranges: bytes**:  
   - Indicates the server supports partial content delivery, allowing clients to request specific byte ranges (e.g., for downloading parts of a file).  

# Task 2. SSL/TLS

I decided to start with 2nd part of topic

## 2.2 Describe how you created your own certificate(s) e.g. with Let’s encrypt (certbot) or selfsigned and re-validate every virtual-host . Explain your security tuning process.

I created self-signed certificates using the OpenSSL tool. 

First, I generated a Certificate Authority (CA) Key and certificate. 

Generating key:

`openssl genrsa -out ca.key 2048`

![image](https://github.com/user-attachments/assets/ca4d06d1-f07c-4b55-8df6-92901a92cced)


Generating certificate:

`openssl req -x509 -new -nodes -key ca.key -sha256 -days 365 -out ca.crt`

![image](https://github.com/user-attachments/assets/85786fd3-c653-4eca-94b9-d45bdf7f41c1)

After that, I created certificates for each virtual host and signed them using the CA key. 

Generation keys for servers:

`openssl genrsa -out server1.key 2048`
`openssl genrsa -out server2.key 2048`

![image](https://github.com/user-attachments/assets/5b10e5f5-f8e4-4cc7-aff1-608eb3b5a218)

![image](https://github.com/user-attachments/assets/e6b6977d-beb7-490c-b509-5f085ed9ec03)

Generating CSR certificates for servers.

`openssl req -new -key server1.key -out server1.csr`
`openssl req -new -key server2.key -out server2.csr`

![image](https://github.com/user-attachments/assets/90b64f7e-2d9d-449b-ace7-c4d3c8dcaccd)

![image](https://github.com/user-attachments/assets/f4aa6ba2-00fa-4c92-8e31-6b33d63d7667)

Signing certificates.

`openssl x509 -req -in server1.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server1.crt -days 365 -sha256`
`openssl x509 -req -in server2.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server2.crt -days 365 -sha256`

![image](https://github.com/user-attachments/assets/50f11a32-5289-4317-9bf1-6d83a1b5c728)

![image](https://github.com/user-attachments/assets/8171fe10-ac73-4da0-90ea-dadca00f6add)

Except the certificates, let's create `dhparam` to make key exchange process stronger.

`openssl dhparam -out dhparam.pem 2048`

![image](https://github.com/user-attachments/assets/20db5325-78aa-4527-9189-0d1d940ed5de)

Now we ready to use HTTPS.

## Enable SSL/TLS and tune the various settings to make it as secure as possible.

Let's modify configuration.

![image](https://github.com/user-attachments/assets/455897da-f3c8-41b5-b22f-7921b7c451b8)

Run syntax check.

![image](https://github.com/user-attachments/assets/ee5b138a-433d-4e12-b5db-674e58cf33a5)

I reloaded nginx. Lets see how it works. I will begin with `curl`.

`sudo curl -v https://server1.st17.sne24.ru --cacert /usr/local/nginx/ssl/ca.crt`

![image](https://github.com/user-attachments/assets/84d02d01-70a6-4f3c-823d-2c83dfcadb54)

`sudo curl -v https://server2.st17.sne24.ru --cacert /usr/local/nginx/ssl/ca.crt`

![image](https://github.com/user-attachments/assets/91ffe455-917b-439f-a220-bfd049857862)

It works!!!

# References

1. https://kb.teramind.co/en/articles/8791235-how-to-generate-your-own-self-signed-ssl-certificates-for-use-with-an-on-premise-deployments
2. https://kb.wisc.edu/iam/page.php?id=4543
3. https://docs.nginx.com/
4. https://askubuntu.com/questions/980145/cant-add-nginx-module-requires-zlib


