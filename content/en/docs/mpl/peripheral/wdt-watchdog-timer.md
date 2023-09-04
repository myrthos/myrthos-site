---
author: Joost Mans
title: WDT Watchdog Timer
date: 2023-08-30
lastmod: 2023-08-30
Description: Overview of the WDT functionality.
layout: docs
tocBreakParenthesis: true
tocBreakUnderscore: true
--- 
<!-- cSpell:ignore Joost lastmod mplwdt wdtcps wdtcws wdtccs wdte swdten mplwt -->

The following gives an overview of the available macros and other defines for the Watchdog Timer module of the supported PIC controllers.

## Macros for WDT

### Configuration pragmas

The watchdog timer macros that are described, will only function properly if the watchdog is configured for software control. This means that the following configuration pragmas have to be set:  
For 16F153xx:

- #pragma config WDTCPS = WDTCPS_31  
This enables the timer value to be controlled by software
- #pragma config WDTCWS = WDTCWS_7  
This enables the window value to be controlled by software
- #pragma config WDTCCS = SC  
This enables the clock source to be controlled by software
- #pragma config WDTE = SWDTEN  
This enables to start and stop the watchdog timer in software

For 16F183xx:

- #pragma config WDTE = SWDTEN  
This enables to start and stop the watchdog timer in software

---------------------------------------

### MPLWDT_CLEAR

Resets the watchdog timer, after which it starts counting again. When the windowed mode is supported by the used controller and active (window is less than 100%, see [MPLWDT_WINDOW](#mplwdt_window)), this macro needs to be called when the window is open. If this macro is called when the window is closed, a window violation will occur and a reset will be generated.

In the case of the window to be less than 100%, the watchdog timer also needs to be armed. This is accomplished by calling [MPLWDT_START](#mplwdt_start).

EXAMPLE

See [MPLWDT_WINDOW](#mplwdt_window)

---------------------------------------

### MPLWDT_INIT(timer) {#mplwdt_init}

Initializes the watchdog timer module with a timer duration. The watchdog timer counts up to the value that is set and will reset the controller when it reaches the specified timer value. To restart the watchdog timer and prevent the reset to happen, the macro [MPLWDT_CLEAR](#mplwdt_clear) needs to be called, which will reset the watchdog, after which it starts counting again until the specified time has expired, or another call to the MPLWDT_CLEAR macro is made. After a reset the default timeout period is 2 seconds.

For the 16F153xx series of controllers, also a window can be set by means of the [MPLWDT_WIN](#mplwdt_win) macro, which specifies a valid window in which the timer can be cleared. By using this macro that window is set to 100%, which makes it equal in behavior to other controllers who do not have this window setting.

Note that the watchdog module can be disabled by the configuration pragma's, in which case initializing the watchdog, will have no effect.

*timer*  
The timer value of the watchdog. If the timer value specified has ended, the watchdog module will generate a reset. The following timer values are supported:

{{< table "table-striped table-bordered" >}}
Define          |Timer
:--------------:|:---:
MPLWDT_TMR_1MS  |1 millisecond
MPLWDT_TMR_2MS  |2 milliseconds
MPLWDT_TMR_4MS  |4 milliseconds
MPLWDT_TMR_8MS  |8 milliseconds
MPLWDT_TMR_16MS |16 milliseconds
MPLWDT_TMR_32MS |32 milliseconds
MPLWDT_TMR_64MS |64 milliseconds
MPLWDT_TMR_128MS|128 milliseconds
MPLWDT_TMR_256MS|256 milliseconds
MPLWDT_TMR_512MS|512 milliseconds
MPLWDT_TMR_1S   |1 second
MPLWDT_TMR_2S   |2 second
MPLWDT_TMR_4S   |4 second
MPLWDT_TMR_8S   |8 second
MPLWDT_TMR_16S  |16 second
MPLWDT_TMR_32S  |32 second
MPLWDT_TMR_64S  |64 second
MPLWDT_TMR_128S |128 second
MPLWDT_TMR_256S |256 second
{{< /table >}}

Note that the above values are approximations. a 31KHz clock is used to get as close as possible to the desired timer value.

EXAMPLE

```c
// Initialize the watchdog by setting a 1 second timer value
MPLWDT_INIT(MPLWDT_TMR_1S);
```

---------------------------------------

### MPLWDT_START

Starts the watchdog timer and in the case of using the watchdog in windowed mode (see [MPLWDT_WINDOW](#mplwdt_window)), if that mode is supported, also arms the watchdog timer.

If the configuration pragma WDTE (see [pragma settings](#mplwdt_pragma)) is not set to be software controlled, the watchdog timer cannot be started in this way, but it will be armed when windowed mode is used and supported.

EXAMPLE

See [MPLWDT_WINDOW](#mplwdt_window)

---------------------------------------

### MPLWDT_STOP

Stops the watchdog timer.

If the configuration pragma WDTE (see [pragma settings](#mplwdt_pragma)) is not set to be software controlled, the watchdog timer cannot be stopped in this way

EXAMPLE

See [MPLWDT_WINDOW](#mplwdt_window)

---------------------------------------

### MPLWDT_WINDOW(window) {#mplwdt_window}

{{< mstyle "underline" >}}This macro is only supported by the following controller series: 16F153xx{{< /mstyle >}}.

Next to setting a timer value for the watchdog, it is also possible to set a window that specifies the range in which it is allowed that a [MPLWDT_CLEAR](#mplwdt_clear) call is made. if the MPLWDT_CLEAR macro is called outside of that window a window violation occurs and the controller is reset. See also the next image.
![alt text](images/wwdt.jpg "Windowed Watchdog Timer")  

*window*  
The duration of the window period relative to the total duration. Only in this valid window period an MPLWT_CLEAR is allowed to be used. The following are the allowed values.

{{< table "table-striped table-bordered" >}}
 window       |Size
:------------|----:
MPLWDT_WIN_100|100%
MPLWDT_WIN_875|87.5%
MPLWDT_WIN_75 |75%
MPLWDT_WIN_625|62.5%
MPLWDT_WIN_50 |50%
MPLWDT_WIN_375|37.5%
MPLWDT_WIN_25 |25%
MPLWDT_WIN_125|12.5%
{{< /table >}}

Note that if the window is unequal to 100%, the watchdog timer also needs to be armed. Calling the [MPLWDT_START](#mplwdt_start) macro will do that. Even if the [pragma settings](#mplwdt_pragma) are set such that software starting and stopping of the watchdog timer is disabled, calling the MPLWDT_START macro will arm the watchdog timer.

EXAMPLE

```c
// Initialize the watchdog by setting a 1 second timer value
MPLWDT_INIT(MPLWDT_TMR_1S);

// Set a window of 75%
MPLWDT_WINDOW(MPLWDT_WIN_75);

// Arm and start the watchdog timer
MPLWDT_START;

// .... Make sure to clear only after 250 msec

// Clear the watchdog timer
MPLWDT_CLEAR;

// Stop the watchdog timer
MPLWDT_STOP;

```
