
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

Let's install DHCP server.

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

![image](https://github.com/user-attachments/assets/0376eff4-d524-49e8-97e2-5464ce9f4309)

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

## 2.1. Create the second virtual machine using VirtualBox in order to test the PXE service.

For testing the PXE server, I created the second VM. As a type of network interface I set the internal network. It must get network configuration from the DHCP server.

![image](https://github.com/user-attachments/assets/31f4afd1-63b7-48cc-ad40-2753f5b1d3a0)

## 2.2. Change the boot order

I changed boot order in VM settings

![Screenshot from 2025-01-28 02-16-53](https://github.com/user-attachments/assets/67430733-acba-4b5c-b7b3-0b9fac1dc4ad)

## 2.3. Show that your PXE client takes the IP.

I booted system and got the installation menu, which I prepared via grub.cfg file. 

![Screenshot from 2025-01-28 02-49-19](https://github.com/user-attachments/assets/02212dd3-ea5d-47de-819f-5d34c8545759)

The PXE client got network configuration from the DHCP server.

![Screenshot from 2025-01-28 02-28-02](https://github.com/user-attachments/assets/c78a43d3-8aed-44b7-823d-b12a50fd75c5)

## 2.4. Boot and install a new system with it and show the proof in the report.

After that, the installation process was started.

![image](https://github.com/user-attachments/assets/2b11b8bf-ecb3-439a-b7bb-179c55982e29)

![image](https://github.com/user-attachments/assets/9dd64c1f-5fea-4b93-a60a-7947c8c0eee0)

And I got Ubuntu 22 server.

![image](https://github.com/user-attachments/assets/059ef358-317f-4dfe-99cd-4d7c6fa3014c)

# Task 2 - Questions to answer

## 2.1 Briefly explain UEFI with secure boot enabled, UEFI without secure boot, and BIOS PXE booting approaches.

UEFI (Unified Extensible Firmware Interface) is a contemporary firmware interface designed to replace the older BIOS system. It is responsible for hardware initialization and launching the operating system. A key security feature within UEFI is Secure Boot, which ensures that only verified and trusted software can execute during the boot process. This feature helps block malware from loading before the OS starts, thereby strengthening system security. Secure Boot works by checking the digital signatures of bootloaders and drivers to confirm their authenticity and integrity. However, when UEFI is used without Secure Boot, it lacks this signature verification capability, allowing the system to boot unsigned or altered bootloaders and operating systems. This can be beneficial for scenarios like testing, custom OS configurations, or running unsigned software.

On the other hand, BIOS (Basic Input/Output System) is the legacy firmware interface used for hardware initialization and OS booting. It predates UEFI and has certain limitations, such as less efficient handling of larger storage devices. In BIOS, PXE (Preboot Execution Environment) booting enables the network interface card to connect to a PXE server and load a boot image, facilitating remote booting and OS installation over a network.

### 2.1.1 How do they work? Explain with a simple diagram.

### UEFI with Secure Boot Enabled

1. When the system starts, the UEFI firmware begins its initialization process.  
2. UEFI verifies the digital signatures of the bootloader and drivers to ensure their authenticity.  
3. If the signatures are valid, the bootloader is permitted to run. If the signatures are invalid or missing, the boot process is halted.  
4. Once verified, the bootloader proceeds to load the operating system.

![image](https://github.com/user-attachments/assets/54b74d8c-1e30-44f2-a0c6-abef490db4e0)

### UEFI without Secure Boot

1. The system powers up, and UEFI initializes the hardware.
2. UEFI loads the bootloader without verifying signatures.
3. The bootloader loads the OS.

![image](https://github.com/user-attachments/assets/f30b34f7-5fd7-4f2a-9535-eaf57ba73df4)

### BIOS PXE Booting

1. When the system powers on, the BIOS firmware starts its initialization process.  
2. The BIOS initializes the Network Interface Card (NIC) and sends a request over the network to a PXE server.  
3. The PXE server responds by sending a boot image to the system.  
4. The received boot image is loaded, initiating the OS installation or boot process.

![image](https://github.com/user-attachments/assets/0b6d320a-ebe2-4429-9d84-1ffb690fa2b8)

## 2.2 What is a GPT?

GPT (GUID Partition Table) is a modern standard for partitioning storage devices such as hard drives and SSDs. It is integrated into the UEFI specification and defines the structure of data on a disk. The term GUID stands for Globally Unique Identifier, meaning each partition on the drive is assigned a unique identifier. This ensures that partition information remains distinct and avoids any overlap or conflicts.

### 2.2.1. What is its general layout? Explain each element.

1. **Protective MBR (Master Boot Record):**  
   Located in the first sector (512 bytes) of a GPT-partitioned disk, the Protective MBR serves as a safeguard. It prevents older MBR-based tools from mistakenly identifying the GPT disk as unpartitioned or attempting to overwrite it, ensuring compatibility and protection.

2. **GPT Header:**  
   The GPT Header contains critical metadata about the partition table and the disk's layout. It includes information such as the disk's unique identifier, the location of the partition table, and the number of partition entries.

3. **Partition Entry Array (Partition Table):**  
   This array stores detailed information about each partition on the disk, including its start and end points, size, type, and attributes. It defines the structure and organization of the disk's partitions.

4. **Partition Data:**  
   This is the actual storage space on the disk allocated for partitions, where data and files are stored. Each partition's boundaries and properties are determined by the corresponding entries in the Partition Entry Array.

5. **Backup GPT Header and Partition Table:**  
   A duplicate of the GPT Header and Partition Table is stored at the end of the disk. This backup provides redundancy, allowing the partition information to be recovered in case the primary GPT Header or Partition Table becomes corrupted or lost.

### 2.2.2. What is the role of a partition table?

### Role of a Partition Table

1. **Organization of Data:**  
   The partition table manages how the storage device is divided into distinct sections called partitions. Each partition can be allocated for specific purposes, such as storing the operating system, applications, or user data, enabling efficient data organization.

2. **Defining Partition Boundaries:**  
   It specifies the size, starting point, and ending point of each partition on the disk. This information is crucial for the operating system to identify where one partition ends and another begins, ensuring proper data access and storage.

3. **Partition Types:**  
   The partition table identifies the type of each partition (e.g., primary, extended, logical) and the file system used (such as NTFS, FAT32, or ext4). This helps the operating system understand how to interact with and manage data within each partition.

4. **Booting Information:**  
   For bootable partitions, the partition table includes essential details required for system startup. This is particularly important for systems using BIOS or UEFI firmware, as it indicates which partition contains the bootloader, enabling the system to boot correctly.

5. **Managing Multiple Operating Systems:**  
   In dual-boot or multi-boot setups, where multiple operating systems are installed on the same drive, the partition table tracks each OS's partition. This allows users to choose which operating system to boot during startup.

6. **Disk Management:**  
   The partition table facilitates easier disk management by enabling users to create, delete, resize, or modify partitions without impacting data stored in other partitions—provided these actions are performed correctly.

7. **Redundancy and Recovery:**  
   In GPT-based systems, the partition table is stored in multiple locations on the disk (both at the beginning and the end). This redundancy ensures that partition information can be recovered if the primary partition table becomes corrupted, enhancing data reliability and recovery options.

## 2.3. What is gdisk?

**Gdisk** is a command-line tool used for managing GUID Partition Table (GPT) disks in Linux and other Unix-like operating systems. It allows users to create, delete, and modify partitions on disks formatted with the GPT scheme. Specifically designed for GPT disks, Gdisk is ideal for systems that need the advanced features and flexibility offered by the GPT partitioning standard.

### 2.3.1 How Does Gdisk Work?

Gdisk operates through a command-line interface, enabling users to interact with and manage the partition tables of GPT disks. Here’s what you can do with Gdisk:

1. **Create Partitions:**  
   Add new partitions to a GPT disk by specifying their size, type, and starting location. This allows for precise allocation of disk space for different purposes.

2. **Delete Partitions:**  
   Remove existing partitions from the disk, freeing up space for new partitions or reorganizing the disk layout as needed.

3. **View Partition Information:**  
   Display the current partition table along with detailed information about each partition, such as its size, type, and location on the disk.

4. **Resize Partitions:**  
   While Gdisk does not directly resize partitions, you can delete and recreate partitions with adjusted sizes to effectively resize them.

5. **Change Partition Types:**  
   Assign or modify the types of partitions using GUIDs (Globally Unique Identifiers) to define their purpose, such as for system, data, or swap partitions.

6. **Backup and Recovery:**  
   Create backups of GPT headers and partition tables to facilitate recovery in case of corruption or data loss. Gdisk can also attempt to recover deleted partitions if backup information is still available.

7. **Convert MBR to GPT:**  
   Convert a disk from the older Master Boot Record (MBR) partitioning scheme to GPT without data loss, making it compatible with modern systems that require GPT.

8. **Check Disk Integrity:**  
   Validate the GPT structure to ensure that headers and partition entries are consistent and free of errors, helping maintain the disk’s reliability and functionality.

Gdisk is a powerful tool for managing GPT disks, offering advanced capabilities for partitioning, recovery, and disk maintenance in Linux and Unix-like systems.

### 2.3.3 Provide a simple practice.

To use Gdisk, we need to specify the disk we want to work with as a parameter. For example, to manage the disk `/dev/nvme0n1p9`, run the command.

![image](https://github.com/user-attachments/assets/017c345b-6919-4944-be29-7309580233c5)

We can backup GPT data to file.

![image](https://github.com/user-attachments/assets/064a2fa9-bfd7-40fd-b1e9-b73c5a822bec)

Or print partition table.

![image](https://github.com/user-attachments/assets/ce06529b-b8f2-405c-9baa-101e432f31e5)

## 2.4 What is a Protective MBR and why is it in the GPT?

The Protective MBR plays a crucial role in safeguarding GPT disks. As an essential component of the GPT scheme, it ensures both backward compatibility and data integrity. It acts as a protective shield, preventing legacy software—which may not support GPT—from damaging or corrupting the GPT structure. When a disk utility or operating system that only recognizes MBR reads the Protective MBR, it interprets the disk as having a single, large partition that is already in use. This prevents the utility or OS from attempting to modify the disk or overwrite partition information, thereby preserving the integrity of the GPT layout.

# Task 3. Partitions

## 3.1. Verify the GPT schema of your Ubuntu machine.

Initially, I utilized the **lsblk** tool to identify existing disks and partitions. **lsblk** is a command-line utility in Linux designed to display information about all available or specified block devices. It presents details about storage devices and their partitions in a clear, tree-like structure. ( It is different from previous question, because i decided to use VM ).

![image](https://github.com/user-attachments/assets/d249ed37-1c1a-4447-911b-84a4c956eae2)

Next, I used the **parted** tool to determine the partitioning scheme in use. **parted** is a command-line utility in Linux for managing disk partitions, supporting both MBR and GPT schemes. I started by running the command with the `/dev/nvme0n1` parameter, which opened the internal interface of **parted**. Inside the interface, I executed the `print` command, and the output displayed **"Partition Table: gpt"**. This confirmed that the GPT partitioning scheme is being used on my system.

![image](https://github.com/user-attachments/assets/bab7aa62-73a3-4fcc-843e-10c6fc7a4b63)


## 3.2. Use the dd utility to dump the Protective MBR and GPT into a file in your home directory. The dump should contain up to first partition entry (Inclusive). Note: upload the dump file to your moodle.

The first sector of the disk contains the Protective MBR, the second sector holds the GPT Header, and the third sector includes the first partition entry. In my system, as I later discovered, each partition entry is 128 bytes in size. Since the first two sectors (Protective MBR and GPT Header) are each 512 bytes (as indicated by the block size), I needed to dump a total of 1,152 bytes (512 + 512 + 128). To achieve this, I used the following command:

`sudo dd if=/dev/sda of=/home/ullibniss/dump bs=128 count=9`

![image](https://github.com/user-attachments/assets/d329c37f-1d9f-457d-8022-2680ce1b6533)

## 3.3. Load the dump file into a hex dump utility (e.g. 010 editor) to look at the raw data in the file.

For analyzing the dump file I used the “https://hex-works.com/” utility.

![image](https://github.com/user-attachments/assets/6cc6d14d-504e-4522-b67e-d0fc2937d9b2)

##  3.5 Name two differences between primary and logical partitions in an MBR partitioning scheme.

### Number Limitations:
- **Primary Partitions:**  
  The MBR partitioning scheme allows for a maximum of **four primary partitions**. If more partitions are needed, one of these primary slots must be used to create an **extended partition**.
  
- **Logical Partitions:**  
  Logical partitions are created **within an extended partition**. While the number of logical partitions is theoretically unlimited, it is subject to the operating system’s constraints. These partitions are used to overcome the four-partition limit of MBR.

### Usage and Booting:
- **Primary Partitions:**  
  Only primary partitions can be marked as **active** and are used for **booting an operating system**. The system's bootloader, such as GRUB or Windows Boot Manager, typically resides on a primary partition.

- **Logical Partitions:**  
  Logical partitions **cannot be set as active** and are primarily used for **additional data storage**. They are not intended for booting an operating system and are instead utilized to organize extra storage space.

# References:
1. PXE server on Ubuntu -
https://wiki.ubuntu.com/UEFI/PXE-netboot-install
2. PXE server on Astra Linux-
https://wiki.astralinux.ru/pages/viewpage.action?pageId=263031254
3. UEFI Specification -
https://uefi.org/specs/UEFI/2.10/05_GUID_Partition_Table_Format.html
4. GPT - https://thestarman.pcministry.com/asm/mbr/GPT.htm
