---
author: Joost Mans
title: NVM non volatile memory
date: 2023-08-30
lastmod: 2023-08-30
Description: Overview of the NVM functionality.
layout: docs
--- 
<!-- cSpell:ignore Joost lastmod eeprom mplnvm mplmodnvm resetvector usermem intvector fmem fsize pbuffer blocksize userid devid revid vref tmph afvr cfvr ersiz wlsiz ursiz eesiz pcnt emem esize -->

This section gives an overview of the available macros and other defines for the non volatile memories: Flash and EEPROM.

With the non volatile memory (NVM) module it is possible to read and write to user accessible areas of the Flash and EEPROM modules. All supported controllers will have Flash memory, but not all supported controllers have EEPROM memory. The PIC16(L)F18xxx series of controllers support reading from and writing to EEPROM memory.

## Macro's for the non volatile memory

### MPLNVM_DISABLE

Disable the non volatile memory module. Disabling the module will reduce the power consumption of the controller.

EXAMPLE

See [MPLNVM_ENABLE](#mplnvm_enable)

---------------------------------------

### MPLNVM_ENABLE

Enable the non volatile memory module. Note that this is only needed after the [MPLNVM_DISABLE](#mplnvm_disable) macro is used, as the module is enabled by default.

EXAMPLE  

```c
// Disable the NVM module
MPLNVM_DISABLE;

// ...

// Enable the NVM module
MPLNVM_ENABLE;
```

---------------------------------------

### MPLNVM_PROGRAM_READ(address) {#mplnvm_program_read}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file{{< /mstyle >}}.

Reads a 16-bit word from a FLASH program memory location. The read value is returned as an *uint16_t*.

*address*  
The address to read a 16 bit word from. The address is one of the following supported addresses:

{{< table "table-striped table-bordered" >}}
Define|Description
:-----|:---
MPLNVM_ADDR_RESETVECTOR|The address location to jump to after a reset
MPLNVM_ADDR_USERMEM0   |User memory location 0
MPLNVM_ADDR_USERMEM1   |User memory location 1
MPLNVM_ADDR_USERMEM2   |User memory location 2
MPLNVM_ADDR_INTVECTOR  |The address location to jump to on an interrupt
MPLNVM_ADDR_FMEM       |Flash memory start address
{{< /table >}}

When as an address MPLNVM_ADDR_FMEM is used, this is to be seen as a starting address. The last address that can be read from is MPLNVM_FSIZE-1. The total number of words that can be written is MPLNVM_FSIZE - MPLNVM_ADDR_FMEM.  
Note that this memory space is also used as program memory.

---------------------------------------

### MPLNVM_PROGRAM_WRITE(address, word) {#mplnvm_program_write}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file{{< /mstyle >}}.

Writes a 16-bit word to a FLASH program memory location.

*address*  
The address to write a 16 bit word to. The address is one of the following supported addresses:

{{< table "table-striped table-bordered" >}}
Define|Description
:-----|:---
MPLNVM_ADDR_RESETVECTOR|The address location to jump to after a reset
MPLNVM_ADDR_USERMEM0   |User memory location 0
MPLNVM_ADDR_USERMEM1   |User memory location 1
MPLNVM_ADDR_USERMEM2   |User memory location 2
MPLNVM_ADDR_INTVECTOR  |The address location to jump to on an interrupt
MPLNVM_ADDR_FMEM       |Flash memory start address
{{< /table >}}

When as an address MPLNVM_ADDR_FMEM is used, this is to be seen as a starting address. The last address that can be written to is MPLNVM_FSIZE-1. The total number of words that can be written is MPLNVM_FSIZE - MPLNVM_ADDR_FMEM.  
Note that this memory space is also used as program memory.

*word*
The 16-bit word to write to the specified address.

---------------------------------------

### MPLNVM_PROGRAM_WRITE_BLOCK(address, pbuffer) {#mplnvm_program_write_block}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file{{< /mstyle >}}.

Writes an array of 16-bit words to a FLASH program memory location.

*address*  
The address to write a 16 bit word to. The address is one of the following supported addresses:

{{< table "table-striped table-bordered" >}}
Define|Description
:-----|:---
MPLNVM_ADDR_RESETVECTOR|The address location to jump to after a reset
MPLNVM_ADDR_USERMEM0   |User memory location 0
MPLNVM_ADDR_USERMEM1   |User memory location 1
MPLNVM_ADDR_USERMEM2   |User memory location 2
MPLNVM_ADDR_INTVECTOR  |The address location to jump to on an interrupt
MPLNVM_ADDR_FMEM       |Flash memory start address
{{< /table >}}

When as an address MPLNVM_ADDR_FMEM is used, this is to be seen as a starting address. The last address that can be written to is MPLNVM_FSIZE-1. The total number of words that can be written is MPLNVM_FSIZE - MPLNVM_ADDR_FMEM.  
Note that this memory space is also used as program memory.

*pbuffer*
A 16 bit pointer to a buffer with a size of MPLNVM_BLOCKSIZE, which contains the words to write.

---------------------------------------

### MPLNVM_USERID_WRITE(pbuffer) {#mplnvm_userid_write}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file{{< /mstyle >}}.

Writes the four words of the USERID to memory.

*pbuffer*
A 16 bit pointer to a buffer with a size of 4 16-bits values, which contains the contents of the four USERID words that are to be written.

---------------------------------------

### MPLNVM_REGISTER_READ(address) {#mplnvm_register_read}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file{{< /mstyle >}}.

Reads a 16-bit word from a register memory location. The read value is returned as an *uint16_t*.

*address*  
The address to read a 16 bit word from. The address is one of the following supported addresses:

{{< table "table-striped table-bordered" >}}
Define|Description
:-----|:---
MPLNVM_REG_DEVID  |The ID of the used controller
MPLNVM_REG_REVID  |The revision ID of the used controller
MPLNVM_REG_USERID0|User defined ID 0
MPLNVM_REG_USERID1|User defined ID 1
MPLNVM_REG_USERID2|User defined ID 2
MPLNVM_REG_USERID3|User defined ID 3
MPLNVM_REG_CONFIG1|Configuration register 1
MPLNVM_REG_CONFIG2|Configuration register 2
MPLNVM_REG_CONFIG3|Configuration register 3
MPLNVM_REG_CONFIG4|Configuration register 4
{{< /table >}}

For the PIC16(L)F15xxx series controllers. The following additional registers are also available:

{{< table "table-striped table-bordered" >}}
Define|Description
:-----|:---
MPLNVM_REG_CONFIG5|Configuration register 5
MPLNVM_DIA_MUI  |The starting address of 9 words, which indicates a unique ID, created during manufacturing
MPLNVM_DIA_EUI  |The starting address of 8 words, which indicates a unique ID, created during manufacturing and specified by the end user
MPLNVM_DIA_TMPL |TMP module low range reference value at 90 degrees, Vdd = 3V, Vref = 2.048V
MPLNVM_DIA_TMPH |TMP module Hight range reference value at 90 degrees, Vdd = 3V, Vref = 2.048V
MPLNVM_DIA_AFVR1|FVR module ADC reference value for 1.024V
MPLNVM_DIA_AFVR2|FVR module ADC reference value for 2.048V
MPLNVM_DIA_AFVR4|FVR module ADC reference value for 4.096V
MPLNVM_DIA_CFVR1|FVR module CMP reference value for 1.024V
MPLNVM_DIA_CFVR2|FVR module CMP reference value for 2.048V
MPLNVM_DIA_CFVR4|FVR module CMP reference value for 4.096V
MPLNVM_DCI_ERSIZ|Flash: The size of a row to erase
MPLNVM_DCI_WLSIZ|Flash: The number of write latches
MPLNVM_DCI_URSIZ|Flash: The number of user rows (size of memory / MPLNVM_DCI_ERSIZ)
MPLNVM_DCI_EESIZ|EEPROM: The size of EEPROM memory
MPLNVM_DCI_PCNT |The number of pins of the controller
{{< /table >}}

---------------------------------------

### MPLNVM_EEPROM_READ(address) {#mplnvm_eeprom_read}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file{{< /mstyle >}}.

Reads an 8-bit byte from an EEPROM memory location. The read value is returned as an *uint8_t*. This macro is only supported for the PIC(L)F18xxx series of controllers.

*address*  
A valid EEPROM address is in the range: MPLNVM_ADDR_EMEM to MPLNVM_ADDR_EMEM + MPLNVM_ESIZE-1.

---------------------------------------

### MPLNVM_EEPROM_WRITE(address, byte) {#mplnvm_eeprom_write}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file{{< /mstyle >}}.

Writes an 8-bit byte to an EEPROM memory location.  
This macro is only supported for the PIC(L)F18xxx series of controllers.

*address*  
A valid EEPROM address is in the range: MPLNVM_ADDR_EMEM to MPLNVM_ADDR_EMEM + MPLNVM_ESIZE-1.

*byte*
The 8-bit byte to write to the specified address.
