# CIA Labs Summary

## Completed by Fedorov Alexey (tg @ullibniss)

## Repository with labs - https://github.com/ullibniss/INNO-Unix-Internet-Applications
---

# Lab 1. Booting

Github: https://github.com/ullibniss/INNO-Unix-Internet-Applications/blob/master/CIA_Lab_1_Fedorov_Alexey.md

In this lab, I set up a **PXE server** and configured it to facilitate network-based installations. To begin, I created the first virtual machine using **VirtualBox**, isolating the network to prevent interference with the shared network. I used **Ubuntu 22.04 LTS** for the PXE server and set up the network using two interfaces—NAT for general access and an Internal Network for communication with PXE clients. With UEFI mode enabled, I proceeded to install necessary services, including **DHCP**, **TFTP**, and **HTTP**, which are essential for network-based installations. The **GRUB bootloader** was also integrated to handle UEFI-based booting.

To configure the services, I began by setting up **DHCP** to allocate IP addresses to the PXE client. This configuration was followed by installing and configuring **TFTP**, which transfers the necessary boot files. Additionally, I installed the **GRUB bootloader** and configured it with the appropriate boot entries. To facilitate the installation, I downloaded the **Ubuntu server ISO**, mounted it, and copied the kernel and initrd files to the TFTP server. These files are essential for booting and initializing the operating system over the network. Finally, I used **Apache** as the HTTP server to serve the installation files.

For testing, I created a second virtual machine and configured it to boot from the PXE server. After adjusting the boot order, the PXE client successfully received its network configuration from the DHCP server and displayed the installation menu, confirming that the setup was functioning correctly. I then initiated the installation of **Ubuntu 22.04 LTS** on the client system, demonstrating the successful deployment of the OS over the network.

In addition to the setup and installation tasks, I explored various concepts, including **UEFI with Secure Boot**, **GPT partitioning**, and **MBR partitioning**. I discussed the roles of the services involved in the PXE process, including how DHCP assigns IP addresses, TFTP transfers boot files, and HTTP handles large file downloads. I also analyzed the differences between primary and logical partitions in the MBR partitioning scheme, which are crucial for disk management in legacy systems.

# Lab 2. Domain Name System (DNS)

Github: https://github.com/ullibniss/INNO-Unix-Internet-Applications/blob/master/CIA_Lab_2_Fedorov_Alexey.md

In this lab, I successfully set up a **caching name server** using Unbound and later configured an **authoritative name server** with NSD. I began by downloading Unbound from its official source and validating the integrity of the files using cryptographic hashes. After verification, I compiled and installed Unbound, ensuring it was properly configured to handle caching of DNS queries. I explored various configuration directories, including `/etc`, `/usr/etc`, and `/usr/local/etc`, and implemented access control settings to prevent unauthorized queries, protect privacy, and optimize resource usage.

Once Unbound was configured, I manually started the server and used `unbound-control` to manage it securely. This involved generating cryptographic keys and certificates for client-server authentication. I tested various commands such as `start`, `stop`, `reload`, and cache flushing, confirming that Unbound was responding correctly. Additionally, I modified `/etc/resolv.conf` to ensure system DNS queries were directed to my caching name server.

For the **authoritative name server**, I set up a second virtual machine, downloaded and validated NSD, and compiled it from source. After creating an NSD configuration file and a **zone file** with records for `st17.sne24.ru`, I integrated the authoritative server with Unbound by adding a delegation entry. This allowed Unbound to forward specific queries to the NSD server instead of external root servers.

To validate the setup, I conducted tests using `dig` from different virtual machines. Queries for both cached and authoritative records were successfully resolved, confirming the proper functioning of the DNS infrastructure. The combination of Unbound and NSD provided a complete DNS solution, effectively handling both recursive and authoritative queries while ensuring security and optimal performance.

# Lab 3. Mail Transfer Agents (MTA)

Github: https://github.com/ullibniss/INNO-Unix-Internet-Applications/blob/master/CIA_Lab_3_Fedorov_Alexey.md

In this lab, I installed and configured **Postfix** as a Mail Transfer Agent (MTA) to ensure secure and reliable email delivery. I began by verifying that no pre-installed MTA was present on the system before downloading Postfix from a trusted source. After validating the source using **GPG signatures**, I compiled and installed the software. The default settings of Postfix were configured to ensure the necessary functionality for email delivery.

Next, I adapted the **Postfix configuration file (`main.cf`)** to meet my requirements, including setting up network parameters. A test user account was created to verify local email delivery, and successful message transmission was confirmed. Additionally, I configured an alias to forward emails from `postmaster@st.sne23.ru` to the test account and validated that the emails were correctly redirected and delivered.

For **mail backup**, I worked with Niyaz Misbakhov to set up a secondary MTA. We updated DNS records to route email traffic to the backup server when the primary service was offline. After shutting down the primary MTA, test emails were successfully queued on the backup server, and with restoring the primary server, the queued messages were delivered. We also configured my MTA to function as a backup for the secondary server, ensuring email handling in case of failures.

To enhance security, We implemented **transport encryption** for email communications. After comparing **SSL/TLS and STARTTLS**, I determined that **STARTTLS** was the preferred method for SMTP encryption. We configured Postfix to use **self-signed SSL certificates** and ensured encryption by refusing non-secure connections. Finally, We verified the encryption setup using **Wireshark**, confirming that STARTTLS was successfully securing email transmissions. This lab allowed me to successfully install, configure, and secure an MTA, ensuring reliable email delivery with encryption.

# Lab 4. Web Servers and Directory Service

Github: https://github.com/ullibniss/INNO-Unix-Internet-Applications/blob/master/CIA_Lab_4_Fedorov_Alexey.md

In this lab, I installed and configured the Nginx web server from source, ensuring SSL/TLS support was enabled. After verifying the source code integrity, I compiled and installed the server. Next, I configured two virtual hosts, `server-1.st17.sne24.ru` and `server-2.st17.sne24.ru`, by adding DNS records and creating separate directories for each. Simple HTML pages were added to verify proper hosting, and the `nginx.conf` file was modified accordingly. After confirming the configuration syntax, I started the Nginx service and enabled it to run at boot. Using `curl`, I tested HTTP/1.1 requests and verified the expected responses.  

To enhance security, I implemented SSL/TLS encryption by generating self-signed certificates using OpenSSL. A Certificate Authority was created to issue and sign certificates for each virtual host. Additionally, I generated `dhparam.pem` to make the key exchange process stronger. The Nginx configuration was updated to enforce TLS 1.2 and 1.3 protocols, disable weak ciphers, and enable session caching for improved performance. After completing these configurations, I checked the syntax and reloaded the server to apply the changes.  

Finally, I tested the HTTPS connections using `curl`, ensuring that the server correctly handled encrypted requests. The successful responses confirmed that SSL/TLS was properly configured. This lab demonstrated the process of setting up a secure web server, from installation and virtual host configuration to implementing SSL/TLS encryption for secure communication.
