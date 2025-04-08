
# Steps:

## Study the Existing Code:
  Access the uart_tx project from the [VSDSquadron_FM repository](https://github.com/5353Vinamra/VSDSquadron-FPGA-Mini-board/tree/main/Task%203/uart_tx).
  Examine the Verilog code to understand the transmission process.
## Design Documentation:
  Create a block diagram detailing the UART transmitter module.
  Develop a circuit diagram illustrating the FPGA's UART TX pin connection to the receiving device.
## Implementation:
  Assemble the hardware setup as depicted in the circuit diagram.
  Synthesize and program the FPGA with the UART transmitter code.
## Testing and Verification:
  Connect the FPGA to a PC or another UART-compatible device.
  Use a serial terminal to observe the transmitted data, ensuring accurate data transmission.
## Documentation:
  Document the block diagram, circuit diagram, code walkthrough, and testing outcomes.
  Produce a short video showcasing the UART transmission in operation.

# Implementation:

## Task which are done here
  Access the uart_tx project from the [VSDSquadron_FM repository](https://github.com/5353Vinamra/VSDSquadron-FPGA-Mini-board/tree/main/Task%203/uart_tx).
  Examine the Verilog code to understand the transmission process.


### UART Transmitter Module (8N1) - Verilog Code Analysis

This document analyzes the `uart_tx_8n1` Verilog module, which implements a transmit-only UART protocol using an 8N1 configuration (8 data bits, No parity, 1 stop bit).

---

#### 📌 Module Overview

module uart_tx_8n1 (
    clk,        // input clock
    txbyte,     // outgoing byte
    senddata,   // trigger transmission
    txdone,     // signal indicating byte has been sent
    tx          // UART transmit wire
);

---

## 🎯 Purpose

The module sends a single byte (`txbyte`) over UART when triggered by the `senddata` signal. It follows the 8N1 format:
- **1 Start Bit (Low)**
- **8 Data Bits (LSB First)**
- **1 Stop Bit (High)**

---

## 🔧 Inputs and Outputs

| Signal     | Direction | Description                              |
|------------|-----------|------------------------------------------|
| `clk`      | Input     | System clock                             |
| `txbyte`   | Input     | Byte to be transmitted                   |
| `senddata` | Input     | Trigger signal to start transmission     |
| `txdone`   | Output    | High when transmission completes         |
| `tx`       | Output    | Serial output bitstream (TX line)        |

---

## 🧠 Internal Logic & FSM States

The module uses a finite state machine (FSM) with four states:

| State Name      | Value   | Description                          |
|-----------------|---------|--------------------------------------|
| `STATE_IDLE`    | 0       | Wait for `senddata` to start sending |
| `STATE_STARTTX` | 1       | Send the start bit (0)               |
| `STATE_TXING`   | 2       | Transmit 8 bits, LSB first           |
| `STATE_TXDONE`  | 3       | Send stop bit (1) and finish         |

---

## ⚙️ Transmission Process

### 1. Idle State (`STATE_IDLE`)
- Waits for `senddata == 1`.
- Sets `txbit` high (idle state).
- Loads `txbyte` into buffer (`buf_tx`) when sending starts.

### 2. Start Bit (`STATE_STARTTX`)
- Sends a start bit (`txbit = 0`).
- Moves to transmit state.

### 3. Data Transmission (`STATE_TXING`)
- Sends 8 bits one-by-one from LSB to MSB.
- Uses `buf_tx` and shifts it right each clock cycle.
- Tracks bits sent using `bits_sent`.

### 4. Stop Bit and Completion (`STATE_TXDONE`)
- Sends stop bit (`txbit = 1`).
- Sets `txdone = 1`.
- Resets state to idle.

---

## ⚠️ Notes and Improvements

- **Bit Timing**: The module sends one bit per clock cycle. In real UART, a **baud rate divider** is required to match timing (e.g., 115200 baud).
- **Non-blocking Assignments**: Use `<=` instead of `=` for synthesizable sequential logic.
- **FSM Structure**: Consider using a `case` statement for better clarity and structure.
---

## ✅ Conclusion

This UART TX module demonstrates a basic transmit mechanism using FSM logic and bit-shifting. It’s suitable for simulation and understanding UART transmission but needs timing control and synthesis-friendly coding for real-world applications.

## Task which are done here
  
2. Design Documentation:
  
Create a block diagram detailing the UART transmitter module.
Develop a circuit diagram illustrating the FPGA's UART TX pin connection to the receiving device.
---

## UART Transmitter Module – Design Documentation

---

### 📦 Block Diagram: UART Transmitter (8N1)

  This block diagram shows the internal structure of the UART transmitter module.

```
+-----------------------------+
|       uart_tx_8n1          |
|-----------------------------|
|                             |
|  +-----------------------+  |
|  | Finite State Machine  |  |
|  | (IDLE, START, DATA,   |  |
|  |  STOP, DONE)          |  |
|  +-----------------------+  |
|              |              |
|              v              |
|     +----------------+      |
|     |  Bit Counter   |      |
|     +----------------+      |
|              |              |
|              v              |
|     +----------------+      |
|     |  Shift Register|<--txbyte
|     +----------------+      |
|              |              |
|              v              |
|            [txbit]---------> tx
|                             |
|       [txdone]------------> txdone
+-----------------------------+
```

---

### 🔌 Circuit Diagram: UART TX Connection

This circuit shows the FPGA’s UART TX pin connected to an external device such as a computer or microcontroller.

```
          +---------------------+
          |      FPGA Board     |
          |                     |
          |  +---------------+  |
          |  |   uart_tx_8n1 |  |
          |  +---------------+  |
          |        |            |
          |     TX |------------+------------------+
          |        |            |                  |
          +--------|------------+                  |
                   |                               |
                [3.3V TTL]                      [3.3V TTL]
               UART TX Line                   UART RX Line
                   |                               |
                   |                               |
              +----+-----+                    +----+-----+
              |  Receiving| Device (e.g., PC,  |  USB     |
              |  MCU, or  | USB-UART Bridge)   |  UART    |
              |  Serial   |                    | Adapter  |
              +----------+                    +----------+
```

**Notes:**
- Ensure both devices operate at the same logic level (e.g., 3.3V).
- A USB-UART bridge (like FTDI or CH340) is typically used for PC connection.
- Only the TX line from FPGA and GND are required for one-way communication.

---

### ⚠️ Important Considerations

- **Baud Rate Matching**: Make sure the receiver expects the same baud rate (e.g., 115200).
- **Voltage Levels**: Match logic levels between FPGA and external device.
- **Decoupling Capacitors**: Always place near power pins of FPGA or USB chips if applicable.

---

### ✅ Summary

- The block diagram explains how data flows through the UART transmitter FSM.
- The circuit diagram shows how to physically connect the UART TX pin to a receiving device.
---

## Tasks which are done here:

3. Implementation:

  Assemble the hardware setup as depicted in the circuit diagram.
  Synthesize and program the FPGA with the UART transmitter code.

---
Steps to Transmit the Code
1. Create the following [files](https://github.com/5353Vinamra/VSDSquadron-FPGA-Mini-board/tree/main/Task%203/uart_tx) in a folder under VSDSquadron_FM.
2. Then, open terminal and through the commands "cd"; "cd VSDSquadron_FM" and "cd uart_transmission" enter the folder "uart_transmission", where you have created the files.
3. Post this, you may verify that the board is connected through "lsusb" command.
4. After this, run "make build" and "sudo make flash".
   ![image](https://github.com/user-attachments/assets/dae53f37-7766-4856-8c62-73f191db270b)


## Tasks which are done here:

4.Testing and Verification:
  Connect the FPGA to a PC or another UART-compatible device.
  Use a serial terminal to observe the transmitted data, ensuring accurate data transmission.
---

Steps of Testing and Verification
1. Install, and then open PuTTy.
2. Verify that the correct port is connected through serial communication (COM 7 in my case)
3. Then, check that a series of "D"s are generated and the RGB LED is blinking (switching between red, green and blue) .
---
## Tasks which are done here

Documentation:
1. Document the block diagram, circuit diagram, code walkthrough, and testing outcomes.
2. Produce a short video showcasing the UART transmission in operation.

Video
https://github.com/user-attachments/assets/d7746c58-76d4-4cd2-9365-6f46dafff968
---

