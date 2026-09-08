#Design criteria
## Objective
The current objective of the project aims to provide a practically negligible-cost, self-recovering, autonomous FPGA/MCU programming subsystem that can be embedded into embedded products.

# MCU selection

## Hard requirements
* Unbrickable: the MCU includes a bootloader in ROM
* No need of external adapters: the ROM bootloader suppoprts USB
* Small size: to prevent adding "real estate" requirements in PCB. Targew is 7x7mm max.
* Very low cost: if cost were high, it would justify an external programmer. Target is $2 max.

## Desirable features
* Large internal flash: So it can replace the flash required by FPGAs or complex SoC
* No radio: the radio of some MCUs adds a compliance burden for marketable designs.
* No BGA package: to simplify PCB and reduce cost

After evaluating those requirements, the *RP2354A* MCU appears to be a unicorn device, since the only capable of satisfying all of the above: 7x7mm package and 2 MB flash, at just $1.2.
Notable alternatives in a small package are ESP32 and Bouffalo Labs MCUs, being small, cheap and with considerable flash. but they have the radio issue, or Artery AT32F435CMU7, but about $3 and less mature ecosystem.
Others from WCH like CH32V203F8U6 and CH32X035F8U6 comes in 3x3mm package and at < $0.5 cost, but it lacks a large internal flash.

There is an issue regarding using just 2 MB of flash since some FPGAs expect up to 64MB for bistestream storage. But it is solvable by usign an external flash with a RP2040/RP2350 MCU, or by compression (tested bitstreams are about 4X compressible with lightweight algorithms like LZ4 which is truly fast for streaming decompression).

Note that the RP2350 has a internal 1.1V DC-DC requiring a 2x1.6mm inductor (not needed by the RP2040 LDO) but this can be skipped since the core voltage of the MCU matches the core voltage of FPGAs and the MCU is known for its flexibility of voltage and frequency of operation, so the same supply can be used saving PCB area.

# WCH board option

A board based on the CH32X035F8U6 MCU was also designed paired with an external ISSI IS25WP016D-JULE-TR 16mbit flash (2x3mm), considering a WINBOND W25Q16JVUXIQ alternative but more expensive.

Combined area usage gets effectively 6.5x3mm, and price stays below $1, but it is expected that the firmware for this platform will be more complex (TinyUSB support is not as good) and also slower: its 48Mhz clock is significantly slower than the 150Mhz (overclockable) RP2354 which has a full featured PIO. The CH32X035 provides a PIOC but with restrictions on pin usage (not possible in the 20 pin version) and DMA clobbers unintended I/O pins.

# Firmware
## Desirable features
* Device agnostic: it would add much complexity if each device needs its custom algorithm 
* Firmware simplicity: restricting the capability of this firmware to upload the bitstream (or program) just to RAM eliminates the unpredictable timings of flash access. Lack of flash programming is not an issue since the selected MCU will store the bitstream of firmware to be run, so the device doesn't need to fetch it from a local flash.
* Ease of use at production by non-technical users: UF2 uploader imposeses *no drivers or tools installation on the host (PC), since the programmer shows as a "thumb" driver where the programming file can be copied.

Considering this, the format to store the programming data was XSVF: most FPGA tools generate SVF (ASCII format), but this is convertible to XSVF (binary format) by tools like OpenOCD's svf2xsvf. The firmware on the MCU need only to convert the XSVF commands to JTAG signals, and since the XSVF is generated once at the host, this makes it device agnostic (the host knows the nuances of each device to be programmed). Note that SVF files generally lacks polling commands to check "busy" bits, but those are really needed for programming flash devices, SRAM target can use worst-case timings since they're really small (a few clocks). In any case, XSVF supports wait loops that can be exploited for programming more complex devices.

