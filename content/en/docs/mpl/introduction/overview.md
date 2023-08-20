---
author: Joost Mans
title: MPL
date: 2023-08-20
lastmod: 2023-08-20
Description: Overview of MPL PIC library.
thumbnail:
    url: /img/mpl.webp
    author: Google DeepMind
    authorURL: https://unsplash.com/@googledeepmind
    origin: Unsplash
    originURL: https://unsplash.com/photos/mbq0qL3ynMs
icon: fas book
tags: ["microchip", "pic", "library", "8-bit", "documentation"]
layout: docs
aliases:
  - "/docs/mpl/introduction/overview"
  - "/docs/mpl/introduction/"
  - "/docs/mpl/"
_build:
  list: always
draft: true
--- 
<!-- cSpell:ignore joost lastmod webp mimage -->  

MPL is a library for a subset of Microchip's 8-bit PIC controllers. It provides macros that remove the differences between different families with respect to accessing the various bits in the controller's registers. Sometimes registers and/or the bits in those registers are named slightly different between families. This library provides a way to make accessing functionality independent of the used family, as long as the microchip device supports the functionality.  
MPL also offers a more generic approach to using the functionality, consisting of initializing, starting and stopping individual modules, and enabling and disabling interrupts.

The goal of MPL is to support the more modern 8-bit PIC controllers as listed in the below image. However currently only the following families are supported:

- PIC16(L)F153xx
- PIC16(L)F183xx

{{< mimage src="supported.webp" caption="Overview of the controller families that are intended to be supported" text="text-italic caption-align-right" >}}

Each of the controllers has a set of modules that are supported by that controller and even multiple instanced of a module. The maximum set of modules are listed below. The 3-letter or 4-letter abbreviation that is used in MPL for a module is provided as well.

{{< mimage src="modules.png" caption="Overview of the modules and their MPL abbreviation" outer="image-center" text="text-italic caption-align-right" >}}

Detailed information of MPL can be found in the menu to the left.
