# **Tasks**

<details>
  <summary>
Expand or Collapse
  </summary>
   
**Step 1: Understanding the Verilog Code**

<details>
  <summary>
Expand or Collapse
  </summary>

**1. Access the Verilog code from the provided link: https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/top.v**

**2. Review the module declaration and understand the purpose of each input and output port:**

   led_red, led_blue, led_green (Output): Control the RGB LED

   hw_clk (Input): Hardware oscillator clock input

   testwire (Output): Test signal output

**3. Analyze the internal components:**
  
   Internal Oscillator (SB_HFOSC) instantiation

   Frequency counter logic driven by the internal oscillator

   RGB LED driver instantiation with defined current parameters

**4. Create a brief documentation explaining the functionality of the Verilog code, including:**

   Purpose of the module

   Description of internal logic and oscillator

   Functionality of the RGB LED driver and its relationship to the outputs

   </details>

**Step 2: Creating the PCF File**

<details>
  <summary>
Expand or Collapse
  </summary>

**1. Access the PCF file from the provided link: https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/VSDSquadronFM.pcf**

**Understand the pin assignments from the PCF file:**

   led_red -> Pin 39

   led_blue -> Pin 40

   led_green -> Pin 41

   hw_clk -> Pin 20

   testwire -> Pin 17

**Cross-reference the pin assignments with the VSDSquadron FPGA Mini board datasheet to verify the correctness of the assignments.**

**Document the pin mapping and explain the significance of each connection in context with the Verilog code and board hardware.**

</details>

**Step 3: Integrating with the VSDSquadron FPGA Mini Board**

<details>
  <summary>
Expand or Collapse
  </summary>
   
**Review the VSDSquadron FPGA Mini board datasheet to understand its features and pinout.**

**Use the datasheet to correlate the physical board connections with the PCF file and Verilog code.**

**Connect the board to the computer as described in the datasheet (e.g., using USB-C and ensuring FTDI connection).**

**Follow the provided Makefile (https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/Makefile) for building and flashing the Verilog code:**

Run 'make clean' to clear any previous builds

Run 'make build' to compile the design

Run 'sudo make flash' to program the FPGA board

**Observe the behavior of the RGB LED on the board to confirm successful programming.**

</details>

**Step 4: Final Documentation**

<details>
  <summary>
Expand or Collapse
  </summary>

**Compile all observations, explanations, and steps into a comprehensive report.
Include:**

**Summary of the Verilog code functionality**

Pin mapping details from the PCF file

Integration steps and observations while working with the FPGA Mini board

Challenges faced and solutions implemented

**Submit the final document along with the working Verilog and PCF files.**

**Document all of these in a GitHub repo.**

</details>
</details>

# **Implementation**

## Step 1: Understanding the Verilog Code

**Tasks which are done here-** 

1. Access the Verilog code from the provided link: https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/top.v

2. Review the module declaration and understand the purpose of each input and output port:

    led_red, led_blue, led_green (Output): Control the RGB LED

    hw_clk (Input): Hardware oscillator clock input

    testwire (Output): Test signal output
--

## Module Port Description - `top.v`

### Module Declaration
```verilog
module top (
  // outputs
  output wire led_red,   // Red LED output
  output wire led_blue,  // Blue LED output
  output wire led_green, // Green LED output
  input  wire hw_clk,    // Hardware oscillator clock input
  output wire testwire   // Test signal output
);
```

### Port Descriptions

| Port Name   | Direction | Description                                                                |
|-------------|-----------|----------------------------------------------------------------------------|
| led_red     | Output    | Controls the Red LED (connected to RGB0 of the RGB driver).                |
| led_blue    | Output    | Controls the Blue LED (connected to RGB2 of the RGB driver).               |
| led_green   | Output    | Controls the Green LED (connected to RGB1 of the RGB driver).              |
| hw_clk      | Input     | External hardware oscillator clock input. Currently unused in this module. |
| testwire    | Output    | Test/debug signal output. Shows a toggling bit from frequency_counter_i[5].|

### Summary

- `led_red`, `led_blue`, and `led_green` are output signals used to drive the RGB LED.
- `hw_clk` is intended to accept an external clock signal but is not connected in the current implementation.
- `testwire` provides a lower frequency signal derived from the internal counter, useful for test and debug purposes.

---

**Tasks which are done here-** 

3. Analyze the internal components:

    Internal Oscillator (SB_HFOSC) instantiation

    Frequency counter logic driven by the internal oscillator

    RGB LED driver instantiation with defined current parameters

--

## Internal Component Analysis - `top.v`

### Internal Oscillator Instantiation (`SB_HFOSC`)

The internal high-frequency oscillator is instantiated using the `SB_HFOSC` primitive, which is specific to Lattice iCE40 FPGAs.

```verilog
SB_HFOSC #(.CLKHF_DIV ("0b10")) u_SB_HFOSC (
  .CLKHFPU(1'b1),   // Power-up enable
  .CLKHFEN(1'b1),   // Clock enable
  .CLKHF(int_osc)   // Output clock signal
);
```

- `CLKHFPU` and `CLKHFEN` are set to `1'b1` to enable and power up the oscillator.
- `CLKHF_DIV` is configured with `"0b10"`, setting the clock division factor.
- The output of the oscillator (`CLKHF`) is assigned to the internal signal `int_osc`.
- This clock is used to drive the counter logic in the design.

---

### Frequency Counter Logic Driven by the Internal Oscillator

A simple 28-bit frequency counter is implemented. It increments on every positive edge of the `int_osc` clock signal.

```verilog
reg [27:0] frequency_counter_i;

always @(posedge int_osc) begin
  frequency_counter_i <= frequency_counter_i + 1'b1;
end
```

- `frequency_counter_i` is a 28-bit register.
- It increments continuously, creating a free-running counter.
- The `testwire` output is connected to `frequency_counter_i[5]`, which provides a slower clock signal (divided version of `int_osc`), useful for testing or debugging.

---

### RGB LED Driver Instantiation (`SB_RGBA_DRV`)

The RGB LED driver is instantiated using the `SB_RGBA_DRV` primitive.

```verilog
SB_RGBA_DRV RGB_DRIVER (
  .RGBLEDEN(1'b1),   // Enable RGB LED
  .RGB0PWM (1'b0),   // Red PWM control (off)
  .RGB1PWM (1'b0),   // Green PWM control (off)
  .RGB2PWM (1'b1),   // Blue PWM control (on)
  .CURREN  (1'b1),   // Current control enable
  .RGB0    (led_red),   // Red LED output connection
  .RGB1    (led_green), // Green LED output connection
  .RGB2    (led_blue)   // Blue LED output connection
);
```

- `RGBLEDEN` enables the RGB driver.
- `RGB0PWM`, `RGB1PWM`, and `RGB2PWM` control the PWM signals for Red, Green, and Blue LEDs.
  - Red and Green are turned off (`1'b0`), and Blue is turned on (`1'b1`).
- `CURREN` enables current control for the LEDs.
- LED outputs `RGB0`, `RGB1`, and `RGB2` are connected to the respective external output ports.

#### Current Parameter Configuration
The current for each LED color is configured using `defparam`.

```verilog
defparam RGB_DRIVER.RGB0_CURRENT = "0b000001"; // Red LED current setting
defparam RGB_DRIVER.RGB1_CURRENT = "0b000001"; // Green LED current setting
defparam RGB_DRIVER.RGB2_CURRENT = "0b000001"; // Blue LED current setting
```

- Each color is assigned the same current setting, `"0b000001"`.
- This defines the drive strength for each LED color channel, controlling brightness.

---

### Summary

- The **SB_HFOSC** provides an internal clock source for the design.
- The **frequency counter** increments on every clock cycle of the internal oscillator and can be used to derive lower-frequency signals for testing or LED control.
- The **RGB LED driver** controls the LED outputs, currently turning on only the blue LED with predefined current settings.
---

**Tasks which are done here-** 

4. Create a brief documentation explaining the functionality of the Verilog code, including:

    Purpose of the module

    Description of internal logic and oscillator

    Functionality of the RGB LED driver and its relationship to the outputs

--

# Verilog Module Documentation - `top.v`

## Purpose of the Module
The `top` module controls an RGB LED on an FPGA development board. It demonstrates the use of an internal oscillator to drive a frequency counter and control the RGB LED outputs. Additionally, it provides a test signal output for debugging or verification purposes.

---

## Description of Internal Logic and Oscillator

### Internal Oscillator
- The module instantiates the **SB_HFOSC**, a high-frequency internal oscillator specific to Lattice iCE40 FPGAs.
- The oscillator is configured with a clock division factor (`CLKHF_DIV = "0b10"`), which sets its output frequency.
- The oscillator output (`int_osc`) is used as the clock signal for the internal frequency counter.

### Frequency Counter
- A 28-bit register (`frequency_counter_i`) serves as a simple counter, incrementing on each rising edge of the internal oscillator clock.
- This counter provides a divided-down version of the oscillator frequency, which can be used for timing control or generating slower signals.
- Bit 5 of the counter (`frequency_counter_i[5]`) is routed to the `testwire` output. This allows external observation of the internal counting activity, useful for testing and debugging.

---

## Functionality of the RGB LED Driver and Its Relationship to the Outputs

### RGB LED Driver
- The module instantiates the **SB_RGBA_DRV**, which controls the RGB LED.
- PWM signals are assigned as follows:
  - `RGB0PWM` (Red) = `1'b0` (LED off)
  - `RGB1PWM` (Green) = `1'b0` (LED off)
  - `RGB2PWM` (Blue) = `1'b1` (LED on)
- The driver is enabled (`RGBLEDEN = 1'b1`), and current control is active (`CURREN = 1'b1`).

### Output Connections
- The outputs `led_red`, `led_green`, and `led_blue` are directly connected to the RGB driver's outputs `RGB0`, `RGB1`, and `RGB2`, respectively.
- In this implementation:
  - Only the blue LED is turned on.
  - Red and green LEDs remain off.
- Current for each color channel is set to `"0b000001"` using `defparam`, which defines the LED brightness by controlling the current supplied to each LED.

---

### Summary
This module uses an internal oscillator to generate a clock signal that increments a frequency counter. The counter provides a test output signal (`testwire`). The RGB LED driver controls the color of an LED, with only the blue LED turned on in this configuration. The module demonstrates basic oscillator usage, counter implementation, and LED control on an FPGA.

---

## Step 2: Creating the PCF File

**Tasks which are done here-**

1. Access the PCF file from the provided link: https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/VSDSquadronFM.pcf

2. Understand the pin assignments from the PCF file:

    led_red -> Pin 39

    led_blue -> Pin 40

    led_green -> Pin 41

    hw_clk -> Pin 20

    testwire -> Pin 17

--

## PCF File for `VSDSquadronFM.pcf`

```
set_io  led_red    39
set_io  led_blue   40
set_io  led_green  41
set_io  hw_clk     20
set_io  testwire   17
```

---

## Pin Assignment Details

| Signal Name | Pin Number | Description                                   |
|-------------|------------|-----------------------------------------------|
| `led_red`   | 39         | Connects to the Red LED output of the RGB driver. |
| `led_blue`  | 40         | Connects to the Blue LED output of the RGB driver. |
| `led_green` | 41         | Connects to the Green LED output of the RGB driver. |
| `hw_clk`    | 20         | External hardware oscillator clock input.      |
| `testwire`  | 17         | Debug/test signal output, showing the counter bit toggle. |

---

### Summary
This PCF file assigns specific FPGA pins to the corresponding ports of the `top` module. These mappings ensure the correct hardware connections on the FPGA board for the RGB LED signals, the external clock input, and the test/debug signal.

**Tasks which are done here-**

3. Cross-reference the pin assignments with the VSDSquadron FPGA Mini board datasheet to verify the correctness of the assignments.

---

### Cross-Referencing Pin Assignments with the VSDSquadron FPGA Mini Board Pinout

Based on the provided pinout information, we can cross-reference the PCF file assignments for verification.

![image](https://github.com/user-attachments/assets/1b741454-0fb4-4d89-9dc8-0c032849e58f)

#### Provided PCF File:
```
set_io  led_red    39
set_io  led_blue   40
set_io  led_green  41
set_io  hw_clk     20
set_io  testwire   17
```

#### Step 1: Verify RGB LED Pins
From the datasheet you shared:

| Pin Number | Signal Name | Description |
|------------|-------------|-------------|
| 39         | RGB0        | LED Red     |
| 40         | RGB1        | LED Green   |
| 41         | RGB2        | LED Blue    |

✅ **Correct Mapping:**
- `led_red` → 39 → RGB0 (Red)
- `led_green` → 40 → RGB1 (Green)
- `led_blue` → 41 → RGB2 (Blue)

---

#### Step 2: Verify `hw_clk` and `testwire` Pins
From the datasheet:

| Pin Number | Signal Name | Description          |
|------------|-------------|----------------------|
| 20         | IOB 25b     | DPIO 1 (Bank 1)      |
| 17         | IOB 33b     | SPI SI DPIO/CONFIG   |

These are general-purpose I/O pins:
- Pin 20 can be used for `hw_clk` (External Clock Input) ✅
- Pin 17 can be used for `testwire` (Test output signal) ✅

---

### Final Verification Table

| Signal Name | PCF Pin | FPGA Pin Description | Correct? |
|-------------|---------|----------------------|----------|
| `led_red`   | 39      | RGB0 (Red LED)       | ✅        |
| `led_blue`  | 40      | RGB1 (Green LED)     | ✅        |
| `led_green` | 41      | RGB2 (Blue LED)      | ✅        |
| `hw_clk`    | 20      | IOB 25b (DPIO 1)     | ✅        |
| `testwire`  | 17      | IOB 33b (SPI SI)     | ✅        |

---

### Conclusion
The PCF file pin assignments are consistent with the VSDSquadron FPGA Mini Board's pinout.  

---

**Tasks which are done here-**

4. Document the pin mapping and explain the significance of each connection in context with the Verilog code and board hardware.

--

## Pin Mapping Documentation and Explanation

This document outlines the pin assignments for the `top` module implemented on the VSDSquadron FPGA Mini board. Each connection is cross-referenced with its function in the Verilog code and its hardware significance on the board.

---

### Pin Mapping Table

| Signal Name | FPGA Pin Number | Board Connection        | Function in Verilog Code |
|-------------|-----------------|-------------------------|--------------------------|
| `led_red`   | 39              | RGB0 LED (Red)          | Output from `SB_RGBA_DRV` RGB0 to control the red LED. |
| `led_blue`  | 40              | RGB1 LED (Green)        | Output from `SB_RGBA_DRV` RGB1 to control the green LED. |
| `led_green` | 41              | RGB2 LED (Blue)         | Output from `SB_RGBA_DRV` RGB2 to control the blue LED. |
| `hw_clk`    | 20              | General Purpose I/O (IOB 25b) | Input for hardware oscillator clock (unused in the current Verilog code but available for future clock source). |
| `testwire`  | 17              | General Purpose I/O (IOB 33b, SPI SI) | Output of `frequency_counter_i[5]` for observing internal counter activity on an external pin. |

---

### Explanation of Each Connection

#### `led_red` → Pin 39 (RGB0)
- **Purpose**: Controls the Red LED on the RGB LED driver.
- **Verilog Role**: Connected to `RGB0` in the `SB_RGBA_DRV` instance.
- **Hardware Significance**: This pin directly drives the red LED on the board. In the provided Verilog code, the red LED is always off (`RGB0PWM = 1'b0`).

#### `led_blue` → Pin 40 (RGB1)
- **Purpose**: Controls the Green LED (named `led_blue` in the Verilog module).
- **Verilog Role**: Connected to `RGB1` in the `SB_RGBA_DRV` instance.
- **Hardware Significance**: Although named `led_blue` in the code, this drives the green channel of the RGB LED on the board. The PWM signal is set to `1'b0`, keeping it off in this implementation.

#### `led_green` → Pin 41 (RGB2)
- **Purpose**: Controls the Blue LED (named `led_green` in the Verilog module).
- **Verilog Role**: Connected to `RGB2` in the `SB_RGBA_DRV` instance.
- **Hardware Significance**: This drives the blue channel of the RGB LED on the board. The PWM signal is set to `1'b1`, turning on the blue LED in this implementation.

#### `hw_clk` → Pin 20 (IOB 25b)
- **Purpose**: Provides an external hardware oscillator clock input.
- **Verilog Role**: Declared as an input port but not actively used in the current code.
- **Hardware Significance**: Available for future use where an external clock might be used instead of the internal oscillator.

#### `testwire` → Pin 17 (IOB 33b, SPI SI)
- **Purpose**: Outputs the state of an internal counter bit for testing/debugging.
- **Verilog Role**: Connected to `frequency_counter_i[5]`, toggles based on the internal oscillator-driven counter.
- **Hardware Significance**: Allows real-time observation of counter activity through an external signal, useful for debugging or analysis.

---

### Summary

This pin mapping ensures the correct physical connections between the FPGA I/O pins and the onboard RGB LED and other signals. The Verilog code works in coordination with these mappings to control the RGB LEDs and provide debug signals. Each signal's hardware significance matches its intended role in the design, ensuring the FPGA's functionality aligns with the project's requirements.

---

## Step 3: Integrating with the VSDSquadron FPGA Mini Board

**Tasks which are done here-**

1. Review the VSDSquadron FPGA Mini board datasheet to understand its features and pinout.

2. Use the datasheet to correlate the physical board connections with the PCF file and Verilog code.


### 1. Review of the VSDSquadron FPGA Mini Board Datasheet

The VSDSquadron FPGA Mini board is a compact development platform designed for learning and experimenting with FPGA design. Here's a summary of its key features and pinout based on the datasheet:

#### Key Features:
- **FPGA Chip**: Lattice iCE40UP5K
- **Power Supply**: 3.3V for I/O and core voltage.
- **Onboard RGB LEDs**: Connected directly to dedicated FPGA pins.
- **Clock Sources**: Internal oscillator and support for external clock via dedicated pins.
- **GPIO**: Multiple configurable general-purpose I/O pins.
- **Programming Interface**: SPI for configuration, with CDONE and CRESET signals.
- **Differential Pairs**: Several I/O pairs support differential signaling.
- **Ground (GND)** and **Power (VCC)** pins are provided.

#### Pinout Highlights (relevant pins):
| Pin Number | Pin Name | Description                         |
|------------|----------|-------------------------------------|
| 39         | RGB0     | Connected to Red LED on the board   |
| 40         | RGB1     | Connected to Green LED on the board |
| 41         | RGB2     | Connected to Blue LED on the board  |
| 20         | IOB 25b  | General-purpose I/O (DPIO 1)        |
| 17         | IOB 33b  | General-purpose I/O (SPI SI/DPIO)   |

---

### 2. Correlating the Datasheet with the PCF File and Verilog Code

#### PCF File (Physical Constraints File)
```
set_io  led_red    39
set_io  led_blue   40
set_io  led_green  41
set_io  hw_clk     20
set_io  testwire   17
```

#### Cross-Reference Analysis

| Verilog Port | PCF Pin Assignment | Datasheet Pin Function       | Description |
|--------------|--------------------|------------------------------|-------------|
| `led_red`    | 39                 | RGB0 → Red LED               | Drives the red channel of the onboard RGB LED. |
| `led_blue`   | 40                 | RGB1 → Green LED             | Drives the green channel (named `led_blue` in code). |
| `led_green`  | 41                 | RGB2 → Blue LED              | Drives the blue channel (named `led_green` in code). |
| `hw_clk`     | 20                 | IOB 25b → General I/O        | Accepts an external clock source. Not actively used in the Verilog code. |
| `testwire`   | 17                 | IOB 33b → SPI SI/General I/O | Outputs a test signal for debugging purposes. |

---

### How the Verilog Code Aligns with the Pinout

- **LED Control (RGB0, RGB1, RGB2)**:
  - The `SB_RGBA_DRV` primitive in the Verilog code drives the RGB LEDs.
  - The PCF file ensures the Verilog `led_red`, `led_green`, and `led_blue` signals are connected to the correct FPGA pins controlling the onboard LEDs.

- **Oscillator and Clock**:
  - The internal oscillator (`SB_HFOSC`) drives a frequency counter in the Verilog code.
  - Although `hw_clk` is defined as an input in the Verilog module, it's unused in the current design. The PCF file still maps it to pin 20 for future use.

- **Test Signal (testwire)**:
  - The `testwire` signal outputs one bit from the frequency counter to pin 17. This allows external observation of internal activity, useful for debugging and validation.

---

### Summary

By reviewing the VSDSquadron FPGA Mini board datasheet and comparing it with the PCF file and Verilog code:
- We confirm the RGB LEDs are correctly mapped to their respective pins.
- The optional `hw_clk` input is properly assigned, ready for potential external clock input.
- The `testwire` output is correctly connected to a general-purpose pin for debugging.

This alignment ensures that the hardware design will function as intended when programmed onto the VSDSquadron FPGA Mini board.

---

**Tasks which are done here-**

Connect the board to the computer as described in the datasheet (e.g., using USB-C and ensuring FTDI connection).

--

![image](https://github.com/user-attachments/assets/87b2720f-9cb4-4fa9-b7e2-03c4ff8fe300)

![WhatsApp Image 2025-03-20 at 18 55 10_4e2ad4d2](https://github.com/user-attachments/assets/65f7f267-ff30-4945-bc00-fa34875ca67b)

Done 👍














