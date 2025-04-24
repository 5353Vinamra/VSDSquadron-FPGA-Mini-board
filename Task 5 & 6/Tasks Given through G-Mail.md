# Tasks Which are done here:

## Study the Existing Code:
Access the uart_loopback project from the [VSDSquadron_FM repository](https://github.com/5353Vinamra/VSDSquadron-FPGA-Mini-board/tree/main/Task%205%20%26%206/uart_loopback).
Analyze the Verilog code to understand the loopback logic.

## Design Documentation:
Create a block diagram illustrating the UART loopback architecture.
Develop a detailed circuit diagram showing connections between the FPGA and any peripheral devices used.

---

I have fully analyzed the code here is the block diagram
![717hajtH6PL-removebg-preview](https://github.com/user-attachments/assets/1d2e028b-f9d5-44cc-a461-2715f0e24bd7)

Circuit Diagram
```
                             +-----------------------------+
                             |     Ultrasonic Sensor       |
                             |         HC-SR04             |
                             |                             |
                             |  Vcc ------> +5V            |
                             |  GND ------> GND            |
                             |  Trig -----> FPGA GPIO_OUT  |
                             |  Echo <----- FPGA GPIO_IN   |
                             +-----------------------------+
                                        |
                                        v
                             +-----------------------------+
                             |            FPGA             |
                             |     VSDSquadron Mini        |
                             |                             |
                             |  GPIO_OUT --> Trigger Pin   |
                             |  GPIO_IN  <-- Echo Pin      |
                             |                             |
                             |  Distance = (Time × Speed)/2|
                             |                             |
                             |  IF Distance < 5 cm         |
                             |     --> Output LOW to Buzzer|
                             |  ELSE                       |
                             |     --> Output HIGH (Silence)|
                             +-----------------------------+
                                        |
                                        v
                             +-----------------------------+
                             |           Buzzer            |
                             |                             |
                             |  IN  <----- FPGA Output      |
                             |  GND ------> GND             |
                             |  Active Low --> Sound ON     |
                             +-----------------------------+
```

🔧 Physical Components
FPGA Board

Handles logic and signal processing

Interfaces with sensors and actuators

Controls timing and output

Ultrasonic Sensor (HC-SR04)

Measures distance using echo time

Range: 2 cm to 400 cm

Outputs a pulse proportional to distance

Buzzer

Active-low trigger

Emits tone when activated

🔄 Signal Flow
Trigger Pin (Digital Output)
→ Sends a short HIGH pulse to initiate distance measurement.

Echo Pin (Digital Input)
→ Receives the return pulse from the sensor.

Time Measurement
→ FPGA calculates duration of the pulse on Echo Pin.

Distance Calculation
→ Distance = (Time × Speed of Sound) / 2

Comparison Logic

If distance < 5 cm
→ Output LOW to buzzer (activate sound)

Else
→ Keep buzzer in HIGH state (silent)

🔁 System Behavior
Object < 5 cm detected:
→ FPGA generates square wave → buzzer ON

No object or object > 5 cm:
→ No square wave → buzzer OFF

# Tasks which are done here:

## Implementation:
Set up the hardware as per the circuit diagram.
Synthesize and upload the Verilog code to the VSDSquadron FPGA Mini.

---

Here is the [code](https://github.com/5353Vinamra/VSDSquadron-FPGA-Mini-board/tree/main/Task%205%20%26%206/Task%205%266%20Necessary%20files)
Make a folder under VSDSquadron_FM

open terminal there
run the code 
```
make build
```
```
sudo make flash
```

and transfer it to the FPGA board. now open docklight chose serial communication set the BAUDS to 9600, 1 stop bit, data bits 8 and parity to none.

and you are done

now test your project with an object if it come closer to the ultrasonic sensor then the buzzer will start beeping you can put it in your house or somewhere so wheneven some stranger enter and the movement is detected the buzzer will start beeping alerting you.

here is the video to show how it beeps and works.


https://github.com/user-attachments/assets/bb5b70b3-2489-4d6c-8be5-6648979c8523


