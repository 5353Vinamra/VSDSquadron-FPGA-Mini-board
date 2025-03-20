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
Run the following command to clean up previous builds. Refer to Fig.

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

Flash the code to the external SRAM with the following command:

```bash
 sudo make flash
```

After executing the above command, the screen will look as shown

![VirtualBox_FPGA_20_03_2025_16_34_11](https://github.com/user-attachments/assets/377725d3-3e70-4c61-bda8-bd264aec99c4)

Enter the password "vsdiat"

Note: while typing the password you will not see the  letters you type so don't worry just type "vsdiat" and press enter

Now if you get error as shown below then probably the board got disconnected.

![VirtualBox_FPGA_20_03_2025_16_35_57](https://github.com/user-attachments/assets/2f608b07-8c47-4fcd-94bd-c04414db1b59)

Try repeating the step as shown.

– On the Virtual Machine, click on ”Devices → USB → FTDI Single RS232-HS [J900]” as
shown

![image](https://github.com/user-attachments/assets/2624f754-49f7-42eb-85dd-471c2ae114f0)

Now write the command again

```bash
sudo make flash
```

It will take around 2.5 minute so be calm. and after the output will be this as shown

![VirtualBox_FPGA_20_03_2025_16_41_20](https://github.com/user-attachments/assets/eb964c53-8b7a-4969-960c-7f81b55cfca4)

Once the code is successfully flashed, you will see the RGB lights on the FPGA board
blinking as shown

https://github.com/user-attachments/assets/5b652545-6f92-4a2a-ab69-ff5f9841f86c

**And your first program is done**




