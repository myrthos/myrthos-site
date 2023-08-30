---
author: Joost Mans
title: PWR Power Savings
date: 2023-08-30
lastmod: 2023-08-30
Description: Overview of the PWR functionality.
layout: docs
--- 
<!-- cSpell:ignore Joost lastmod mplpwr fosc hfintosc lfintosc sosc -->

This section gives an overview of the available macros and other defines for power saving configurations. Note that each module will have a macro to turn that module off, which is also a power saving option. These macros are described with the module documentation.

## Macros for power saving

### MPLPWR_INIT(mode) {#mplpwr_init}

This macro sets the power saving mode that is to be used.

*mode*  
The power saving mode to be used. The following modes are supported:

Define                   |Timer
:-----------------------:|:---:
MPLPWR_MODE_IDLE         |Idle mode
MPLPWR_MODE_SLEEP_NORMAL |Sleep mode
MPLPWR_MODE_SLEEP_LOW    |Sleep mode with low internal power

In idle mode, the CPU and memory operations are halted, but the peripheral clocks continue to run, when using one of the following clocks: Fosc, HFINTOSC, LFINTOSC or SOSC.
Normal sleep mode, is like idle mode, but Fosc is also disabled.
Low sleep mode, is like normal sleep mode, but the internal voltage is lowered to reduce power consumption even more.

EXAMPLE  

```c
// Set power mode
MPLPWR_INIT(MPLPWR_MODE_SLEEP_LOW);

// Put controller to sleep
MPLPWR_START;
// ONe instruction is pre-fetched before going to sleep
NOP();

// If we get here, the device has woken
MPLPWR_STOP;
```

---------------------------------------

### MPLPWR_START

Sets the controller in idle or sleep mode, based on the configuration set by [MPLPWR_INIT](#mplpwr_init).  
After going to idle or sleep, the instruction following this macro is pre-fetched.

EXAMPLE  
See [MPLPWR_INIT](#mplpwr_init).

---------------------------------------

### MPLPWR_STOP

The idle mode is not automatically reset after waking up. Using this macro resets that mode.

EXAMPLE  
See [MPLPWR_INIT](#mplpwr_init).
