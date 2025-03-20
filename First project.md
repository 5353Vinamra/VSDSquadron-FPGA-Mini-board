# Making a RGB LED blink in VSDSquadron FPGA mini board

After you have done the setup you will see a window as shown. Take your cursor in the middle of the screen and
right click on mouse. You will see lot of options as shown. You need to left
click on terminal as marked in RED in below.

![VirtualBox_FPGA_20_03_2025_16_09_54](https://github.com/user-attachments/assets/6487ff95-5215-4a96-aca8-c8c127e8b4b2)

You should see a terminal window as shown in below Fig

![VirtualBox_FPGA_20_03_2025_16_12_56](https://github.com/user-attachments/assets/6806daa0-bb3d-4029-a3ac-99fd21a9f459)

Let’s start our first project. To navigate through project directories in a UNIX environment,
use the following commands:
```bash
cd
```
```bash
cd VSDSquadron_FM
```
```bash
cd blink_led
```
The commands above allow you to:
– Change to the home directory (‘cd‘).
– Navigate to the ‘VSDSquadron FM‘ folder, which has a sample project.
– Move into the ‘blink led‘ directory, which is the first FPGA project to be tried on VSDSquadron FPGA Mini (FM) board.

Refer to the figure below

![VirtualBox_FPGA_20_03_2025_16_15_51](https://github.com/user-attachments/assets/01ff32ea-40b7-4ac2-885c-9bcc0ab96c90)

There is a preloaded project in ”blink led” directory. To test the project on VSDSquadron
FPGA Mini (FM) board, we need to make sure that the board is connected to the Oracle
Virtual Machine. Perform below steps
– Connect the board to your PC, as shown.

![WhatsApp Image 2025-03-20 at 16 21 08_f2f36584](https://github.com/user-attachments/assets/253b9538-d820-418a-bbd3-6668f00abbf9)

– On the Virtual Machine, click on ”Devices → USB → FTDI Single RS232-HS [J900]” as
shown

![image](https://github.com/user-attachments/assets/2624f754-49f7-42eb-85dd-471c2ae114f0)

To confirm if the board is connected to the USB, type the ‘lsusb‘ command in the terminal.
You should see a line stating ”Future Technology Devices International,” as shown

![VirtualBox_FPGA_20_03_2025_16_26_49](https://github.com/user-attachments/assets/47d3752b-c262-4967-a390-c72f450d0a35)

To program the VSDSquadron FPGA Mini (FM) board, follow these steps:
– Run the following command to clean up previous builds. Refer to Fig.

```bash
make clean
```
![VirtualBox_FPGA_20_03_2025_16_28_54](https://github.com/user-attachments/assets/1f6b0875-1a67-4d1e-9a35-202d81be5e85)

Build the binaries for the FPGA board using below command. Below Fig shows the output
screen after ‘make build‘ command:

```bash
make build
```

![VirtualBox_FPGA_20_03_2025_16_30_50](https://github.com/user-attachments/assets/488c3add-1d6f-49fa-88ba-6463f59051c2)



