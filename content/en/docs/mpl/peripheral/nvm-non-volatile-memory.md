---
author: Joost Mans
title: NVM non volatile memory
date: 2023-08-30
lastmod: 2023-08-30
Description: Overview of the NVM functionality.
layout: docs
tocBreakParenthesis: true
tocBreakUnderscore: true
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

### MPLNVM_WRITE_ERROR

This macro is a value that is either 0 (no error) or 1 (write error) and it is set by the internal NVM logic in case of a write error. The error status can be read after one of the NVM macros that write data is called.  
It needs to be reset by the user, by assigning the value 0 to it.

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

EXAMPLE

See [MPLNVM_PROGRAM_WRITE](#mplnvm_program_write).

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

EXAMPLE

```c
// Set the base address
uint16_t address = MPLNVM_FSIZE - MPLNVM_BLOCKSIZE;

// Reset the error state
MPLNVM_WRITE_ERROR = 0;

// Write a single byte to the base address + 2
MPLNVM_PROGRAM_WRITE(address+2, 0xAA);

// Check error state
if (MPLNVM_WRITE_ERROR)
{
    // A write error occurred, handle it
    // ...

    // Reset the error state
    MPLNVM_WRITE_ERROR = 0;
}
else
{
    // Read the byte back
    uint16_t data = MPLNVM_PROGRAM_READ(address+2);
    if (data != 0xAA)
    {
        // The read data is not the same as the written data.
    }
}
```

---------------------------------------

### MPLNVM_PROGRAM_WRITE_BLOCK(address, pbuffer) {#mplnvm_program_write_block}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file{{< /mstyle >}}.

Writes an array of 16-bit words to a FLASH program memory location.

*address*  
The starting address to write the 16 bit words in *pbuffer* to. The address is one of the following supported addresses:

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

EXAMPLE

```c
// Set the base address
uint16_t address = MPLNVM_FSIZE - MPLNVM_BLOCKSIZE;

// Reset error state
MPLNVM_WRITE_ERROR = 0;

// Fill a buffer of words with data
uint16_t buf[MPLNVM_BLOCKSIZE];
uint8_t index;
for (index = 0; index < MPLNVM_BLOCKSIZE; index++)
{
    buf[index] = index * 3;    
}
// Write it as one block
MPLNVM_PROGRAM_WRITE_BLOCK(address, buf);

// Check error state
if (MPLNVM_WRITE_ERROR)
{
    // A write error occurred, handle it
    // ...

    // Reset the error state
    MPLNVM_WRITE_ERROR = 0;
}
else
{
    // Read the data back to verify it
    uint16_t data;
    for (index = 0; index < MPLNVM_BLOCKSIZE; index++)
    {
        // Read on word
        data = MPLNVM_PROGRAM_READ(address+index);
        if (data != buf[index])
        {
            // The data is incorrect
        }
    }
}
```

---------------------------------------

### MPLNVM_USERID_WRITE(pbuffer) {#mplnvm_userid_write}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file{{< /mstyle >}}.

Writes the four words of the USERID to memory.

*pbuffer*  
A 16 bit pointer to a buffer with a size of 4 16-bits values, which contains the contents of the four USERID words that are to be written.

EXAMPLE

```c
// Reset error state
MPLNVM_WRITE_ERROR = 0;

// Write UserID
uint16_t buf[4];
buf[0] = 0x0101;
buf[1] = 0x0010;
buf[2] = 0x0A55;
buf[3] = 0x05AA;
MPLNVM_USERID_WRITE(buf);

// Check error state
if (MPLNVM_WRITE_ERROR)
{
    // A write error occurred, handle it
    // ...

    // Reset the error state
    MPLNVM_WRITE_ERROR = 0;
}
else
{
    // Read User ID
    uint16_t address = MPLNVM_REG_USERID0;
    uint16_t data;
    for (uint8_t index = 0; index < 4; index++)
    {
        data = MPLNVM_REGISTER_READ(address+index);
        if (data != buf[index])
        {
            // The data is incorrect
        }
    }
}
```

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

EXAMPLE

See [MPLNVM_USERID_WRITE](#mplnvm_userid_write).

---------------------------------------

### MPLNVM_EEPROM_READ(address) {#mplnvm_eeprom_read}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file and a controller with an EEPROM{{< /mstyle >}}.

Reads an 8-bit byte from an EEPROM memory location. The read value is returned as an *uint8_t*. This macro is only supported for the PIC(L)F18xxx series of controllers.

*address*  
A valid EEPROM address is in the range: MPLNVM_ADDR_EMEM to MPLNVM_ADDR_EMEM + MPLNVM_ESIZE-1.

EXAMPLE

See [MPLNVM_EEPROM_WRITE](#mplnvm_eeprom_write).

---------------------------------------

### MPLNVM_EEPROM_WRITE(address, byte) {#mplnvm_eeprom_write}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file and a controller with an EEPROM{{< /mstyle >}}.

Writes an 8-bit byte to an EEPROM memory location.  
This macro is only supported for the PIC(L)F18xxx series of controllers.

*address*  
A valid EEPROM address is in the range: MPLNVM_ADDR_EMEM to MPLNVM_ADDR_EMEM + MPLNVM_ESIZE-1.

*byte*  
The 8-bit byte to write to the specified address.

EXAMPLE

```c
const uint8_t data[16] = {
    /* 0x00-0x0F */ 0xCF, 0x20, 0x7C, 0xC2, 0x85, 0x48, 0xC7, 0x81, 0xC7, 0xC7, 0x87, 0xD2, 0x46, 0xD5, 0x47, 0x08,
};
    
// Write data to internal EEPROM
for (int index = 0; index < 16; index++)
{
    MPLNVM_EEPROM_WRITE(MPLNVM_ADDR_EMEM + (uint16_t)index, data[index]);
}

// Check if the data has been written properly
for (int index = 0; index < 16; index++)
{
    if (MPLNVM_EEPROM_READ(MPLNVM_ADDR_EMEM  + (uint16_t)index) != data[index])
    {
        // The data is not the same
    }
}
```

---------------------------------------

### MPLNVM_EEPROM_READ_WORD(address) {#mplnvm_eeprom_read_word}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file and a controller with an EEPROM{{< /mstyle >}}.

Reads a 16-bit word from an EEPROM memory location. The read value is returned as an *uint16_t*.

*address*  
A valid EEPROM address is in the range: MPLNVM_ADDR_EMEM to MPLNVM_ADDR_EMEM + MPLNVM_ESIZE-1.

EXAMPLE

See [MPLNVM_EEPROM_WRITE_WORD](#mplnvm_eeprom_write_word).

---------------------------------------

### MPLNVM_EEPROM_WRITE_WORD(address, word) {#mplnvm_eeprom_write_word}

{{< mstyle "underline" >}}Usage of this macro requires to use the `mplmodnvm16.c` source file and a controller with an EEPROM{{< /mstyle >}}.

Writes a 16-bit word to an EEPROM memory location.  

*address*  
A valid EEPROM address in the range: MPLNVM_ADDR_EMEM to MPLNVM_ADDR_EMEM + MPLNVM_ESIZE-1.

*word*  
The 16-bit word to write to the specified address.

EXAMPLE

```c
const uint16_t dataInt[32] = {
    /*                 0       1       2       3       4       5       6       7       8       9       A       B       C       D       E       F */
    /* 0x00-0x0F */ 0xCFA3, 0x8120, 0x47C6, 0xC5C2, 0x8145, 0x6487, 0xC6C7, 0x8182, 0xC7C7, 0xC5C7, 0x83C7, 0xD5D2, 0x4683, 0xD592, 0x4687, 0x0287,
    /* 0x10-0x1F */ 0x5644, 0xC2E3, 0x0828, 0x6609, 0x2808, 0x0345, 0x2844, 0x4C8A, 0x11D7, 0x29C7, 0x54D4, 0x106C, 0x01D0, 0x5454, 0x3810, 0x1038,
};
    
// Write data to internal EEPROM
for (int index = 0; index < 32; index++)
{
    MPLNVM_EEPROM_WRITE_WORD( (((uint16_t)index) << 1) + MPLNVM_ADDR_EMEM, dataInt[index]);
}

// Check if the data has been written properly
for (int index = 0; index < 32; index++)
{
    if (MPLNVM_EEPROM_READ_WORD( (((uint16_t)index) << 1) + MPLNVM_ADDR_EMEM) != dataInt[index])
    {
        // The data is not the same
    }
}
```
