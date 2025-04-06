
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

```
