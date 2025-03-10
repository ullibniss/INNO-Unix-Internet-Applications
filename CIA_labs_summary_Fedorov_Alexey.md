# CIA Labs Summary

## Completed by Fedorov Alexey (tg @ullibniss)

## Repository with labs - https://github.com/ullibniss/INNO-Unix-Internet-Applications
---

# Lab 1. Booting

Github: https://github.com/ullibniss/INNO-Unix-Internet-Applications/blob/master/CIA_Lab_1_Fedorov_Alexey.md

This lab is devided globally on 2 parts - **PXE server configuration** and **UEFI vs BIOS booting approaches**

## PXE server configuration

The task involved setting up a PXE server on an Ubuntu 22.04 LTS virtual machine with both DHCP, TFTP, and HTTP services. The setup was done with UEFI mode enabled and two network interfaces: one for NAT and the other for an internal network. After configuring the network settings with Netplan, the DHCP server was installed and configured to assign IP addresses to clients. The TFTP server was also set up to deliver the necessary boot files, including the GRUB bootloader for UEFI.

For the installation files, the necessary kernel (vmlinuz) and initial RAM disk (initrd) were copied to the TFTP server, and the ISO file was placed in the HTTP server's directory. The bootloader configuration file (grub.cfg) was created to define boot options and set up the Ubiquity installer to start automatically.

In the PXE environment, the roles of each service are:

DHCP: Automatically assigns network configurations to PXE clients, including TFTP server and boot file details.
TFTP: Transfers small, essential boot files to the PXE client.
HTTP: Serves larger files, such as the operating system image, to the PXE client for installation.
The server was set to deploy Ubuntu 22.04 LTS.

The PXE server was not deployed directly on the SNE network to avoid potential IP conflicts, connectivity issues, and disruptions to the existing network infrastructure.

To test the PXE setup, a second VM was created with the internal network interface. The boot order was adjusted to boot from the network, and upon booting, the PXE client received its IP address from the DHCP server. The installation menu was displayed, and the client successfully began the installation of Ubuntu 22.04 LTS server, demonstrating that the PXE environment was functioning correctly.

## UEFI vs BIOS booting approaches

### UEFI vs. BIOS PXE Booting Approaches

**UEFI with Secure Boot Enabled**:
UEFI (Unified Extensible Firmware Interface) is a modern replacement for BIOS, responsible for hardware initialization and booting the operating system. With Secure Boot enabled, UEFI ensures that only verified and trusted bootloaders and drivers can execute. This protects against malware by verifying digital signatures. If signatures are invalid, the boot process is halted, ensuring system security.

**UEFI without Secure Boot**:
In UEFI without Secure Boot, the system still initializes hardware, but it doesn't check digital signatures. This allows unsigned or altered bootloaders to run, useful for testing or using custom OS configurations.

**BIOS PXE Booting**:
BIOS, the older firmware interface, initializes hardware and uses PXE (Preboot Execution Environment) to boot the system from a network. The BIOS sends a request to a PXE server, which responds with a boot image, starting the operating system installation or boot process.

### GPT (GUID Partition Table)

**GPT Layout**:
- **Protective MBR**: Prevents legacy MBR-based tools from misinterpreting the GPT disk.
- **GPT Header**: Contains metadata about the disk's layout, including the disk's unique identifier.
- **Partition Entry Array**: Stores information about each partition, including size, type, and attributes.
- **Partition Data**: The actual storage space where data resides.
- **Backup GPT Header and Partition Table**: A duplicate stored at the end of the disk for recovery in case of corruption.

### Partitions

**Verifying GPT Schema**:
The `lsblk` and `parted` tools can be used to verify the partitioning scheme on a disk. The `parted` tool shows "Partition Table: gpt" for a GPT partitioned disk.

**Using `dd` to Dump the Protective MBR and GPT**:
The `dd` command can be used to dump the Protective MBR and the GPT into a file. This process captures the first 1,152 bytes of the disk, which include the Protective MBR, GPT Header, and the first partition entry.
**Role of a Partition Table**:
A partition table organizes a disk into partitions, defining their size and type. It also manages bootable partitions and enables multi-OS setups. In GPT, redundancy is built in for recovery.

### Protective MBR

The **Protective MBR** safeguards GPT disks from being damaged by older MBR-based tools. It ensures compatibility by representing the GPT disk as a single large partition, preventing tools that don't support GPT from altering its structure.

### Primary vs. Logical Partitions in MBR

- **Primary Partitions**: MBR allows up to four primary partitions. They can be used for booting an OS and can be marked as active.
- **Logical Partitions**: These are created within an extended partition, overcoming the four-partition limit. Logical partitions cannot be set as active and are typically used for additional storage.

# Lab 2. Domain Name System (DNS)

Github: https://github.com/ullibniss/INNO-Unix-Internet-Applications/blob/master/CIA_Lab_2_Fedorov_Alexey.md

In this 


# Lab 3. Mail Transfer Agents (MTA)

Github: https://github.com/ullibniss/INNO-Unix-Internet-Applications/blob/master/CIA_Lab_3_Fedorov_Alexey.md




# Lab 4. Web Servers and Directory Service

Github: https://github.com/ullibniss/INNO-Unix-Internet-Applications/blob/master/CIA_Lab_4_Fedorov_Alexey.md

