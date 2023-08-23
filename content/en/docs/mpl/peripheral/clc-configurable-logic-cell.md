---
author: Joost Mans
title: CLC Configurable Logic Cells
date: 2023-08-23
lastmod: 2023-08-23
Description: Overview of the CLC functionality.
layout: docs
--- 
<!-- cSpell:ignore Joost lastmod mocule MPLCLC -->

This section gives an overview of the available macros and other defines for the Configurable Logic Cells module.  
At the moment this support is limited to enabling and disabling the module. Other functionality is not yet supported.

## Using the Configurable Logic Cells

With the Configurable Logic Cells (CLC) module, it is possible to configure a specific sequence of logic gates that can be used for a specific solution. The output of the CLC can be used as in input, clock or trigger in other modules. The CLC module is enabled by default.

---------------------------------------

## MPLCLC_X_DISABLE

Disable the Configurable Logic Cells module. Disabling the module will reduce the power consumption of the controller.

The letter 'X' in the module is to be replaced with a number, ranging from 1 to 4, depending on the number of supported CLC modules for the used controller.

Controller|Supported
:--------:|:-------:
16F15xxx  | 1,2,3,4
16F183x3  | 1,2
16F1832x  | 1,2,3,4
16F1834x  | 1,2,3,4
{.table}

EXAMPLE

See [MPLCLC_X_ENABLE](#mplclc_enable)

---------------------------------------

## MPLCLC_X_ENABLE

Enable the Configurable Logic Cells module. Note that this is only needed after the [MPLCLC_X_DISABLE](#mplclc_disable) macro is used, as the module is enabled by default.

The letter 'X' in the module is to be replaced with a number, ranging from 1 to 4, depending on the number of supported CLC modules for the used controller.

Controller|Supported
:--------:|:-------:
16F15xxx  | 1,2,3,4
16F183x3  | 1,2
16F1832x  | 1,2,3,4
16F1834x  | 1,2,3,4
{.table}

EXAMPLE  

```c
// Disable CLC module 1
MPLCLC_1_DISABLE;

// ...

// Enable CLC module 1
MPLCLC_1_ENABLE;
```
