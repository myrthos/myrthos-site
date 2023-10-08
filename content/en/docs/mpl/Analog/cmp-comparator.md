---
author: Joost Mans
title: CMP Comparator
date: 2023-10-08
lastmod: 2023-10-08
Description: Overview of the CMP functionality.
layout: docs
tocHideParenthesis: true
tocBreakUnderscore: true
--- 
<!-- cSpell:ignore Joost lastmod -->
The following gives an overview of the available macros and other defines for setting the comparator modules. The comparator compares two analog inputs and outputs the result of that comparison as a digital value.

Not all controllers support the same amount of comparator modules. The following provides an overview of what is supported depending on the controller used.

- The PIC16(L)F15313 controller supports only 1 comparator. All other controllers in the PIC16(L)F153xx series support 2 comparators
- The PIC16(L)F18313 controller supports only 1 comparator. All other controllers in the PIC16(L)F183xx series support 2 comparators

There are different macros for each of the comparators. By means of the number 1 and 2 in the macro name, the comparator is specified, which looks like: MPLPWM_X_SET, where \_X\_ refers to one of the supported comparator modules and is either a 1 or a 2.

[TOC]

## Macros for the comparator

---------------------------------------

### MPLCMP_1_DISABLE {#mplcmp_disable}

### MPLCMP_2_DISABLE

Disable the comparator module. Disabling the module will reduce the power consumption of the controller.

EXAMPLE  
See [MPLCMP_X_ENABLE](#mplcmp_enable)

---------------------------------------

### MPLCMP_1_ENABLE {#mplcmp_enable}

### MPLCMP_2_ENABLE

Enable the comparator module. Note that this is only needed after the [MPLCMP_X_DISABLE](#mplcmp_disable) macro is used, as the module is enabled by default.

EXAMPLE  

```{.c}
// Disable the comparator 1 module
MPLCMP_1_DISABLE;

// ...

// Enable the Comparator 1 module
MPLCMP_1_ENABLE;
```

---------------------------------------

### MPLCMP_1_INIT(polarity, hysteresis, timer) {#mplcmp_init}

### MPLCMP_2_INIT(polarity, hysteresis, timer)

Initializes the comparator. It will also stop the comparator and disable the generation of interrupts by the comparator.

*polarity*  
Determines if the polarity of the output is reversed or not. There are two options: MPLCMP_POL_NORMAL for normal polarity and MPLCMP_POL_INVERT for an inverse polarity. This leads to the following options for the output signal of the comparator:

{{< table "table-striped table-bordered" >}}
Source|Polarity|Output
---|---|---
Vin+ < Vin-|MPLCMP_POL_NORMAL|Low
Vin+ > Vin-|MPLCMP_POL_NORMAL|High
Vin+ < Vin-|MPLCMP_POL_INVERT|High
Vin+ > Vin-|MPLCMP_POL_INVERT|Low
{{< /table >}}

*hysteresis*  
To avoid the output to start oscillating when the input values are not stable, but vary slightly, a hysteresis can be enabled. If this is enabled a hysteresis of typical 25 mV is used on the inputs.

{{< table "table-striped table-bordered" >}}
Value | Description
---|---
MPLCMP_HYST_ON |Hysteresis is enabled
MPLCMP_HYST_OFF|hysteresis is disabled
{{< /table >}}

*timer*  
The output of a comparator can be used as a source for gate control of Timer1. This feature is useful for timing the duration or interval of an analog event. It is recommended that the comparator output is synchronized to Timer1. This prevents Timer1 to increment while a change in the comparator is occurring.
The following two options can be used:

{{< table "table-striped table-bordered" >}}
Define|Description
---|---
MPLCMP_TMR_SYNC|The comparator putput is synchronized with Timer1 clock.
MPLCMP_TMR_ASYNC|The comparator putput is not synchronized with Timer1 clock.
{{< /table >}}

EXAMPLE

```{.c}
// Initialize the CMP
MPLCMP_1_INIT(MPLCMP_POL_NORMAL, MPLCMP_HYST_ON, MPLCMP_TMR_SYNC);

// Use as a positive input, the output of the DAC
// and as a negative input the Fixed Voltage Reference.
MPLCMP_1_SOURCE(MPLCMP_SRC_DAC1, MPLCMP_SRC_FVR);

// Set pin A4 as the poutput
TRISAbits.TRISA4 = 0;
// Set pin A4 as output of the CMP
MPLCMP_1_POUT(MPLPER_OUT_RA4);

// Start the Comparator
MPLCMP_1_START;

// Get the conparator value
uint8_t result = MPLCMP_1_GET;
```

---------------------------------------

### MPLCMP_1_SOURCE(pos, neg) {#mplcmp_src}

### MPLCMP_2_SOURCE(pos, neg)

Sets the sources that will be used as the inputs of the comparator.

*pos*  
The source of the posisitve input, which is one of the following values:

{{< table "table-striped table-bordered" >}}
Define|Description
------|----
MPLCMP_SRC_DAC1 | The output of Fixed Voltage Reference
MPLCMP_SRC_FVR | The output of DAC1
MPLCMP_SRC_IN0 | The analog input pin C1IN0+/C2IN0+
MPLCMP_SRC_IN1 | The analog input pin C1IN1+/C2IN1+
MPLCMP_SRC_VSS | The VSS voltage
{{< /table >}}

The source MPLCMP_SRC_IN1 is not available on all controllers. It is only available on the PIC16(L)F1535x, PIC16(L)F1537x, PIC16(L)F1538x.

Note that in order to use the I/O pins as an analog input, the pin needs to be set as input with the TRIS register and as alanalog with the ANSEL register.

*neg*  
The source of the negative input, which is one of the following values:

{{< table "table-striped table-bordered" >}}
Define|Description
------|----
MPLCMP_SRC_FVR | The output of DAC1
MPLCMP_SRC_IN0 | The analog input pin C1IN0-/C2IN0-
MPLCMP_SRC_IN1 | The analog input pin C1IN1-/C2IN1-
MPLCMP_SRC_IN2 | The analog input pin C1IN2-/C2IN2-
MPLCMP_SRC_IN3 | The analog input pin C1IN3-/C2IN3-
MPLCMP_SRC_VSS | The VSS voltage
{{< /table >}}

Note that in order to use the I/O pins as an analog input, the pin needs to be set as input with the TRIS register and as alanalog with the ANSEL register.

EXAMPLE

See [MPLCMP_X_INIT](#mplcmp_init)

---------------------------------------

### MPLCMP_1_GET {#mplcmp_get}

### MPLCMP_2_GET

Returns the current state of the comparator output, which is either a 1 or a 0. The value is returned as an *uint8_t*.

---------------------------------------

### MPLCMP_1_POUT(pin) {#mplcmp_pin}

### MPLCMP_2_POUT(pin)

Sets the putput pin that will be used to reflect the output of the comparator.

*pout*  
The output pin to be used as the output of the comparator. This is one of the supported output pins of the used controller and is one of the MPLPER_OUT_Rxx defines, where xx is the name of the port pin being used.
Note that the pin has to be defined as an output, prior to using this macro. Also in order to be able to use this the PPS1WAY pragma needs to be set to off, before including the `xc.h` or `mplpic.h` file: `#pragma config PPS1WAY = OFF;`

EXAMPLE

```{.c}
// Set the pin to use as an output
TRISBbits.TRISB0 = 0;

// Set the input pin
MPLCMP_1_POUT(MPLPER_OUT_RB0);
```

---------------------------------------

### MPLCMP_1_INT(on) {#mplcmp_int}

### MPLCMP_2_INT(on)

Enable or disable the generation on the rising and/or falling edge of the output of the comparator. Which edge will be used, depends on the setting of [MPLCMP_X_INTMODE](#mplcmp_intmode).

*on*  
Enables (on=1) or disables (on=0) the generation of interrupts.  
Note that the interrupt flag is always set upon an interrupt event of the comparator, even if interrupts are disabled.

EXAMPLE

```{.c}
// Initialize the comparator
MPLCMP_1_INIT(MPLCMP_POL_NORMAL, MPLCMP_HYST_ON, MPLCMP_TMR_SYNC);

// Use as a positive input, the output of the DAC
// and as a negative input the Fixed Voltage Reference.
MPLCMP_1_SOURCE(MPLCMP_SRC_DAC1, MPLCMP_SRC_FVR);

// Set pin A4 as the poutput
TRISAbits.TRISA4 = 0;
// Set pin A4 as output of the CMP
MPLCMP_1_POUT(MPLPER_OUT_RA4);

// Generate interrupt on the rising edge
MPLCMP_1_INTMODE(MPLCMP_INT_POS);

// Enable interrupts
MPLCMP_1_INT(1);

// Start the Comparator
MPLCMP_1_START;

// Continue with the program...
```

There needs to be an interrupt service routine defined to handle the interrupt via B0.

```{.c}
void __interrupt() cmp_isr()
{
    // Check Comparator flag
    if (MPLCMP_1_INTFLAG) 
    {
        // Get the value
        uint8_t result = MPLCMP_1_GET;
        
        // Do something with it, but keep it short
        // ..
        
        // Reset interrupt flag
        MPLCMP_1_INTCLEAR;
    }
    // Check other potential interrupt sources
    // .
    // .    
}
```

---------------------------------------

### MPLCMP_1_INTMODE(mode) {#mplcmp_intmode}

### MPLCMP_2_INTMODE(mode)

Sets the mode in which an interrupt will be generated, which is either on the rising edge of the oytput, the falling edge, or both.

*mode*  
Sets the mode when an interrupt will be generated, which has the following values:

{{< table "table-striped table-bordered" >}}
Define|Description
------|----
MPLCMP_INT_OFF | No interrupt is generated
MPLCMP_INT_POS | An interrupt is generated when the output signal rises from 0 to 1
MPLCMP_INT_NEG | An interrupt is generated when the output signal falls from 1 to 0
MPLCMP_INT_BOTH | An interrupt is generated when the output signal changes
{{< /table >}}

EXAMPLE

See [MPLCMP_X_INT](#mplcmp_int)

---------------------------------------

### MPLCMP_1_INTFLAG {#mplcmp_intflag}

### MPLCMP_2_INTFLAG

Returns the state of the interrupt. It returns the value 0 if there is no current interrupt and a value unequal to 0 when there is an interrupt. The macro only returns a value unequal to 0 if both the interrupt flag is set and the generation of interrupts is enabled.  
Note that the interrupt flag needs to be manually cleared in an interrupt service routine by using the [MPLCMP_X_INTCLEAR](#mplcmp_intclear) macro.

EXAMPLE

See [MPLCMP_X_INT](#mplcmp_int)

---------------------------------------

### MPLCMP_1_INTCLEAR {#mplcmp_intclear}

### MPLCMP_2_INTCLEAR

Clears the interrupt flag. This macro is normally used in an interrupt service routine.

EXAMPLE

See [MPLCMP_X_INT](#mplcmp_int)

---------------------------------------

### MPLCMP_1_START {#mplcmp_start}

### MPLCMP_2_START

Starts the comparator, after which it will immediately start comparing the input signals using the current settings. If the comparator was already started, nothing will change.

EXAMPLE

See [MPLCMP_X_INT](#mplcmp_int)

---------------------------------------

### MPLCMP_1_STOP {#mplcmp_stop}

### MPLCMP_2_STOP

Stops the comparator. If the comparator was already stopped, nothing will happen. No other values and settings are influenced by stopping the comparator.

---------------------------------------

### MPLCMP_1_CLEAR {#mplcmp_clear}

### MPLCMP_2_CLEAR

Stops the comparator, clears the interrupt flag and disables interrupts.
