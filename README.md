# dap42
The dap42 project is an open-source firmware and [hardware design](https://github.com/devanlai/dap42-hardware) for an inexpensive, minimalist CMSIS-DAP based debug probe based on the [STM32F042F6](http://www.st.com/web/catalog/mmc/FM141/SC1169/SS1574/LN1823/PF259617) in a TSSOP-20 package.

This project is currently in alpha - features generally do 80% of what's needed, but the last 20% is generally untested.

## Current features
### Firmware
* [Serial Wire Debug](http://www.arm.com/products/system-ip/debug-trace/coresight-soc-components/serial-wire-debug.php) (SWD) access over [CMSIS-DAP 1.0](http://www.arm.com/products/processors/cortex-m/cortex-microcontroller-software-interface-standard.php) HID interface (currently only works with [OpenOCD](http://openocd.org))
* CDC-ACM USB-serial bridge
* [Device Firmware Upgrade](http://www.usb.org/developers/docs/devclass_docs/DFU_1.1.pdf) (DFU) over USB (detach-only, switches to on-chip [DFuSe](http://dfu-util.sourceforge.net/dfuse.html) bootloader).

## Flash instructions
In general, the firmware can be uploaded over USB DFU without any extra hardware:
* Unflashed STM32F04x chips always start in the DFU bootloader.
* The bootloader button can be used to force the chip to start from the bootloader on reset (unless disabled)
* dap42 firmware supports the DFU_DETACH request to switch to the bootloader
* When dap42 firmware is reset by the watchdog, it enables the bootloader to ensure that firmware that consistently hard-faults or hangs can always upload new firmware.

The default method to upload new firmware is via [dfu-util](http://dfu-util.sourceforge.net/). The Makefile includes the `dfuse-flash` target to invoke dfu-util. dfu-util automatically detaches the dap42 firmware and uploads the firmware through the on-chip bootloader.

## Usage
The dap42 firmware has been tested with gdb and OpenOCD on STM32F042 (of course), STM32F103, and LPC11C14 targets.

For OpenOCD to configure and use the dap42, the CMSIS-DAP interface must be selected and the dap42's VID/PID pair must be specified.
The dap42 VID/PID pair is from [pid.codes](http://pid.codes/): [1209/DA42](http://pid.codes/1209/DA42/).

An example of the extra configuration for OpenOCD is given below:

    interface cmsis-dap
    cmsis_dap_vid_pid 0x1209 0xDA42

## Planned features
### Firmware
* CMSIS-DAP 1.10 support
 * Command queueing (command level, not packet level)
 * [Serial Wire Output](http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.ddi0314h/Chdfgefg.html) (SWO) trace support
* [Serial Line CAN](http://lxr.free-electrons.com/source/drivers/net/can/slcan.c) (SLCAN) interface - Silent mode, RX only.
* [Media Transfer Protocol](https://en.wikipedia.org/wiki/Media_Transfer_Protocol) (MTP) interface or [Mass Storage Device](https://en.wikipedia.org/wiki/USB_mass_storage_device_class) (MSD) interface for drag-n-drop target firmware flashing

### Hardware
* Simultaneous USB-serial bridge and SWO trace using an STM32F042K6 in an LQFP-32 package with both UARTs pinned out.
* ESD protection
* Level-shifting/protective tri-state buffer between the probe and target SWD port.

## Licensing
All contents of the dap42 project are licensed under terms that are compatible with the terms of the GNU Lesser General Public License version 3.

Non-libopencm3 related portions of the dap42 project are licensed under the less restrictive ISC license, except where otherwise specified in the headers of specific files.

See the LICENSE file for full details.