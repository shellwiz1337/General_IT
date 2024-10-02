
# FOG Server Deployment for Windows Server 2022 Image Management
## Introduction
This project demonstrates the deployment and testing of the FOG Project, an open-source computer imaging solution, used to capture and deploy images of Windows Server 2022 in a virtualized environment. The project covers a complete workflow
from setting up the FOG server on an Ubuntu 22.04.5 machine to capturing and deploying images across multiple domain controllers. This documentation serves as a detailed guide to understand the steps, configurations, and key features of FOG 
in managing operating system deployment.

## Project Objective
To set up an image deployment infrastructure using FOG Project on an internal network and deploy Windows Server 2022 images to multiple domain controllers (DC1, DC2, and DC3). The project also explores configuring a PfSense firewall
for PXE booting to facilitate the network booting required by FOG.


## Requirements
- Ubuntu 22.04.5 LTS for the FOG server installation.
- Windows Server 2022 Standard Evaluation version (Desktop Experience) for domain controllers.
- PfSense Firewall for network booting (PXE) configuration.
- VMware environment for virtualized server instances.


## Technologies Used
- FOG Project: Open-source computer imaging and network booting solution.
- Ubuntu 22.04.5 LTS: Operating system for the FOG server.
- Windows Server 2022: Domain controllers for image deployment.
- Sysprep: Windows system preparation tool for generalizing server images.
- PfSense: Open-source firewall software used for managing PXE booting.

  
## FOG Project Overview

  FOG Project is an open-source imaging solution designed to simplify network-based OS deployment. It is widely used in environments where multiple systems need to be deployed,
  managed, and maintained efficiently. Some of FOG's key features include:

- Disk Imaging: Capture and deploy entire OS images.
- PXE Booting: Boot computers over the network using Preboot Execution Environment (PXE).
- Remote Management: Manage clients using the FOG client installed on the operating systems.
- Multicast Deployments: Simultaneously deploy images to multiple machines.


## Project Setup
1. FOG Server Installation on Ubuntu

To start, I installed the stable version of the FOG Project on an Ubuntu 22.04.5 LTS server. The installation was performed by following the official FOG installation guide.

Steps:
1. Update the Server: First, I updated the Ubuntu server to ensure all packages were up to date.

`sudo apt update && sudo apt upgrade`

2. Install Required Packages: Installed the necessary packages, including Apache, MySQL, PHP, and other dependencies required by FOG.

3. Run FOG Installer: Downloaded and executed the FOG installation script.

```
wget https://github.com/FOGProject/fogproject/archive/master.tar.gz
tar -zxvf master.tar.gz
cd fogproject-master/bin
sudo ./installfog.sh
```

During the installation, I selected:


- Normal FOG server (without DHCP as PfSense handles this).
- Skipped HTTPS for simplicity in this test environment.
  
4. Access the FOG Web Interface: Once the installation was complete, I accessed the FOG web portal at http://<fog-server-ip>/fog/management to finalize the setup.

   ![image](https://github.com/user-attachments/assets/6f700e74-513e-4e90-9e75-e61199de4eb1)



## 2. Windows Server 2022 (DC1) Setup
For the Windows environment, I installed Windows Server 2022 (Standard Evaluation with Desktop Experience) on the first domain controller (DC1). After installation, the system was prepared 
to capture the image via FOG.

### Steps:
1. Windows Server Installation: Installed Windows Server 2022 using the custom installation option, allocating the entire disk space to the OS.

`Administrator Password: something123`

2. FOG Client Installation: Installed the FOG client MSI on DC1 and specified the IP address of the FOG server during setup to allow communication between FOG and DC1.

3. Sysprep Preparation: Used the Sysprep tool to generalize the system for imaging. This ensures the captured image can be deployed across other systems without conflicts.

`C:\Windows\System32\Sysprep\sysprep.exe`

After running Sysprep, I configured DC1 to network boot without booting from the disk again before capturing the image.

## 3. Image Capture with FOG
After configuring DC1, I captured its image using FOG.

Steps:
1. Network Boot: Forced DC1 to boot from the network using Esc + F12 during the VMware boot sequence.

2. Host Registration in FOG: Registered DC1 as a new host in FOG during the PXE boot process, ensuring that the host was shut down afterward.

3. Image Capture: Configured the FOG server to capture the image from DC1, selecting Windows Other as the image type.


## 4. Image Deployment to DC2 and DC3
  After capturing the image from DC1, I deployed it to two additional domain controllers, DC2 and DC3.

Steps:
1. Network Boot and Host Registration:

- Both DC2 and DC3 were network booted, and I registered them in the FOG web portal.
- Set the hostnames as dc2 and dc3, respectively, and skipped snap-ins, product keys, domain joining, and primary user assignment.

2. Image Deployment: Deployed the captured image from DC1 to both DC2 and DC3, and both deployments were successful.

   ![image](https://github.com/user-attachments/assets/a7577a8d-f021-47f6-8adb-2a65648bfeae)

   ![image](https://github.com/user-attachments/assets/7d4e31e7-2117-4185-9972-3182d01acfe5)

   ![image](https://github.com/user-attachments/assets/ef2de66c-ee30-4622-b539-16e1c41d56d7)




## 5. PfSense Configuration for PXE Booting
To allow the systems to network boot, I configured our PfSense firewall to handle the PXE boot process.

### Steps:
1. PfSense Web Portal: Accessed the PfSense web portal using its LAN IP address.

2. DHCP Configuration: Modified the DHCP server settings to enable PXE booting. The necessary configurations included setting the next-server (FOG server IP address)
   and the filename (pxelinux.0) for PXE booting.

![image](https://github.com/user-attachments/assets/c85016bc-7592-43a1-995a-14b39887a1e7)


## Conclusion
This project successfully demonstrates the use of FOG Project for capturing and deploying images of Windows Server 2022 across multiple virtual domain controllers. FOG's flexibility and scalability 
make it a robust solution for environments requiring efficient OS deployment. Additionally, the configuration of PfSense for PXE booting enables seamless network-based installation, simplifying the
management of large-scale system rollouts.

By following the steps detailed in this documentation,it will be easier to extend the configuration for their specific use cases, and explore FOG’s broader features like automated installations and remote management.
