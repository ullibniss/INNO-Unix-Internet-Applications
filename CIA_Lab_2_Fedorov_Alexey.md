# Lab 2.

## Done by Fedorov Alexey

# Task 1. Downloading and Installing a Caching Name Server

The sources for the latest version:
- BIND - https://www.isc.org/bind/
- Unbound - https://nlnetlabs.nl/projects/unbound/about/
- NSD will be installed later.

## 1.1 Validating the Download

```
The https://www.isc.org website provides signature files in addition to the BIND tarball.
These can be used to check if you have downloaded the version they intended to
distribute. The Unbound website uses a different mechanism, in particular, a
modification detection code, better known as a cryptographic hash.
```

## Why is it wise to verify your download?

Verifying the download ensures that the software has not been tampered with and is exactly what the developers intended to distribute. This protects against malware or corrupted files.

## Which mechanism is the best one to use (signatures or hashes)? Why?

Signatures provide a higher level of security as they are tied to the private key of the distributor, confirming authenticity. Hashes are faster to compute but vulnerable to collision attacks if not securely obtained.

## Download the Unbound+NSD and check its validity using one of the signatures.

### Unbound

Downloaded Unbound and SHA256 hash.

![image](https://github.com/user-attachments/assets/2ee11706-4629-4e8d-ae88-6ab3bc35e2fc)

Validated downloaded archive.

![image](https://github.com/user-attachments/assets/c8956d03-e440-40af-98ca-6ee121964e0f)

### NSD

Downloaded NSD and SHA256 hash.

![image](https://github.com/user-attachments/assets/11461bb5-3215-42c1-926b-e909da6aa47b)

Validated downloaded archive.

![image](https://github.com/user-attachments/assets/ad2a4a23-e2d4-494e-841e-05275572e10c)

## 1.2 - Documentation & Compiling

### Unbound

Configuring unbound, using configure command.

`sudo ./configure --prefix=/usr/local --sysconfdir=/usr/local/etc/unbound --localstatedir=/var/run`

![image](https://github.com/user-attachments/assets/82640f7f-2d96-43e6-a687-02a8cacc7cb2)

![image](https://github.com/user-attachments/assets/8b98555e-d0f4-4fdc-b712-61f9c0d20b29)

Applying `make`.

![image](https://github.com/user-attachments/assets/051c11b3-776a-496e-a411-6f3121d44b8c)

![image](https://github.com/user-attachments/assets/46cb3f77-6adb-49ee-b1a1-72e745c697cf)

Applying `make install`.

![image](https://github.com/user-attachments/assets/48ec1178-895a-4d23-bf06-892a7ecc7571)

![image](https://github.com/user-attachments/assets/377b4c07-65d1-442a-82b2-e9b85a48ba99)

### NSD

Configuring nsd, using configure command.

`sudo ./configure --prefix=/usr/local --sysconfdir=/usr/local/etc/nsd --localstatedir=/var/run`

![image](https://github.com/user-attachments/assets/65feb416-5d6b-4f3d-b2d6-7250cd7cb08a)

![image](https://github.com/user-attachments/assets/63a159ca-3bf6-4478-838b-865f76fc45a9)

Applying `make`.

![image](https://github.com/user-attachments/assets/08d56252-ebd3-4cd7-9137-2a771f4fc7e8)

![image](https://github.com/user-attachments/assets/ec6f5545-f0cc-46fb-9191-06aa49b2ad5f)

Applying `make install`.

![image](https://github.com/user-attachments/assets/c07402ef-37dd-485c-8fca-f20bd8c21632)

## What is the difference between /etc, /usr/etc, /usr/local/etc?

- `/etc`: Default system-wide configuration.
- `/usr/etc`: Legacy or distribution-specific configurations.
- `/usr/local/etc`: Used for user-installed software, avoiding conflicts with system packages.

# Task 2. Configuring Caching Name Server.

## Why are caching-only name servers useful?

- Reduce query latency by storing frequently accessed DNS records.
- Decrease upstream bandwidth usage.
- Provide resilience by serving cached data during upstream failures.

## Root.hints

I downloaded root hints from `ftp.rs.internic.net`.

`wget ftp://ftp.rs.internic.net/domain/named.cache -O /usr/local/etc/unbound/root.hints`

![image](https://github.com/user-attachments/assets/d482a044-168f-4bf2-a2ae-544391d39993)

## Unbound configuration

I configured unbound the following way:

![image](https://github.com/user-attachments/assets/ac99d41c-6380-47b3-b313-b6eba9331fd6)

I also checked errors in configuration file.

![image](https://github.com/user-attachments/assets/933943a0-91bc-4f17-8379-cd1f17c59ef2)

## Why is access control important for recursive server?

1. **Prevent Abuse**: Stops unauthorized users from exploiting the server for DDoS or amplification attacks.
2. **Protect Privacy**: Shields cached DNS data from unauthorized access.
3. **Conserve Resources**: Reduces load by limiting queries to trusted clients.
4. **Enhance Security**: Prevents cache poisoning and other attacks.
5. **Ensure Compliance**: Meets security regulations and best practices.

Restrict access to trusted IP ranges and monitor activity for optimal performance and safety.

## Why do the programs return a result value?

This is an exit code. The program usually returns 1 if it encountered an error and 0 if it finished successfully. In this case, the program is considered to have finished with an error if any syntax errors were detected during the check.

# Task 3. Running Caching Name Server


