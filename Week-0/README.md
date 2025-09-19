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
Dear Friend we need a Computer machine either Installed with Windows 10/11 or Linux - Ubuntu / Centos or Mac. If we dont have Linux machine then we need a 🎯 Virtual Machine Configuration for which Instruction Pdf is provided. 
Here the link is given to download Oracle virtual machine (https://www.virtualbox.org/wiki/Downloads) Just download with the given link and install like normal software you usually do with double click on the executable.


<img width="486" height="305" alt="image" src="https://github.com/user-attachments/assets/0f4cb3c9-f443-4d19-b174-1ff4d0e14bff" />

Click on "Windows Host" and a file named VirtualBox-7.2.2-170484-Win.exe get downloaded on your system.

## What is VirtualBox? What is its Role in Tool Installation.
VirtualBox is a free and open-source virtualization software developed by Oracle. It allows you to run multiple operating systems (OS) on your computer at the same time — like running Linux inside Windows or macOS. Here your main computer’s e.g., Windows, macOS becomes Host and VirtualBox becomes application on top to execute the our Guest OS i.e. Ubuntu Linux. There we will install the Opensource tools like (Yosys, OpenLane, Magic, etc.). 

## How to install the Ubuntu 20.04 on this VirtualBox.
Before this make sure that VirtualBox is already installed in your windows machine. Now for Ubuntu 20.04+ please visit the link (https://ubuntu.com/download). Move to Desktop Ubuntu for download.


<img width="1614" height="664" alt="image" src="https://github.com/user-attachments/assets/93e36ff4-2f14-4e4c-b9c6-91771a2c1972" />

In this Picture the Blue tick shows the forward path you need to click upon for Ubuntu ISO image download on your computer. On click you will land to the page showing the Intel or AMD 64-bit architecture dont choose ARM one (in general it is not for us).

<img width="1006" height="273" alt="image" src="https://github.com/user-attachments/assets/89f1aed6-784a-4ce4-a2fc-5766d2afb3a1" />

The file size is 5.9 GB will take some time, so, have patience. 

## What is Iso image? 
Most Linux distros (like Ubuntu, Fedora) are distributed as ISO files. An ISO image is a single file that contains the complete contents of a CD, DVD, or Blu-ray disc.
It’s basically a digital copy (archive) of the entire disc, including
-  👉 Operating system files
-  👉 Bootable data (so you can install OS)
-  👉 File system structure

## Now we will give steps to install the this Ubuntu.ISO image with Virtualbox.
Please check this 3 minute video for installation of ubuntu only. Choose your OS machine to Win 10/ 11 in the first section. Dont worry if not known to you. Just move with video.


https://github.com/user-attachments/assets/87c6f65a-90e2-4cdc-887e-dd326d633b22

## ⚙️ Tool Installation & Verification 
- 👉 Now inside the VirtualBox you will have Ubuntu. Here right click and open an terminal. IF you dont find see the image below

  
 <img width="1033" height="565" alt="image" src="https://github.com/user-attachments/assets/918a1a17-9757-4b73-b558-128c684036ac" />
- Now the terminal will open and you can see the <code style="color : blue">username@hostname</code> so our username is  "vsdgp" and Hostname is "VSD". This is exactly what we have done in VirtualBox setting as
  shown below

  <img width="937" height="479" alt="image" src="https://github.com/user-attachments/assets/49bc4889-74f7-4891-b6b6-5abdf4560980" />

- Now, do "ls" to list the directory in terminal and "cd" to change the directory. These are two basic commands.

<img width="936" height="477" alt="image" src="https://github.com/user-attachments/assets/8a0e5182-097f-41b7-959f-c589578f5f65" />

- First Just call the <code style="color : blue">git --version</code> command in the terminal. It should return if you have the Git tool for downloading the repos. Let's try.
  
<img width="943" height="478" alt="image" src="https://github.com/user-attachments/assets/9e28315a-35b4-4a25-aec1-86ab5b7de56c" />

The picture clearly shows that the git need to be installed, it also gives us the Hint that use "sudo apt install git" is the command.


<img width="936" height="470" alt="image" src="https://github.com/user-attachments/assets/517f7259-44c7-4e6e-bb2c-488ef3a9053e" />

- Once it is done, it will ask your password , Then it will ask "Y/y" Yes for the changes in the system and as download is complete it will show some version on checking it again

<img width="934" height="476" alt="image" src="https://github.com/user-attachments/assets/bb20bdd1-c56e-4afa-bbb4-7cb45b6f9f67" />

- Friends, you have completed the major step of Installation, Now for ✅ Yosys Installation we need create a "vsdflow" folder using mkdir command. Then change the current directory to vsdflow using cd command and clone the repository ($ git clone https://github.com/YosysHQ/yosys.git) and the dependencies as  - shown in the VSD Pdf.

<img width="939" height="481" alt="image" src="https://github.com/user-attachments/assets/232676a1-fcd8-446f-8974-0ea2e6cf70fc" />

- Now clone the Yosys,  "git clone https://github.com/YosysHQ/yosys.git"

<img width="1267" height="365" alt="image" src="https://github.com/user-attachments/assets/23575dff-886a-4304-bcc9-a75ea72fb3ba" />

- Yosys is downlaoded but still need installation. "ls" will list the folder we downloaded with git command.

<img width="1395" height="852" alt="image" src="https://github.com/user-attachments/assets/aa24b7fa-8a9a-4601-8e19-44a62ebf20ba" />

- Now change directory to yosys to install. But we need to use "make" command to install. Hence first "make" needs to be installed.

<img width="1308" height="442" alt="image" src="https://github.com/user-attachments/assets/83c3c6b7-2e01-4c55-aed5-d8917422d674" />

- copy and paste below commands on terminal as shown and press "y" When and whereever asked. It will take some time depending on Internet speed.
  
<img width="643" height="140" alt="image" src="https://github.com/user-attachments/assets/21764ad1-f03a-455e-942f-21879ddd036f" />


<img width="1267" height="295" alt="image" src="https://github.com/user-attachments/assets/420d4b8d-221f-43d8-95b5-caf47ca683ab" />

- Now first "make" in same folder and then "sudo make install".

<img width="1265" height="195" alt="image" src="https://github.com/user-attachments/assets/6ec7b39d-ce1c-48b4-8645-2345aece4144" />

- if you land to ❌ Error

<img width="1263" height="194" alt="image" src="https://github.com/user-attachments/assets/ad3ee169-0a24-4a1c-9121-211b6f2fc1bc" />

- after 💡 Solution Applied 

<img width="1265" height="315" alt="image" src="https://github.com/user-attachments/assets/01d62b57-af17-4c65-b19e-e1ac37e9afa8" />

- Running again "make" it take will some time. Have a ☕ Coffee break and when you come back it will be ✅ Done

<img width="1267" height="593" alt="image" src="https://github.com/user-attachments/assets/cf5d188c-54df-49da-876a-24409325328a" />

<img width="1299" height="720" alt="image" src="https://github.com/user-attachments/assets/e680ff13-a683-4801-9915-7ccf5d970712" />

- After make install

<img width="817" height="598" alt="image" src="https://github.com/user-attachments/assets/8e700ae9-1685-41bb-9232-ef46d0fb46e0" />

- exit to comeout on terminal

<img width="813" height="592" alt="image" src="https://github.com/user-attachments/assets/75936c8c-0bd4-4f79-8c1a-fce4bebc6684" />


```bash
$ mkdir vsdflow
$ cd vsdflow
$ git --version
$ sudo apt install git # (If git reports no version. please install it) 
$ git clone https://github.com/YosysHQ/yosys.git
$ cd yosys
$ sudo apt install make # (If make is not installed please install it) 
$ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
$ make
$ git submodule update --init --recursive (if make reports error) 
$ make (run make again)
$ sudo make install
$ yosys (test yosys)





