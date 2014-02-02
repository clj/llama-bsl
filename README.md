TI CC2538 Serial Boot Loader
============================

This folder contains a python script that communicates with the boot loader of the Texas Instruments CC2538 SoC (System on Chip).
It can be used to erase, program and verify the flash of the CC2538 with a simple USB to serial converter.

###Requirements

To run this script you need a Python interpreter, Linux and Mac users should be fine, Windows users have a look here: [Python Download][python].

To communicate with the uart port of the CC2538 SoC you need a usb to serial converter.
* If you use the SmartRF06 board (CC2538DK) you can use the on board ftdi chip. Make sure the "Enable UART" jumper is set on the board. See the cc2538dk platform folder for more info on drivers for this chip on different operating systems.
* If you use a different platform, there are many cheap USB to UART converters available, but make sure you use one with 3.3v voltage levels.

###Boot Loader Backdoor

Once you connected the SoC you need to make sure the serial boot loader is enabled. A chip without a valid image (program), as it comes from the factory, will automatically start the boot loader. Once you upload an image to the chip, the "Image Valid" bits are set to 0 to indicate that a valid image is present in flash. On the next reset the boot loader won't be started and the image is immediately executed.
To make sure you don't get "locked out", i.e. not being able to communicate over serial with the boot loader in the SoC anymore, you need to enable the boot loader backdoor in your image (this is not checked by the current version of the script yet). When the boot loader backdoor is enabled the boot loader will be started when the chip is reset and a specific pin of the SoC is pulled high or low (configurable).
The boot loader backdoor can be enabled and configured with the 8-bit boot loader backdoor field in the CCA area in flash. If you set this field to 0xF3FFFFFF the bootloader will be enabled when pin PA3 is pulled low during boot. This translates to holding down the `select` button on the SmartRF06 board while pushing the EM reset button.
If you did lock yourself out or there is already an image flashed on your SoC, you will need a jtag programmer to erase the image. This will reset the image valid bits and enable the boot loader on the next reset. The SmartRF06EB contains both a jtag programmer and a USB - uart converter.

###Usage

The script will automatically select the first serial looking port from a USB to uart converter in `/dev` (OSX, Linux), Windows needs testing.

Before uploading your image start the boot loader on the SoC (Select + Reset on CC2538DK).
If you want to use the script stand-alone you can use `python cc2538-bsl.py -h` for help.

###Remarks

* Currently the CC2538-bsl doesnt check if the boot loader backdoor is enabled in the image it is about to flash.
* Programming multiple SoCs at the same time is not yet supported.
* Reading from the SoC is not implemented yet.
* Automatic detection of the serial port needs testing, especially on Windows.

[python]: http://www.python.org/download/ "Python Download"