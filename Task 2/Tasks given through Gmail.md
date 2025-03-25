# Implementation
## Tasks which are done here:

### 1. Study the Existing Code:
  Access the uart_loopback project from the VSDSquadron_FM repository.
  Analyze the Verilog code to understand the loopback logic.

---

## UART Loopback Analysis

### Accessing the Project
The UART loopback project is available in the [VSDSquadron_FM repository](https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/uart_loopback/uart_trx.v). The key file for analysis is `uart_trx.v`, which contains the Verilog implementation for UART transmission and reception.

### Understanding the Verilog Code
The `uart_trx.v` module is designed to handle UART communication. Below is a structured analysis of its functionality:

#### Module Overview
- **Purpose**: Implements UART transmission with an 8N1 (8 data bits, no parity, 1 stop bit) format.
- **Components**:
  - `txbyte`: 8-bit data input for transmission.
  - `senddata`: Signal to initiate data transmission.
  - `tx`: Serial data output.
  - `txdone`: Indicates completion of transmission.
  - `clk`: System clock input.

#### State Machine Logic
The transmission logic is controlled by a finite state machine (FSM) with the following states:

1. **STATE_IDLE**: The module waits for `senddata` to be asserted.
2. **STATE_STARTTX**: Sends the start bit (logic low).
3. **STATE_TXING**: Sends the 8-bit data sequentially, LSB first.
4. **STATE_TXDONE**: Sends the stop bit (logic high) and completes transmission.

#### Loopback Mechanism
- **Concept**: In a loopback system, transmitted data (`tx`) is fed back into the receiver (`rx`).
- **Implementation in the Module**: The provided `uart_trx.v` module includes only transmission logic but is part of a broader system where received data is expected to be read back.
- **Expected Behavior**: If the `tx` signal is internally connected to `rx`, the system should be able to verify correct data transmission by comparing received data with sent data.

### Conclusion
The `uart_trx.v` module provides UART transmission functionality using an FSM. The transmission process involves sending a start bit, data bits, and a stop bit in sequence. The loopback mechanism relies on feeding the transmitted `tx` back into `rx` for verification within the system.

---

## Tasks which are done here:

### 2. Design Documentation:
  Create a block diagram illustrating the UART loopback architecture.
  Develop a detailed circuit diagram showing connections between the FPGA and any peripheral devices used.

---

## UART Loopback Block Diagram

```lua
          +-------------------+
          |      FPGA         |
          | +--------------+  |
  clk --->| |  UART TX     |  |
          | |              |  |
          | |   TX ------->|-----> TX_OUT
          | |              |  |
          | |   RX <-------|<----- RX_IN
          | |  UART RX     |  |
          | +--------------+  |
          +-------------------+
```

TX_OUT is looped back to RX_IN to create the loopback effect.

clk is the clock signal driving the FPGA.

--

## UART Loopback Circuit Diagram

```lua
     +--------------------+
     |     USB-UART       |
     |  (FTDI/CP2102)     |
     |                    |
     |   TX  ------------>|------> RX (FPGA)
     |   RX  <------------|<------ TX (FPGA)
     |   GND ------------>|------> GND (FPGA)
     |   VCC ------------>|------> VCC (FPGA)
     +--------------------+

     +--------------------+
     |       FPGA         |
     |                    |
     |   TX  ------------>|------> RX (USB-UART)
     |   RX  <------------|<------ TX (USB-UART)
     |   GND ------------>|------> GND (USB-UART)
     |   VCC ------------>|------> VCC (USB-UART)
     +--------------------+
```

The USB-UART module (like an FTDI chip) is connected to the FPGA for serial communication.

The TX of one device is connected to the RX of the other, and vice versa.

GND and VCC connections ensure proper power and signal reference.

---
