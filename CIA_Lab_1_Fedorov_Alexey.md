
# Lab 1. Booting

## Done by Fedorov Alexey

# Task 1. PXE Installation

## 1.1. Create the first virtual machine using VirtualBox and isolate a private network on your workstation: do not pollute our shared network with your own DHCP service. There are several network settings offered by VirtualBox, choose the right one accordingly and Install your PXE server there. Hint: use two network adapters.

I decided to create Ubuntu 22.04 LTS virtual machine for PXE server.

![image](https://github.com/user-attachments/assets/87fc6fe0-2f3f-484d-8f09-bfb78c6b71ec)

Added second interface - Internal Network. First stayed NAT.

![image](https://github.com/user-attachments/assets/b9c5e3a9-cf80-4bcd-9cfe-a2cd1d39cf6d)

UEFI mode enabled.

![image](https://github.com/user-attachments/assets/829a2275-c8fa-4968-ac8a-27ff9ef81e39)

## 1.2. Hint: you need to set up some of the services such as DHCP, TFTP, HTTP, NFS, and some boot loader (e.g. PXELINUX or GRUB) based on your approach. Also, take care of UEFI and BIOS based approaches. Hint: understand BIOS based approach but implement UEFI approach.

I chose to set up a PXE server by integrating `DHCP`, `TFTP`, and `HTTP` services.

Once the server was deployed, I configured the network settings. In Ubuntu, network configuration is managed through the Netplan configuration file.

![image](https://github.com/user-attachments/assets/f6532eb8-7117-4535-a2e5-e7f937b3d34a)

To apply any changes, the command `netplan apply` must be executed.

![image](https://github.com/user-attachments/assets/f998d65d-a2d9-4e44-be71-08f0c1479646)

Network configured.

![image](https://github.com/user-attachments/assets/97db09ab-f8ea-417c-8bdf-e871d319519f)

Let's install DHCP service.

![image](https://github.com/user-attachments/assets/c2164f47-9052-4a5d-ab36-daed9815c3ae)

Before configuration we need to choose interface for DHCP in `/etc/default/isc-dhcp-server`.

![image](https://github.com/user-attachments/assets/3cc6d6c0-0250-4304-a24d-067db1f1bb53)

Now configuring DHCP server in `/etc/dhcp/dhcpd.conf`:

![image](https://github.com/user-attachments/assets/7c6ecadc-591e-49ae-b7de-07e58138ff58)

To apply setting we need to restart it.

![image](https://github.com/user-attachments/assets/90f1dfa6-259c-4614-9cd9-44f9442b7349)

Next let's install TFTP server.

![image](https://github.com/user-attachments/assets/554ffb3d-9482-49ed-8851-306091bfbb4a)

It's config in `/etc/default/tftpd-hpa`. I modified it.

![image](https://github.com/user-attachments/assets/0568d642-0701-4c66-a97e-f326eeb80be9)

Apply settings.

![image](https://github.com/user-attachments/assets/50abc26c-835c-4620-b9be-372de5930088)

Because I deployed the PXE server using the UEFI approach, I also installed the `grub-efi-amd64-bin` package.

![image](https://github.com/user-attachments/assets/eadb1c17-30c8-41c6-b672-b24f918e6248)

For creating GRUB file I used the following command:

`grub-mknetdir --net-directory=/srv/tftp --subdir=/ubuntu-installer/grub`

![image](https://github.com/user-attachments/assets/120c6657-eae8-4e00-95fe-f16be97324dd)

And it created `core.efi` on the tftp folder. Location of this file I added to DHCP configuration.

Next, I downloaded the ISO file for the Ubuntu server, mounted it to the filesystem, and copied the `vmlinuz` and `initrd` files to the TFTP directory. Here, `vmlinuz` represents the compressed, bootable Linux kernel image, while `initrd` is a temporary root filesystem loaded into memory during the boot process. The `initrd` image includes critical drivers and scripts required to initialize the actual root filesystem on the disk.

![image](https://github.com/user-attachments/assets/e0761a84-12f7-4696-8070-ccac816decf8)

![image](https://github.com/user-attachments/assets/9a7b412a-909b-4b0b-8d67-59a784e55710)

As an HTTP server I used apache. After I installed apache I moved to the “/var/www/html” folder ISO file.

![image](https://github.com/user-attachments/assets/a78ca0bc-a032-48fd-8eee-01dc3acf2d6d)

![image](https://github.com/user-attachments/assets/88d4adc4-1c41-4006-a9c4-bd868f0fdd73)

Once all the necessary services were set up, I created a file named `/srv/tftp/ubuntu-installer/grub/grub.cfg` and added the following content to it.

![image](https://github.com/user-attachments/assets/93ec0690-f48c-4a7a-a197-96a1ec4d229b)

The `grub.cfg` file defines the boot menu options and manages how the bootloader initiates the operating system on the computer.  
Here’s an explanation of the parameters used:  
- **`set timeout=10`**: The GRUB boot menu will appear for 10 seconds before automatically selecting the default boot entry.  
- **`set default=0`**: Sets the first menu entry (indexed as 0) as the default boot option.  
- **`set gfxpayload=keep`**: Preserves the graphical settings of the bootloader when the kernel starts.  
- **`/ubuntu-server/vmlinuz`**: The path to the Linux kernel image.  
- **`ip=dhcp`**: Configures the system to obtain an IP address via DHCP.  
- **`url=http://192.168.254.1/ubuntu-24.10-live-server-amd64.iso`**: Specifies the URL of the Ubuntu ISO file used for installation.  
- **`only-ubiquity`**: Directs the system to launch the Ubiquity installer immediately, bypassing the live session. Ubiquity is Ubuntu’s graphical installer.  
- **`initrd /ubuntu-server/initrd`**: Specifies the initial RAM disk (`initrd`) file, which is loaded into memory to set up the environment before mounting the actual root filesystem.

### 1.2.1. Write about each service’s role in the PXE environment.

The DHCP service plays a key role in automatically assigning network configuration settings to the PXE client. These settings usually include an IP address, subnet mask, gateway, DNS servers, and details about the TFTP server and the network boot file.  

TFTP (Trivial File Transfer Protocol) is utilized to transfer the initial boot files to the PXE client. It is a simple and lightweight protocol, ideal for scenarios where reliability takes precedence over speed or security.  

After the PXE client loads the initial bootloader and kernel from the TFTP server, it typically relies on HTTP (or occasionally FTP) to download larger files, such as the operating system image or installation packages. HTTP is generally faster and more dependable than TFTP, particularly when handling large file transfers.

### 1.2.2. Your PXE server should serve the operating system of your choosing.

My PXE contains Ubuntu 22.04 LTS server.

## 1.3. Question: why not run your DHCP service on the SNE network directly? 

Deploying a DHCP server on the SNE network might disrupt the existing network infrastructure. If my DHCP service begins assigning IP addresses to devices on the SNE network, it could lead to IP conflicts, connectivity problems, or unauthorized access to the network.

# Task 2. PXE Client Setup


