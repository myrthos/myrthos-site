---
author: Joost Mans
title: CLR Clock Reference
date: 2023-08-23
lastmod: 2023-08-23
Description: Overview of the CLR functionality.
layout: docs
tocHideParenthesis: true
tocBreakUnderscore: true
--- 
<!-- cSpell:ignore Fosc Joost lastmod MPLCLK HFINTOSC LFINTOSC MFINTOSC MPLCLR mplclr MPLPER mplpic Secundary SOSC -->

The following gives an overview of the available macros and other defines for setting the reference clock of the supported PIC controllers. This module makes it possible to output the clock reference at the clock reference output, which can be provided to other modules and outputted on an output pin.

## Macro's for the Clock Reference

### MPLCLR_DISABLE

Disables the clock reference module. Disabling the module will reduce the power consumption of the controller.

---------------------------------------

### MPLCLR_ENABLE

Enables the clock reference module. Note that this is only needed after the [MPLCLR_DISABLE](#mplclr_disable) macro is used, as the module is enabled by default.

---------------------------------------

### MPLCLR_INIT(divider, duty) {#mplclr_init}

Initializes the clock reference by using a divider and a duty cycle. Depending on the controller being used, the input clock can be changed, but after initialization it is set to the Fosc clock frequency. The clock reference is also stopped after initialization (if it was running before).

*divider*  
The divider that is used to divide the input clock with. The following values are supported:

{{< table "table-striped table-bordered" >}}
Divider      |Description
:-----------:|:---:
MPLCLR_DIV128|Divide by 128
MPLCLR_DIV64 |Divide by 64
MPLCLR_DIV32 |Divide by 32
MPLCLR_DIV16 |Divide by 16
MPLCLR_DIV8  |Divide by 8
MPLCLR_DIV4  |Divide by 4
MPLCLR_DIV2  |Divide by 2
MPLCLR_DIV1  |Divide by 1
{{< /table >}}

*duty*  
The duty cycle of the clock reference. The following values are supported:

{{< table "table-striped table-bordered" >}}
Duty       |Description
:---------:|:---:
MPLCLR_DC75|Duty cycle is 75%
MPLCLR_DC50|Duty cycle is 50%
MPLCLR_DC25|Duty cycle is 25%
MPLCLR_DC0 |Duty cycle is 0%
{{< /table >}}

The duty cycle has no effect when the input clock is Fosc and the the divider is MPLCLR_DIV1.

EXAMPLE

See [MPLCLK_SOURCE](#mplclr_source)

---------------------------------------

### MPLCLR_SOURCE(src) {#mplclr_source}

{{< mstyle "underline" >}}This macro is only supported on the 16(L)F153xx series controllers{{< /mstyle >}}.

Sets the desired input clock signal that is to be used. Should be called after an [MPLCLR_INIT](#mplclr_init).

*src*  
One of the supported input clocks that is used to generated the clock reference. The following values are supported:

{{< table "table-striped table-bordered" >}}
Clock source          |Description
:--------------------:|:---:
MPLCLR_SRC_FOSC       |The system oscillator frequency
MPLCLR_SRC_HFINTOSC   |The High Frequency oscillator
MPLCLR_SRC_LFINTOSC   |The Low Frequency oscillator (31KHz)
MPLCLR_SRC_MFINTOSC500|The Medium Frequency oscillator (500 KHz)
MPLCLR_SRC_MFINTOSC   |The Medium Frequency oscillator (31.25KHz)
MPLCLR_SRC_SOSC       |The Secundary oscillator
MPLCLR_SRC_NCO1       |The Output of NCO1
MPLCLR_SRC_CLC1       |The Output of CLC1
MPLCLR_SRC_CLC2       |The Output of CLC2
MPLCLR_SRC_CLC3       |The Output of CLC3
MPLCLR_SRC_CLC4       |The Output of CLC4
{{< /table >}}

EXAMPLE  

```c
// Initialize the clock and set a frequency of 32 MHz
MPLCLK_INIT;
MPLCLK_SET(MPLCLK_F32, 10000);

// Initialize the clock reference to 8 MHz (32/4)and a duty cycle of 50%
MPLCLR_INIT(MPLCLR_DIV4, MPLCLR_DC50);

// By changing the input clock to 500 KHz, the clock reference will be 500/4 = 125KHz
MPLCLR_SOURCE(MPLCLR_SRC_MFINTOSC500);

// Output to a pin
MPLCLR_POUT(MPLPER_OUT_RA4);

// Start the clock reference
MPLCLR_START;
```

---------------------------------------

### MPLCLR_POUT(pin) {#mplclr_pout}

Defines the output pin that will be used to output the clock reference on.

*pin*  
The output pin to use as an output for the clock reference signal. This is one of the supported output pins of the used controller and is one of the MPLPER_OUT_Rxx defines, where xx is the name of the port pin being used. See also {{< link "per-peripheral#peripheral-io" >}}Peripheral I/O{{< /link >}}.

Note that the pin has to be defined as an output, prior to using this macro. Also in order to be able to use this the PPS1WAY pragma needs to be set to off, before including the xc.h or mplpic.h file: `#pragma config PPS1WAY = OFF;`

EXAMPLE

See [MPLCLR_SOURCE](#mplclr_source)

---------------------------------------

### MPLCLR_START

Starts the generation of the reference clock and if set, outputs the frequency on the output pin.

EXAMPLE

See [MPLCLR_SOURCE](#mplclr_source)

---------------------------------------

### MPLCLR_STOP

Stops the generation of the reference clock.
