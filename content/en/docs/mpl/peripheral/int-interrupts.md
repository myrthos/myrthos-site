---
author: Joost Mans
title: INT Interrupts
date: 2023-08-30
lastmod: 2023-08-30
Description: Overview of the INT functionality.
layout: docs
tocHideParenthesis: true
tocBreakUnderscore: true
--- 
<!-- cSpell:ignore Joost lastmod mplint periph -->

This section gives an overview of the available macros and other defines for interrupt configurations. The module specific interrupt macros and defines are handled in the individual modules.

## Using an interrupt

Besides the specific module interrupt settings and flags, which are defined in the modules, the following provide the macros to enable and disable the more generic interrupts.

---------------------------------------

### MPLINT_GLOBAL

This macro allows to disable or enable interrupts globally.  
Note that interrupt flags will be set when the event occurs, irrespective of whether or not interrupts are enabled. The software should take care to clear the flags prior to enabling the interrupt.

EXAMPLE  

```c
// Enable interrupts globally
MPLINT_GLOBAL = 1;

// Get the current interrupt status
uint8_t status = MPLINT_GLOBAL;

// Disable interrupts globally
MPLINT_GLOBAL = 0;

```

---------------------------------------

### MPLINT_PERIPH

This macro provides allows to disable or enable interrupts from peripherals and modules.  
Note that interrupt flags are set when the event occurs, irrespective of whether or not interrupts are enabled. The software should take care to clear the flags prior to enabling the interrupt.

EXAMPLE  

```c
// Enable peripheral interrupts globally
MPLINT_PERIPH = 1;

// Get the current peripheral interrupt status
uint8_t status = MPLINT_PERIPH;

// Disable peripheral interrupts globally
MPLINT_PERIPH = 0;

```
