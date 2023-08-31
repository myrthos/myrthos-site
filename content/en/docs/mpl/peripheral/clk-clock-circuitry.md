---
author: Joost Mans
title: CLK Clock Circuitry
date: 2023-08-30
lastmod: 2023-08-30
Description: Overview of the CLK functionality.
layout: docs
--- 
<!-- cSpell:ignore Joost lastmod mplclk mplmodclk fosc delayms xtal mplconfig herz delayus -->

The following gives an overview of the available macros and other defines for setting the internal clock of the supported PIC controllers. Not all frequencies are supported by all controllers.

## Macro's for the Clock

### MPLCLK_DISABLE

Disables the internal clock module. This does not disable the actual clock, as it is always enabled, but the clock will no longer be delivered to other internal modules. Disabling the module will reduce the power consumption of the controller.

---------------------------------------

### MPLCLK_ENABLE

Enables the internal clock module. This does not enable the actual clock, as it is always enabled, but the clock will be delivered again to other internal modules. Note that this is only needed after the [MPLCLK_DISABLE](#mplclk_disable) macro is used, as the module is enabled by default.

---------------------------------------

### MPLCLK_INIT

Initializes the clock and enables the internal frequencies.  
If an external clock frequency is used, this macro is not needed, but a call should be made to [MPLCLK_SET_CYCLE](#mplclk_set_cycle) in order to set the correct cycle time.

EXAMPLE:

See [MPLCLK_SET](#mplclk_set)

---------------------------------------

### MPLCLK_SET(freq, wait) {#mplclk_set}

{{< mstyle "underline" >}}Usage of this macro, requires the file `mplmodclk.c` to be included as a source file{{< /mstyle >}}.

Sets the desired internal clock frequency and optionally waits for the clock setting to be completed.  
This macro will also set an internal variable that indicates what the resulting cycle time is of the selected frequency. This cycle time is equal to 1/(Fosc/4). This is equal to the cycle time of executing a single instruction on the selected controller.
If an external clock frequency is used, this macro is not needed , but a call should be made to [MPLCLK_SET_CYCLE](#mplclk_set_cycle) in order to set the correct cycle time.  

*freq*  
One of the supported internal clock frequencies to set. Not supported clock frequencies will not be defined either. As a result selecting a not supported clock frequency will result in a compiler error. The following shows the supported frequencies for each of the supported controllers:

{{< table "table-striped table-bordered" >}}
Frequency            ||PIC16(L)F153xx|PIC16(L)F183xx|PIC18(L)FxxK40|PIC18(L)FxxK42|
-------------|--------|:------------:|:------------:|:------------:|:------------:|
MPLCLK_F64M  |64 MHz  |              |              |      x       |      x       |
MPLCLK_F48M  |48 MHz  |              |              |      x       |      x       |
MPLCLK_F32M  |32 MHz  |       x      |      x       |      x       |      x       |
MPLCLK_F24M  |24 MHz  |       x      |      x       |      x       |      x       |
MPLCLK_F16M  |16 MHz  |       x      |      x       |      x       |      x       |
MPLCLK_F12M  |12 MHz  |       x      |      x       |      x       |      x       |
MPLCLK_F8M   |8 MHz   |       x      |      x       |      x       |      x       |
MPLCLK_F6M   |6 MHz   |       x      |      x       |      x       |      x       |
MPLCLK_F4M   |4 MHz   |       x      |      x       |      x       |      x       |
MPLCLK_F3M   |3 MHz   |       x      |      x       |      x       |      x       |
MPLCLK_F2M   |2 MHz   |       x      |      x       |      x       |      x       |
MPLCLK_F1M   |1 MHz   |       x      |      x       |      x       |      x       |
MPLCLK_F500K |500 KHz |       x      |      x       |      x       |      x       |
MPLCLK_F250K |250 KHz |       x      |      x       |      x       |      x       |
MPLCLK_F125K |125 KHz |       x      |      x       |      x       |      x       |
MPLCLK_LOW   |31 KHz  |       x      |      x       |      x       |      x       |
{{< /table >}}

*wait*  
It could take a short amount of time before the clock has settled and is stable. The implementation of this macro is that a status flag is polled in a loop. The *wait* parameter indicates the maximum number of times this poll is performed, so it should be set high enough, especially for the higher frequencies.  
When either the flag is set or the loop is finished the macro will return. When a value of 0 is used for *wait* there is no check performed and the macro returns immediately. In that case the [MPLCLK_WAIT](#mplclk_wait) macro can be used to wait for completion of setting the new clock.

EXAMPLE  

```c
// Initialize the clock and set a frequency of 32 MHz
MPLCLK_INIT;
MPLCLK_SET(MPLCLK_F32, 10000);   
```

---------------------------------------

### MPLCLK_WAIT(wait) {#mplclk_wait}

Waits until the initialization of the clock has been completed.

*wait*  
It could take a short amount of time before the clock has settled and is stable. The implementation of this macro is that a status flag is polled in a loop. The *wait* parameter indicates the maximum number of times this poll is performed, so it should be set high enough, especially for the higher frequencies.  
When either the flag is set or the loop is finished the macro will return. When a value of 0 is used for *wait* there is no check performed and the macro returns immediately.

EXAMPLE:

```c
// Initialize the clock and set a frequency of 32 MHz
MPLCLK_INIT;
MPLCLK_SET(MPLCLK_F32, 0);

// Do something else

// Wait until the clock has actually changed
MPLCLK_WAIT(10000);
```

---------------------------------------

### MPLCLK_GET_CYCLE

{{< mstyle "underline" >}}Usage of this macro, requires the file `mplmodclk.c` to be included as a source file{{< /mstyle >}}.

Retrieves the current instruction cycle time. The cycle time is used internally by other macros, like those for the PWM.  
It only makes sense to use this macro after a clock has been set via the [MPLCLK_SET](#mplclk_set) macro, or after the cycle time is set [MPLCLK_SET_CYCLE](#mplclk_set_cycle), otherwise 0, or an incorrect value will be returned.  
The returned value is an **uint16_t** and shows the value in nanoseconds. When the clock frequency is MPLCLK_LOW, the instruction cycle time does not fit in a 16-bit unsigned integer and the value 0xFFFF is returned. Other macros that use this instruction cycle time will correct for this automatically.  

EXAMPLE  

```c
// Initialize the clock and set a frequency of 32 MHz
MPLCLK_INIT;
MPLCLK_SET(MPLCLK_F32, 10000);   

// Get the instruction cycle time, should be 125 (nanoseconds)
uint16_t cycle_time = MPLCLK_GET_CYCLE;
```

---------------------------------------

### MPLCLK_SET_CYCLE(cycle) {#mplclk_set_cycle}

{{< mstyle "underline" >}}Usage of this macro, requires the file `mplmodclk.c` to be included as a source file{{< /mstyle >}}.

Overrides the current cycle time with a new value. This is commonly used when an external crystal is used as a clock frequency, to set the correct cycle time, belonging to that frequency.  

*cycle*  
The new cycle time, which should be equal to 1/(Fosc/4) and should be given as a value in nanoseconds. The value is an **uint16_t**.

EXAMPLE  

```c
// Set the clock registers correctly for an external clock
 ..
 ..

// Set the instruction cycle time belonging to the used clock.
// If we assume it is 20MHz, the instruction cycle time is 200 (nanoseconds)
MPLCLK_SET_CYCLE(200);
```

---------------------------------------

### MPLCLK_DELAYMS(duration) {#mplclk_delayms}

{{< mstyle "underline" >}}Usage of this macro, requires the file `mplmodclk.c` to be included as a source file{{< /mstyle >}}.

Waits for the specified amount of milliseconds. The behavior of this macro differs depending on whether or not **_XTAL_FREQ** is defined in *mplconfig.h*.  
If it is defined, the value of **_XTAL_FREQ** needs to be the actually used frequency in Herz.

If **_XTAL_FREQ** is defined with the correct frequency in *mplconfig.h*, and the duration to wait for is a constant value (i.e. not a variable) it is more efficient to use the internal XC8 inline function **__delay_ms(duration)**, as it is more accurate, given that is optimized during compilation for a specific frequency and duration.

*duration*  
The time to wait for in milliseconds, entered as an **uint16_t**.

If **_XTAL_FREQ** is defined with the correct frequency in *mplconfig.h*, and the duration to wait for is not a constant value an internal delay function is called, which will wait for 1 millisecond in a loop. This means there is a small overhead that with small duration values and low clock frequencies, results in a small inaccuracy.  
If **_XTAL_FREQ** is not defined in *mplconfig.h*, a different internal delay function is called, which has a significant overhead, resulting in not being able to set the minimum delay of 1 millisecond for all frequencies. The following table shows what the minimum duration is per clock frequency in this case:

{{< table "table-striped table-bordered" >}}
Frequency|Duration
:-------:|:------:
  64MHz  |  1ms
  48MHz  |  1ms
  32MHz  |  1ms
  24MHz  |  1ms
  16MHz  |  1ms
  12MHz  |  1ms
   8MHz  |  2ms
   6MHz  |  2ms
   4MHz  |  5ms
   3MHz  |  5ms
   2MHz  |  10ms
   1MHz  |  15ms
 500KHz  |  25ms
 250KHz  |  50ms
 125KHz  | 100ms
  31KHz  | 400ms
{{< /table >}}

---------------------------------------

### MPLCLK_DELAYUS(duration) {#mplclk_delayus}

{{< mstyle "underline" >}}Usage of this macro, requires the file `mplmodclk.c` to be included as a source file{{< /mstyle >}}.

Waits for the specified amount of microseconds. The behavior of this macro differs depending on whether or not **_XTAL_FREQ** is defined in *mplconfig.h*.  
If it is defined, the value of **_XTAL_FREQ** needs to be the actually used frequency in Herz.

If **_XTAL_FREQ** is defined with the correct frequency in *mplconfig.h*, and the duration to wait for is a constant value (i.e. not a variable) it is more efficient to use the internal XC8 inline function **__delay_us(duration)**, as it is more accurate, given that is optimized during compilation for a specific frequency and duration.

*duration*  
The time to wait for in microseconds, entered as an **uint16_t**.

If **_XTAL_FREQ** is defined with the correct frequency in *mplconfig.h*, an internal delay function is called, which will wait for 1 microsecond in a loop. This means there is a small overhead that with short duration values and low clock frequencies, results in a small inaccuracy.  
If **_XTAL_FREQ** is not defined in *mplconfig.h*, the [MPLCLK_DELAYMS](#mplclk_delayms) macro is called, with the duration value divided by 1000.
