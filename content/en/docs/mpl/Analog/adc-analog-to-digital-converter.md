---
author: Joost Mans
title: ADC Analog to Digital Converter
date: 2023-09-06
lastmod: 2023-09-06
Description: Overview of the ADC functionality.
layout: docs
tocHideParenthesis: true
tocBreakUnderscore: true
--- 
<!-- cSpell:ignore Joost lastmod TRIS ANSEL MPLADC nref fosc mimage vref mplclk mtag abits trisa ansa mplper delayus mplmodadc mplpic intflag intclear -->

This section gives an overview of the available macros and other defines for setting the analog to digital converter module. Most of the pins can be used as an analog input, but need to be configured properly by setting the I/O pin as an analog input, which is accomplished by setting the TRIS and ANSEL settings correctly for that I/O pin.  
The ADC is a 10 bit module, where the analog input value is converted to a 10 bit digital value.

## Macros for ADC

### MPLADC_DISABLE

Disable the ADC module. Disabling the module will reduce the power consumption of the controller.

EXAMPLE  
See [MPLADC_ENABLE](#mpladc_enable)

---------------------------------------

### MPLADC_ENABLE

Enable the ADC module. Note that this is only needed after the [MPLADC_DISABLE](#mpladc_disable) macro is used, as the module is enabled by default.

EXAMPLE  

```c
// Disable the ADC module
MPLADC_DISABLE;

// ...

// Enable the ADC module
MPLADC_ENABLE;
```

---------------------------------------

### MPLADC_INIT(align, clock, nref, pref) {#mpladc_init}

Initializes the ADC module. It will also stop the ADC, disable auto trigger and set the source of the ADC to ground. The ADC will convert the input voltage, based on the range defined by the negative reference voltage *nref* and the positive reference voltage *pref*.

*align*  
The alignment of the 10 bit converted value in the 16 bit register result register. The following shows the two options:

{{< table "table-striped table-bordered" >}}
Define|Description
---|---
MPLADC_ALIGN_LEFT |The 10 bit value is the high order 10 bits of the 16 bit result
MPLADC_ALIGN_RIGHT|The 10 bit value is the low order 10 bits of the 16 bit result
{{< /table >}}

This is also shown in the following figure, where left alignment is shown at the top and right alignment at the bottom:  

{{< mimage src="img/alignment.jpg" title="WAlignment" outer="image-center" >}}
{{< mtag "br" />}}
This alignment should be taken into account when retrieving the converted analog value by means of the [MPLADC_GET](#mpladc_get) macro.

*clock*  
The clock is either based on Fosc divided by a divisor or a dedicated RC oscillator.

{{< table "table-striped table-bordered" >}}
Clock | Description
---|---
MPLADC_CLK_FOSC2 |The current clock speed divided by 2
MPLADC_CLK_FOSC4 |The current clock speed divided by 4
MPLADC_CLK_FOSC8 |The current clock speed divided by 8
MPLADC_CLK_FOSC16|The current clock speed divided by 16
MPLADC_CLK_FOSC32|The current clock speed divided by 32
MPLADC_CLK_FOSC64|The current clock speed divided by 64
MPLADC_CLK_RC    |Use the dedicated RC oscillator
{{< /table >}}

The ADC can only convert the analog value correctly when the combination of Fosc/divisor results in a cycle time between 1 and 6 microseconds. When e.g. the clock frequency is 32 MHz, the only valid divisor values are 32 (1 us) and 64 (2 us). For an Fosc of 8 MHz, the valid divisors are 8 (1 us), 16 (2 us) and 32 (4 us).  
The macro [MPLADC_CALC](#mpladc_calc) will calculate the correct and fastest divisor, given the current oscillator frequency.

*nref*  
The negative voltage reference of the converter. The following values are possible:

{{< table "table-striped table-bordered" >}}
Define|Description
---|---
MPLADC_NREF_VSS|The VSS power signal is used as the reference
MPLADC_NREF_EXT|The external VREF- signal is used as the reference (only on 16F18xxx series)
{{< /table >}}

*pref*  
The positive voltage reference of the converter. The following values are possible:  

{{< table "table-striped table-bordered" >}}
Define|Description
---|---
MPLADC_PREF_VDD|The VDD power signal is used as the reference
MPLADC_PREF_EXT|The external VREF+ signal is used as the reference
MPLADC_PREF_FVR|The Fixed Voltage Reference output is used as the reference
{{< /table >}}

EXAMPLE

```c
// Set the current clock frequency to 16 MHz
MPLCLK_INIT;
MPLCLK_SET(MPLCLK_F16M);

// Get the fastest divisor
uint8_t divisor = MPLADC_CALC;

// Initialize the ADC
MPLADC_INIT(MPLADC_ALIGN_RIGHT, divisor, MPLADC_NREF_VSS, MPLADC_PREF_VDD);

// Set pin A4 as analog input
TRISAbits.TRISA4 = 1;
ANSELAbits.ANSA4 = 1;
// Set pin A4 as input of the ADC
MPLADC_SOURCE(MPLPER_IN_RA4);

// Wait
MPLCLK_DELAYUS(5);

// Start the conversion
MPLADC_START;
// Wait for the conversion to be complete
MPLADC_WAIT;

// Get the converted value
uint16_t result = MPLADC_GET;
```

---------------------------------------

### MPLADC_CALC

{{< mstyle "underline" >}}Usage of this macro, requires the file `mplmodadc.c` to be included as a source file{{< /mstyle >}}.

This macro will call the function *mpl_adc_calc*. It will calculate the fastest value to set as the clock value in the [MPLADC_INIT](#mpladc_init) call.  
In order to calculate this correctly the processor clock has to be set by means of the [MPLCLK_SET](../../peripheral/clk-clock-circuitry/#mplclk_set) macro or the [MPLCLK_SET_CYCLE](../../peripheral/clk-clock-circuitry/#mplclk_set_cycle) should have been called to override the cycle time.  

EXAMPLE

See [MPLADC_INIT](#mpladc_init)

---------------------------------------

### MPLADC_SOURCE(src) {#mpladc_src}

Sets the source that will be used as the input of the ADC.

*src*  
The source to use, which is one of the following values:

{{< table "table-striped table-bordered" >}}
Define|Description
------|----
MPLADC_SRC_DAC1 | The output of Fixed Voltage Reference
MPLADC_SRC_FVR | The output of DAC1
MPLADC_SRC_TEMP | The output of the temperature indicator
MPLADC_SRC_VSS | The VSS voltage
MPLPER_IN_Rxy | The input pin y on port x
{{< /table >}}

When switching between different sources, the ADC requires a settling time before it can accurately convert the analog value. The time needed for that depends on the temperature, but is about 4-5 us.

If the option MPLPER_IN_Rxy is selected a valid combination of x (port) and y (pin) needs to be provided, like MPLPER_IN_RA4, for pin 4 of PortA.  

Note that the used pins needs to be configured as an analog input pin, using the TRIS and ANSEL registers.

EXAMPLE

See [MPLADC_INIT](#mpladc_init)

---------------------------------------

### MPLADC_TRIGGER(trig) {#mpladc_trigger}

Instead of being triggered by software the ADC can also be also triggered by an event. After selection of the trigger source, a rising edge from that source will start an ADC measurement.

*trig*  
The trigger source to use. Which one can be used, depend son the used controller. The ADC is triggered on a rising edge of the selected trigger input.

{{< table "table-striped table-bordered" >}}
Source |Description|16F153xx|16F183xx
-------|-----------|:------:|:------:
MPLADC_TRG_NONE|Disable trigger                |x|x
MPLADC_TRG_PPS |External pin trigger           |x|
MPLADC_TRG_IOC |Trigger on interrupt           |x|
MPLADC_TRG_TMR0|Trigger on timer 0 overflow    |x|x
MPLADC_TRG_TMR1|Trigger on timer 1 overflow    |x|x
MPLADC_TRG_TMR2|Trigger on timer match with PR2|x|x
MPLADC_TRG_TMR3|Trigger on timer 3 overflow    | |x
MPLADC_TRG_TMR4|Trigger on timer match with PR4| |x
MPLADC_TRG_TMR5|Trigger on timer 5 overflow    | |x
MPLADC_TRG_TMR6|Trigger on timer match with PR6| |x
MPLADC_TRG_CMP1|Trigger on comparator 1 output |x|x
MPLADC_TRG_CMP2|Trigger on comparator 2 output |x|x
MPLADC_TRG_CCP1|Trigger on CCP1 output         |x|x
MPLADC_TRG_CCP2|Trigger on CCP2 output         |x|x
MPLADC_TRG_CCP3|Trigger on CCP3 output         | |x
MPLADC_TRG_CCP4|Trigger on CCP4 output         | |x
MPLADC_TRG_PWM1|Trigger on PWM1 output         |x|
MPLADC_TRG_PWM2|Trigger on PWM2 output         |x|
MPLADC_TRG_PWM3|Trigger on PWM3 output         |x|
MPLADC_TRG_PWM4|Trigger on PWM4 output         |x|
MPLADC_TRG_NCO1|Trigger on NCO1 output         |x|
MPLADC_TRG_CLC1|Trigger on CLC1 output         |x|x
MPLADC_TRG_CLC2|Trigger on CLC2 output         |x|x
MPLADC_TRG_CLC3|Trigger on CLC3 output         |x|x
MPLADC_TRG_CLC4|Trigger on CLC4 output         |x|x
{{< /table >}}

Note that if MPLADC_TRG_PPS is selected as the trigger source, the correct pin needs to be set with the [MPLADC_PIN](#mpladc_pin) macro.

---------------------------------------

### MPLADC_PIN(pin) {#mpladc_pin}

Set the input pin that wil be used in automatically triggering the ADC to start a conversion. This is only relevant when the [MPLADC_TRIGGER](#mpladc_trigger) macro is used and is set to MPLADC_TRG_PPS.

*pin*  
The input pin to be used as a trigger for the ADC. This is one of the supported input pins of the used controller and is one of the MPLPER_IN_Rxx defines, where xx is the name of the port pin being used.
Note that the pin has to be defined as an input, prior to using this macro. Also in order to be able to use this the PPS1WAY pragma needs to be set to off, before including the xc.h or mplpic.h file: `#pragma config PPS1WAY = OFF;`

EXAMPLE

```c
// Set an input pin as an automatic trigger
MPLADC_TRIGGER(MPLADC_TRG_PPS);

// Set the input pin
TRISBbits.TRISB0 = 1;
MPLADC_PIN(MPLPER_IN_RB0);
```

---------------------------------------

### MPLADC_GET

Retrieve the 10 bits value from the ADC, which is the digital result of the analog value conversion.  
The value returned is 10 bits in a 16 bit *uint16_t*. Depending on the alignment that was set in [MPLADC_INIT](#mpladc_init) it is either the lower 10 bits in that 16 bit value (alignment is *MPLADC_ALIGN_RIGHT*) or the upper 10 bits (alignment is *MPLADC_ALIGN_LEFT*).

EXAMPLE

See [MPLADC_INIT](#mpladc_init)

---------------------------------------

### MPLADC_INT(on) {#mpladc_int}

Enable or disable the generation of an interrupt when the conversion has finished.

*on*  
Enables (on=1) or disables (on=0) the generation of interrupts.  
Note that the interrupt flag is always set upon an interrupt event of the ADC, even if interrupts are disabled.

EXAMPLE

```c
// Set the current clock frequency to 16 MHz
MPLCLK_INIT;
MPLCLK_SET(MPLCLK_F16M);

// Get the fastest divisor
uint8_t divisor = MPLADC_CALC;

// Initialize the ADC
MPLADC_INIT(MPLADC_ALIGN_RIGHT, divisor, MPLADC_NREF_VSS, MPLADC_PREF_VDD);

// Set pin A4 as analog input
TRISAbits.TRISA4 = 1;
ANSELAbits.ANSA4 = 1;
// Set pin A4 as input of the ADC
MPLADC_SOURCE(MPLPER_IN_RA4);

// Wait 5 us
MPLCLK_DELAYUS(5);

// Set an input pin as an automatic trigger
MPLADC_TRIGGER(MPLADC_TRG_PPS);

// Enable interrupt
MPLADC_INT(1);

// Start the conversion
MPLADC_START;

// Continue with the program...
```

There needs to be an interrupt service routine defined to handle the interrupt via B0.

```c
void __interrupt() adc_isr()
{
    // Check ADC flag
    if (MPLADC_INTFLAG) 
    {
        // Get the value
        uint16_t result = MPLADC_GET;
        
        // Do something with it, but keep it short
        // ..
        
        // Reset interrupt
        MPLADC_INTCLEAR;
    }
    // Check other potential interrupt sources
    // .
    // .    
}
```

---------------------------------------

### MPLADC_INTFLAG

Returns the state of the interrupt. It returns the value 0 if there is no current interrupt and a value unequal to 0 when there is an interrupt. The macro only returns a value unequal to 0 if both the interrupt flag is set and the generation of interrupts is enabled.  
Note that the interrupt flag needs to be manually cleared in an interrupt service routine by using the [MPLADC_INTCLEAR](#mpladc_intclear) macro.

EXAMPLE

See [MPLADC_INT](#mpladc_int)

---------------------------------------

### MPLADC_INTCLEAR

Clears the interrupt flag. This macro is normally used in an interrupt service routine.

EXAMPLE

See [MPLADC_INT](#mpladc_int)

---------------------------------------

### MPLADC_START

Starts the conversion of the analog value on the input of the ADC. If the ADC was already started, it will continue to run. No other values and settings are influenced by starting the AD conversion.

EXAMPLE

See [MPLADC_INT](#mpladc_int)

---------------------------------------

### MPLADC_STOP

Stops the ADC conversion. If the ADC was already stopped, nothing will happen. No other values and settings are influenced by stopping the AD conversion.

---------------------------------------

### MPLADC_WAIT

Waits for the conversion that was automatically started or started by an [MPLADC_START](#mpladc_start) to be completed.

---------------------------------------

### MPLADC_CLEAR

Stops the ADC module, clears the interrupt flag and disables interrupts.
