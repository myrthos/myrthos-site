---
author: Joost Mans
title: PER Peripheral I/O
date: 2023-08-23
lastmod: 2023-08-23
Description: Overview of the PER functionality.
layout: docs
tocHideParenthesis: true
tocBreakUnderscore: true
--- 
<!-- cSpell:ignore Joost lastmod MCLRE MLPPER MPLPER MLPER mplpic mtag -->

The following gives an overview of the available macros and other defines for working with the I/O pins of the supported PIC controllers as a source or output of the different PIC modules. This is supported for the controllers of the following series:

* PIC16(L)F153xx
* PIC16(L)F183xx

In order to be able to set the I/O pins properly, the controller configuration pragma for the `PPS1WAY` configuration bit is to be set to `OFF`. This is part of the configuration pragmas that need to be set before the file `xc.h` or `mplpic.h` (which includes `xc.h`) is included in the main source file.

## Using Peripheral pins

There are two macros defined that need to be used for working with the PPS pins. These macros unlock the Peripheral Pin Select (PPS) to enable making changes, and after all changes are made, lock it again so it prevents changes being made after that.

---------------------------------------

### MPLPER_UNLOCK

Unlocks the PPS module and allows changes to be made. During the unlock, interrupts are temporarily disabled to prevent the unlock sequence from being interrupted.  
All MPL macros that allow to set an input or an output pin, will make sure that the unlock macro is called automatically.

EXAMPLE  

```c
// Unlock the PPS module
MPLPER_UNLOCK;

// Make A5 the output of CMP1 on a PIC18F24K40
MPLPER_OUT_RA5 = 0x0B

// Lock the PPS module
MPLPER_LOCK;
```

---------------------------------------

### MPLPER_LOCK

Locks the PPS module and prevents changes to be made. During the lock, interrupts are temporarily disabled to prevent the lock sequence from being interrupted. All MPL macros that allow to set an input or an output pin, will make sure that the lock macro is called automatically.

EXAMPLE  
See [MPLPER_UNLOCK](#mplper_unlock)

---------------------------------------

## Peripheral I/O

Several modules can use an input pin of the controller to drive some of its functionality or output results to an output pin. Each controller can have a different set of I/O pins that are supported. Defines are available for the input and output pins that can be used with macros that support them. These defines are available per controller, so it is not possible to select a pin that does not exist. Instead, a compiler error will be created.

The following is an overview of the supported I/O pins per controller:

### Inputs for PIC16(L)F153xx

{{< table "table-striped table-bordered" >}}
  Parameter  |     Pin     |15313|1532x|1534x|1535x|1537x|1538x|
:-----------:|:-----------:|:---:|:---:|:---:|:---:|:---:|:---:|
MPLPER_IN_RF7|RF7&nbsp;input    | | | | | |x|
MPLPER_IN_RF6|RF6&nbsp;input    | | | | | |x|
MPLPER_IN_RF5|RF5&nbsp;input    | | | | | |x|
MPLPER_IN_RF4|RF4&nbsp;input    | | | | | |x|
MPLPER_IN_RF3|RF3&nbsp;input    | | | | | |x|
MPLPER_IN_RF2|RF2&nbsp;input    | | | | | |x|
MPLPER_IN_RF1|RF1&nbsp;input    | | | | | |x|
MPLPER_IN_RF0|RF0&nbsp;input    | | | | | |x|
MPLPER_IN_RE2|RE2&nbsp;input    | | | | |x|x|
MPLPER_IN_RE1|RE1&nbsp;input    | | | | |x|x|
MPLPER_IN_RE0|RE0&nbsp;input    | | | | |x|x|
MPLPER_IN_RD7|RD7&nbsp;input    | | | | |x|x|
MPLPER_IN_RD6|RD6&nbsp;input    | | | | |x|x|
MPLPER_IN_RD5|RD5&nbsp;input    | | | | |x|x|
MPLPER_IN_RD4|RD4&nbsp;input    | | | | |x|x|
MPLPER_IN_RD3|RD3&nbsp;input    | | | | |x|x|
MPLPER_IN_RD2|RD2&nbsp;input    | | | | |x|x|
MPLPER_IN_RD1|RD1&nbsp;input    | | | | |x|x|
MPLPER_IN_RD0|RD0&nbsp;input    | | | | |x|x|
MPLPER_IN_RC7|RC7&nbsp;input    | | |x|x|x|x|
MPLPER_IN_RC6|RC6&nbsp;input    | | |x|x|x|x|
MPLPER_IN_RC5|RC5&nbsp;input    | |x|x|x|x|x|
MPLPER_IN_RC4|RC4&nbsp;input    | |x|x|x|x|x|
MPLPER_IN_RC3|RC3&nbsp;input    | |x|x|x|x|x|
MPLPER_IN_RC2|RC2&nbsp;input    | |x|x|x|x|x|
MPLPER_IN_RC1|RC1&nbsp;input    | |x|x|x|x|x|
MPLPER_IN_RC0|RC0&nbsp;input    | |x|x|x|x|x|
MPLPER_IN_RB7|RB7&nbsp;input    | | |x|x|x|x|
MPLPER_IN_RB6|RB6&nbsp;input    | | |x|x|x|x|
MPLPER_IN_RB5|RB5&nbsp;input    | | |x|x|x|x|
MPLPER_IN_RB4|RB4&nbsp;input    | | |x|x|x|x|
MPLPER_IN_RB3|RB3&nbsp;input    | | | |x|x|x|
MPLPER_IN_RB2|RB2&nbsp;input    | | | |x|x|x|
MPLPER_IN_RB1|RB1&nbsp;input    | | | |x|x|x|
MPLPER_IN_RB0|RB0&nbsp;input    | | | |x|x|x|
MPLPER_IN_RA7|RA7&nbsp;input    | | | |x|x|x|
MPLPER_IN_RA6|RA6&nbsp;input    | | | |x|x|x|
MPLPER_IN_RA5|RA5&nbsp;input    |x|x|x|x|x|x|
MPLPER_IN_RA4|RA4&nbsp;input    |x|x|x|x|x|x|
MPLPER_IN_RA3|RA3&nbsp;input[^1]|x|x|x|x|x|x|
MPLPER_IN_RA2|RA2&nbsp;input    |x|x|x|x|x|x|
MPLPER_IN_RA1|RA1&nbsp;input    |x|x|x|x|x|x|
MPLPER_IN_RA0|RA0&nbsp;input    |x|x|x|x|x|x|
[^1]: RA3 is only available as an input pin (15313/1532x/1534x) when in the configuration words, MCLRE is set to OFF and LVP is set to OFF. In other cases it is not available
{{< /table >}}
{{< mtag "br" />}}

### Inputs for PIC16(L)F183xx

{{< table "table-striped table-bordered" >}}
  Parameter  |     Pin     |18313|1832x|1834x|
:-----------:|:-----------:|:------------:|:------------:|:------------:|
MPLPER_IN_RC7|RC7&nbsp;input    | | |x|
MPLPER_IN_RC6|RC6&nbsp;input    | | |x|
MPLPER_IN_RC5|RC5&nbsp;input    | |x|x|
MPLPER_IN_RC4|RC4&nbsp;input    | |x|x|
MPLPER_IN_RC3|RC3&nbsp;input    | |x|x|
MPLPER_IN_RC2|RC2&nbsp;input    | |x|x|
MPLPER_IN_RC1|RC1&nbsp;input    | |x|x|
MPLPER_IN_RC0|RC0&nbsp;input    | |x|x|
MPLPER_IN_RB7|RB7&nbsp;input    | | |x|
MPLPER_IN_RB6|RB6&nbsp;input    | | |x|
MPLPER_IN_RB5|RB5&nbsp;input    | | |x|
MPLPER_IN_RB4|RB4&nbsp;input    | | |x|
MPLPER_IN_RA5|RA5&nbsp;input    |x|x|x|
MPLPER_IN_RA4|RA4&nbsp;input    |x|x|x|
MPLPER_IN_RA3|RA3&nbsp;input[^2]|x|x|x|
MPLPER_IN_RA2|RA2&nbsp;input    |x|x|x|
MPLPER_IN_RA1|RA1&nbsp;input    |x|x|x|
MPLPER_IN_RA0|RA0&nbsp;input    |x|x|x|
[^2]: RA3 is only available as an input pin when in the configuration words, MCLRE is set to OFF and LVP is set to OFF. In other cases it is not available
{{< /table >}}
{{< mtag "br" />}}

### Outputs for PIC16(L)F153xx

{{< table "table-striped table-bordered" >}}
  Parameter   |    Pin   |15313|1532x|1534x|1535x|1537x|1538x|
:------------:|:--------:|:---:|:---:|:---:|:---:|:---:|:---:|
MPLPER_OUT_RF7|RF7&nbsp;output| | | | | |x|
MPLPER_OUT_RF6|RF6&nbsp;output| | | | | |x|
MPLPER_OUT_RF5|RF5&nbsp;output| | | | | |x|
MPLPER_OUT_RF4|RF4&nbsp;output| | | | | |x|
MPLPER_OUT_RF3|RF3&nbsp;output| | | | | |x|
MPLPER_OUT_RF2|RF2&nbsp;output| | | | | |x|
MPLPER_OUT_RF1|RF1&nbsp;output| | | | | |x|
MPLPER_OUT_RF0|RF0&nbsp;output| | | | | |x|
MPLPER_OUT_RE2|RE2&nbsp;output| | | | |x|x|
MPLPER_OUT_RE1|RE1&nbsp;output| | | | |x|x|
MPLPER_OUT_RE0|RE0&nbsp;output| | | | |x|x|
MPLPER_OUT_RD7|RD7&nbsp;output| | | | |x|x|
MPLPER_OUT_RD6|RD6&nbsp;output| | | | |x|x|
MPLPER_OUT_RD5|RD5&nbsp;output| | | | |x|x|
MPLPER_OUT_RD4|RD4&nbsp;output| | | | |x|x|
MPLPER_OUT_RD3|RD3&nbsp;output| | | | |x|x|
MPLPER_OUT_RD2|RD2&nbsp;output| | | | |x|x|
MPLPER_OUT_RD1|RD1&nbsp;output| | | | |x|x|
MPLPER_OUT_RD0|RD0&nbsp;output| | | | |x|x|
MPLPER_OUT_RC7|RC7&nbsp;output| | |x|x|x|x|
MPLPER_OUT_RC6|RC6&nbsp;output| | |x|x|x|x|
MPLPER_OUT_RC5|RC5&nbsp;output| |x|x|x|x|x|
MPLPER_OUT_RC4|RC4&nbsp;output| |x|x|x|x|x|
MPLPER_OUT_RC3|RC3&nbsp;output| |x|x|x|x|x|
MPLPER_OUT_RC2|RC2&nbsp;output| |x|x|x|x|x|
MPLPER_OUT_RC1|RC1&nbsp;output| |x|x|x|x|x|
MPLPER_OUT_RC0|RC0&nbsp;output| |x|x|x|x|x|
MPLPER_OUT_RB7|RB7&nbsp;output| | |x|x|x|x|
MPLPER_OUT_RB6|RB6&nbsp;output| | |x|x|x|x|
MPLPER_OUT_RB5|RB5&nbsp;output| | |x|x|x|x|
MPLPER_OUT_RB3|RB3&nbsp;output| | | |x|x|x|
MPLPER_OUT_RB2|RB2&nbsp;output| | | |x|x|x|
MPLPER_OUT_RB1|RB1&nbsp;output| | | |x|x|x|
MPLPER_OUT_RB0|RB0&nbsp;output| | | |x|x|x|
MPLPER_OUT_RA7|RA7&nbsp;output| | | |x|x|x|
MPLPER_OUT_RA6|RA6&nbsp;output| | | |x|x|x|
MPLPER_OUT_RB4|RB4&nbsp;output| | |x|x|x|x|
MPLPER_OUT_RA5|RA5&nbsp;output|x|x|x|x|x|x|
MPLPER_OUT_RA4|RA4&nbsp;output|x|x|x|x|x|x|
MPLPER_OUT_RA3|RA3&nbsp;output| | | |x|x|x|
MPLPER_OUT_RA2|RA2&nbsp;output|x|x|x|x|x|x|
MPLPER_OUT_RA1|RA1&nbsp;output|x|x|x|x|x|x|
MPLPER_OUT_RA0|RA0&nbsp;output|x|x|x|x|x|x|
{{< /table >}}
{{< mtag "br" />}}

### Outputs for PIC16(L)F183xx

{{< table "table-striped table-bordered" >}}
  Parameter   |    Pin   |18313|1832x|1834x|
:------------:|:--------:|:---:|:---:|:---:|
MPLPER_OUT_RC7|RC7&nbsp;output| | |x|
MPLPER_OUT_RC6|RC6&nbsp;output| | |x|
MPLPER_OUT_RC5|RC5&nbsp;output| |x|x|
MPLPER_OUT_RC4|RC4&nbsp;output| |x|x|
MPLPER_OUT_RC3|RC3&nbsp;output| |x|x|
MPLPER_OUT_RC2|RC2&nbsp;output| |x|x|
MPLPER_OUT_RC1|RC1&nbsp;output| |x|x|
MPLPER_OUT_RC0|RC0&nbsp;output| |x|x|
MPLPER_OUT_RB7|RB7&nbsp;output| | |x|
MPLPER_OUT_RB6|RB6&nbsp;output| | |x|
MPLPER_OUT_RB5|RB5&nbsp;output| | |x|
MPLPER_OUT_RB4|RB4&nbsp;output| | |x|
MPLPER_OUT_RA5|RA5&nbsp;output|x|x|x|
MPLPER_OUT_RA4|RA4&nbsp;output|x|x|x|
MPLPER_OUT_RA3|RA3&nbsp;output| | | |
MPLPER_OUT_RA2|RA2&nbsp;output|x|x|x|
MPLPER_OUT_RA1|RA1&nbsp;output|x|x|x|
MPLPER_OUT_RA0|RA0&nbsp;output|x|x|x|
{{< /table >}}
{{< mtag "br" />}}

---------------------------------------

### MLPPER_OUT_RESET(pin)

Disconnects the port output pin from the peripheral and assign it as an output pin again. Note that the port settings such as TRISx and WPUx are not affected by this macro.

*pin*  
The output pin that needs to be disconnected from the module it is currently connected to. After this operation the output pin is a regular output pin again.

EXAMPLE

```c
// Assign C4 to itself again
MLPPER_OUT_RESET(MLPER_OUT_RC4);
```
