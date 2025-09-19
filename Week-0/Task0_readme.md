##  🚀 Week 0: VLSI System Design (VSD) Program Foundation & Tool Setup 
📦 *Tools Installed in Week 0 - Task 0*

## 🖥️ Virtual Machine Configuration requirement (Ubuntu 20.04+)

| Resource   | Allocation     | Remarks                                |
|------------|----------------|----------------------------------------|
| 💾 RAM     | 6 GB           | Sufficient for running EDA tools smoothly |
| 📀 Storage | 50 GB HDD      | Enough space for OS + tools + projects |
| 🧮 CPU     | 4 vCPUs        | Provides parallelism for synthesis & PnR |
| 🐧 OS      | Ubuntu 20.04+  | Recommended version for open-source EDA tools |


## What type of machine we need to prepare it for Week - 0 Task ?
Dear Friend we need a Computer machine either Installed with Windows 10/11 or Linux - Ubuntu / Centos or Mac. If we dont have Linus machine then we need a 🎯 Virtual Machine Configuration for which Instruction Pdf is provided. 
Here Oracle virtual machine link is given (https://www.virtualbox.org/wiki/Downloads) Just download the virtual machine


<img width="486" height="305" alt="image" src="https://github.com/user-attachments/assets/0f4cb3c9-f443-4d19-b174-1ff4d0e14bff" />

Click on "Windows Host" and a file named VirtualBox-7.2.2-170484-Win.exe get downloaded on your system.

## What is VirtualBox? What is its Role in Tool Installation.
VirtualBox is a free and open-source virtualization software developed by Oracle. It allows you to run multiple operating systems (OS) on your computer at the same time — like running Linux inside Windows or macOS, without needing to restart your machine. Here your main computer’s e.g., Windows, macOS, Linux becomes a Host and VirtualBox becomes middle man where the Guest OS i.e. Ubuntu get runs. On top of this we will install the Opensource tools (Yosys, OpenLane, Magic, etc.). 

## How to install the Ubuntu 20.04 on this VirtualBox.
Before this We are sure that VirtualBox is already installed. Now for Ubuntu 20.04+ we will visit the site (https://ubuntu.com/download). Here amon many move to Desktop Ubuntu for download


<img width="1614" height="664" alt="image" src="https://github.com/user-attachments/assets/93e36ff4-2f14-4e4c-b9c6-91771a2c1972" />

Here, In the Picture the Blue tick shows the way we will click upon and download. Next on click you will land to the page with the Image as shown below

<img width="1006" height="273" alt="image" src="https://github.com/user-attachments/assets/89f1aed6-784a-4ce4-a2fc-5766d2afb3a1" />

The file size is 5.9 GB will take some time, so, have patience. 

## What is Iso image? 
Most Linux distros (like Ubuntu, Fedora) are distributed as ISO files. An ISO image is a single file that contains the complete contents of a CD, DVD, or Blu-ray disc.
It’s basically a digital copy (archive) of the entire disc, including
-  👉 Operating system files
-  👉 Bootable data (so you can install OS)
-  👉 File system structure

## Now we will give steps to install the this Ubuntu.ISO image with Virtualbox.




