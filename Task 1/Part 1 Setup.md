# VSDSquadron-FPGA-Mini-board
## How to setup

### Installation and Settings
This section provides information about the software and hardware settings required to run ”RGB
blink LED” program on the VSDSquadron FPGA Mini (FM) board using Yosys, nextpnr and
Project IceStorm
Installation Instructions for Windows Users
 Make sure your C drive or D drive has atleast 100GB space

![image](https://github.com/user-attachments/assets/75024056-c3bf-41cb-8e2d-1782bf0c70f3)

Download [VSDSquadron FPGA Mini (FM) Software](https://forgefunder.com/~kunal/vsdsquadron_fpga_mini.zip) Software on your laptop

Unzip the downloaded file and follow the below instructions

Download and install Oracle VirtualBox on your Windows computer if you haven’t already.
You can download it from the [official website](https://www.virtualbox.org/wiki/Downloads)

Launch VirtualBox and click on the ”New” button to create a new virtual machine. Fill up
the details exactly as highlighted in RED as shown.
In the ”Create Virtual Machine” wizard, enter a name for the virtual machine and select the
operating system type as Linux and version as Xubuntu (64-bit) that matches the one installed
in the VDI file you want to open. And then click on ”Next”

![image](https://github.com/user-attachments/assets/71747ee4-2ff2-47f5-8db6-5b1844b96859)

On the next screen, allocate memory as 4GB (or 4096 MB) and number of CPUs as 4, as
shown. Then click on ”Next” button

![image](https://github.com/user-attachments/assets/d3703e8d-8dfa-4586-9f13-e526a5add2f5)

Create a virtual hard disk. Choose the ”Use an existing virtual hard disk file” option and
click on the folder icon to browse to the location of the unzipped VDI file on your Windows
computer as shown. Once done, click on ”Next”

![image](https://github.com/user-attachments/assets/cbdd590c-673f-4e1d-b6d9-fb428c006458)

Click on the ”Finish” button as shown.

![image](https://github.com/user-attachments/assets/91d2068c-6a2c-4391-b643-8d8d3aa4acd7)

Once the virtual machine is created, select it from the list of available virtual machines in the
VirtualBox Manager and click on the ”Start” button to launch it, as shown. It would
take about 2-3 minutes to start the virtual machine

![image](https://github.com/user-attachments/assets/3d1b95a6-d056-4ed3-89ce-acf3f52cf13a)

The VM will start and prompt for password as shown. Enter the password as ”vsdiat”
and click on ”Log In” button

![image](https://github.com/user-attachments/assets/a68e9830-d6dc-490d-a33e-6de625ff1ae9)

Now you have done the setup of the Virtual Machine for programming in VSDSquadron FPGA mini.







