---
layout: post
title: Introducing PDPjs, a PDP-11 Emulator
date: 2016-10-06 15:00:00
permalink: /blog/2016/10/06/
machines:
  - id: test1170
    type: pdp11
    debugger: true
    config: /devices/pdp11/machine/1170/vt100/debugger/machine.xml
    connection: dl11->vt100.serialPort
  - id: vt100
    type: pc8080
    config: /devices/pc8080/machine/vt100/machine.xml
    connection: serialPort->test1170.dl11
---

[PDPjs](/devices/pdp11/machine/) is the newest addition to the PCjs family of emulators, joining PCx86, PC8080, and C1Pjs.

While PDPjs may eventually support a range of DEC PDP machines, my current focus is on the PDP-11, starting with the
PDP-11/70.  From there, I'll work backwards to support other PDP-11 models, such as the PDP-11/45, until I reach the
beginning of the PDP-11 line: the PDP-11/20.

I'm starting with the top-of-the-line PDP-11/70 largely because the core of the emulator is being adapted from the
JavaScript [PDP 11/70 Emulator (v1.3)](http://skn.noip.me/pdp11/pdp11.html) written by
[Paul Nankervis](mailto:paulnank@hotmail.com), who has generously given permission to use his code in PCjs.
Since his emulator is a fully functional 11/70, it made sense to start there and work backwards, neutering features
as needed.

The code has already undergone a lot of refactoring. Opcodes are now decoded by function tables rather than a single
switch statement, and every opcode is implemented with a discrete function.  Other refactoring includes flag management,
interrupt management, and device management.

Most of the work remaining is in device management.  Like other PCjs emulators, PDPjs has a Bus component,
[bus.js](/modules/pdp11/lib/bus.js), that allows separate device components to register I/O handlers for specific
UNIBUS addresses.  During the initial port, I moved all of Paul's original device management code into one "catch-all"
component, [device.js](/modules/pdp11/lib/device.js), which has now been converted to the new I/O registration model.

The first new device component is [serialport.js](/modules/pdp11/lib/serialport.js), which is currently the
only means PDPjs has of communicating with the outside world.  So you can try
[PDPjs connected to a VT100 Terminal](/devices/pdp11/machine/1170/vt100/), by clicking the "Run" button on the test machine.
The test machine is running [custom boot code](/apps/pdp11/boot/test/), adapted from boot code written by Paul, but due to the
lack of other device support, nothing can be booted yet.

Obviously PDPjs is very much a work-in-progress.  Before I proceed much farther, I really want to put the CPU through
some rigorous testing, so I'll be on the lookout for some comprehensive PDP-11 instruction tests.  Or I'll write my own,
and compare results across 1 or 2 other PDP-11 emulators.

{% include machine.html id="test1170" %}

{% include machine.html id="vt100" %}

*[@jeffpar](http://twitter.com/jeffpar)*  
*Oct 6, 2016*