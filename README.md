<div style="text-align: right">
    <img width="90%" src="./resources/2-edited.jpeg"/>    
</div>
<img width="20%" src="./resources/1-edited.jpeg" align="right"/>
[![Build Status](https://jenkins.dragomirecky.com/job/swift-embedded/job/swift/badge/icon)](https://jenkins.dragomirecky.com/job/swift-embedded/job/swift/)


This project aims to bring Swift to the world of embedded systems and IoT. It enables Swift to be used on microcontrollers with no operating system and with minimal resources available.

 

### What version of Swift does it support?
   *Swift 5.1*... yes, enjoy the latest features of Swift on bare metal! 🚀

### Are all features of Swift available?

Yes, except for full unicode support. In order to save some memory, it includes minimal support only – you can use unicode characters in your strings, but they are ignored by operations like `.uppercased()` (`"žluťoučký".uppercased()` returns `"žLUťOUčKý"` instead of `"ŽLUŤOUČKÝ"`).

### Any limitations?

Code size. A "hello world" application has a little bit over one megabyte because it includes big part of the Swift standard library. However, it is a fixed cost (does not grow proportionally with your program) and you can fit quite a lot on a microcontroller with 2MB of flash!

### What boards are supported?

Short answer: [NUCLEO-F439ZI](https://www.st.com/en/evaluation-tools/nucleo-f439zi.html), but adding support for any other STM32F4-based board with 2MB of flash memory should require few lines of code only.

[The toolchain](https://github.com/swift-embedded/swift) itself should be able to target any microcontroller using thumbv7-m or thumbv7-em architecture. However, any practical embedded application is going to require a package providing access to hardware peripherals. I am currently focusing on supporting the STM32F4 family of microcontrollers – the [stm32](https://github.com/swift-embedded/stm32) Swift package provides access to the basic hardware peripherals of those microcontrollers, such as UART, SPI or GPIO.

Also, to make building an embedded application as simple as possible, I have created a small [cross](https://github.com/swift-embedded/cross) command-line utility. It is a wrapper around `swift build` that handles all the things as setting up a linker script or using the right compiler flags, making compiling an app a simple one-liner: `swift run cross build`.

-----

> This project is in an early phase, and there is still a lot to work on. Also, if you want to know more about the process of porting Swift to embedded systems, feel free to check out my thesis [Swift for Embedded Systems](https://dspace.cvut.cz/bitstream/handle/10467/82498/F8-DP-2019-Dragomirecky-Alan-thesis.pdf).


## Getting Started

### Installing the toolchain

1. Download the latest build of the toolchain from [here](https://jenkins.dragomirecky.com/job/swift-embedded/job/swift) put the .xctoolchain file to one of `/Library/Developer/Toolchains/` or `~/Library/Developer/Toolchains/`:

    ```bash
    $ mkdir -p /Library/Developer/Toolchains
    $ mv <downloaded file>.xctoolchain ~/Library/Developer/Toolchains
    ```

2. Activate the toolchain with:

    ```bash
    export TOOLCHAINS=baremetal.YYYYMMDD
    ```

3. Check that `swift` and other command-line utilities now reference the newly downloaded toolchain.

    ```bash
    $ xcrun -f swift
    /Users/alandragomirecky/Library/Developer/Toolchains/swift-LOCAL-2019-12-10-a.xctoolchain/usr/bin/swift
    ```
    
    > You have to have Xcode installed. Otherwise, `xcrun` won't find the toolchain.
    
### Running an example

1. Checkout this repository:

    ```bash
    $ git clone https://github.com/swift-embedded/swift-embedded
    ```
    
2. Go to a directory with some example app and compile it:
    
    ```bash
    $ cd swift-embedded/examples/Blinky
    $ swift run cross build
    ```
    
3. Flash and run the application. One option is using the `openocd` (`brew install openocd`):

    In one terminal, run openocd (it connects to your board and starts a gdb server)
    
    ```bash
    $ openocd -f board/st_nucleo_f4.cfg
    ```
    
    And in second, load your application to the board and start it:

    ```bash
    $ xcrun arm-none-eabi-gdb .build/debug/Blinky -ex 'tar ext :3333' -ex 'load'
    ```