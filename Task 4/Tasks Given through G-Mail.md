# Tasks which are done here:

## Study the Existing Code:
Access the uart_tx_sense project from the VSDSquadron_FM repository.
Review the Verilog code to understand how sensor data is acquired and transmitted.

---

# UART TX Sense Module Analysis

The `uart_tx_sense` module is a Verilog-based design that handles the task of reading sensor data and transmitting it over a UART line. It's built to work efficiently with sensor systems where continuous or triggered data transmission is needed.

## Overview

The module starts by receiving **32-bit wide sensor data** through the `data` input line. This data becomes valid when the `valid` signal goes high. Internally, the module captures this data and holds it in a buffer. Transmission only begins when the module is in an **idle** state and the data is marked as **valid**.

## UART Transmission

A UART transmission protocol is used for communication. This involves sending:
- a **Start Bit** (logic low),
- followed by **8 bits of actual data**,
- and ending with a **Stop Bit** (logic high).

This process is repeated until the entire 32-bit sensor reading is transmitted—most likely broken into four 8-bit segments.

## Finite State Machine (FSM)

An internal FSM (Finite State Machine) manages:
- The sequencing of **start**, **data**, and **stop** bits
- Ensuring proper timing with respect to the **UART baud rate**
- Synchronization of data capture and transmission

## Ports and Signals

- `clk`: Main clock signal for synchronous operations
- `reset_n`: Active-low asynchronous reset
- `data[31:0]`: Input for sensor data
- `valid`: Input handshake signal indicating valid data
- `ready`: Output indicating readiness to accept new data
- `tx_out`: UART-compliant serial output signal

## Functionality Summary

- Captures and stores sensor data on valid signal
- Sends data over UART in a byte-wise fashion
- Uses internal FSM for reliable, protocol-compliant transmission
- Provides status through `ready` signal and serial output through `tx_out`

This design ensures clean separation between data acquisition and transmission, allowing **reliable and consistent UART output** of sensor data.

---

# Tasks which are done here:

## Design Documentation:
Create a block diagram depicting the integration of the sensor module with the UART transmitter.
Develop a circuit diagram showing connections between the FPGA, sensor, and the receiving device.

---

Block Diagram

```bash
+-----------------+     +--------------------+     +------------------+     +---------------------+
|   Sensor Module | --> |  Sensor Interface  | --> | UART Transmitter | --> |  UART Receiver (PC) |
+-----------------+     |   (Verilog logic)  |     |    (uart_tx.v)   |     |  via USB-to-Serial  |
                        +--------------------+     +------------------+     +---------------------+
                                  |                          |
                            [sensor_data]              [tx_serial_line]

```

```bash
                             +------------------------+
                             |      Sensor Module     |
                             |  (e.g., TMP36 or LDR)  |
                             |                        |
                             |  Vcc ----> +3.3V        |
                             |  GND ----> GND          |
                             |  OUT ----> FPGA GPIO    |
                             +------------------------+
                                        |
                                        v
                             +------------------------+
                             |         FPGA           |
                             |   VSDSquadron Mini     |
                             |                        |
                             |  GPIO <--- Sensor OUT  |
                             |                        |
                             |  UART_TX --> TX Pin -->|----+
                             |                        |    |
                             |                GND --> |    |
                             +------------------------+    |
                                                           |
                                                           v
                                      +--------------------------------+
                                      |      Receiving Device (PC)     |
                                      |   (e.g., USB-to-Serial Cable)  |
                                      |                                |
                                      |  RX  <---- UART_TX             |
                                      |  GND <---- GND                 |
                                      +--------------------------------+
```

---

# Tasks which are done here:

Implementation:
Set up the hardware according to the circuit diagram, ensuring proper sensor interfacing.
Synthesize and load the Verilog code onto the FPGA.

--- 

## 🚀 Uploading the Design to FPGA

1. **Create a New Folder**  
   Inside your `VSDSquadron_FM` directory, make a subfolder (e.g., `uart_tx_sense`) and place all the necessary Verilog files there.

2. **Navigate to the Project Directory**  
   Open your terminal and use the following commands to move into the folder:
   ```bash
   cd VSDSquadron_FM
   cd uart_tx_sense
   ```

3. **Check Board Connection**  
   Use the command below to confirm your FPGA board is properly connected via USB:
   ```bash
   lsusb
   ```

4. **Build and Flash**  
   Once you’re in the correct directory and the board is detected, compile and upload the design using:
   ```bash
   make build
   sudo make flash
   ```

---
