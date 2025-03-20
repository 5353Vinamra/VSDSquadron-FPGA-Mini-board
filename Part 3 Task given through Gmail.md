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

**Task which are done here-** 

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

**Task which are done here-** 

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

**Task which are done here-** 

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

## Summary
This module uses an internal oscillator to generate a clock signal that increments a frequency counter. The counter provides a test output signal (`testwire`). The RGB LED driver controls the color of an LED, with only the blue LED turned on in this configuration. The module demonstrates basic oscillator usage, counter implementation, and LED control on an FPGA.











