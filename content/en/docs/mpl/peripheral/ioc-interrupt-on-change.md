---
author: Joost Mans
title: IOC Interrupt on Change
date: 2023-08-30
lastmod: 2023-08-30
Description: Overview of the IOC functionality.
layout: docs
tocHideParenthesis: true
tocBreakUnderscore: true
--- 
<!-- cSpell:ignore Joost lastmod mplioc -->

This section gives an overview of the available macros and other defines for interrupt on change configurations.

## Using an interrupt on change

An interrupt on change can be set to an input pin of the used controller. Currently only enabling or disabling the module itself is supported. Disabling the module saves power. The IOC module is enabled by default.

---------------------------------------

### MPLIOC_DISABLE

Disable the Interrupt On Change module. Disabling the module will reduce the power consumption of the controller.

EXAMPLE  
See [MPLIOC_ENABLE](#mplioc_enable)

---------------------------------------

### MPLIOC_ENABLE

Enable the Interrupt On Change module. Note that this is only needed after the [MPLIOC_DISABLE](#mplioc_disable) macro is used, as the module is enabled by default.

EXAMPLE  

```c
// Disable the IOC module
MPLIOC_DISABLE;

// ...

// Enable the IOC module
MPLIOC_ENABLE;
```
