# ChickenBoot

The unbrickable and versatile embeddable boot loader  

<img src="doc/chickenboot-logo-full.png" height="480">  

# Building

To build the firmware:  
```
export PICO_SDK_PATH=$HOME/pico/pico-sdk

cd firmware
cmake -S . -B build -DPICO_BOARD=pico
make -C build -j
```

Then copy the build/main.uf2 file to the virtual drive (created by holding the BOOT button, the usual label for the drive is RPI-RP2)
# PCB design files

There are two board designs: one based on a MCU from WCH, the other based on a Raspberry Pi Pico.  


for details, read [Design document](DESIGN.md)


## View the boards online:  
The interactive viewer allows to explore the design in full without the need of installing any tools

- [RPI based design](https://kicanvas.org/?repo=https%3A%2F%2Fgithub.com%2Fsuarezvictor%2Fchickenboot%2Ftree%2Fmain%2Fhardware%2Fchickenboot-rpi)

- [WCH based design](https://kicanvas.org/?repo=https%3A%2F%2Fgithub.com%2Fsuarezvictor%2Fchickenboot%2Ftree%2Fmain%2Fhardware%2Fchickenboot-jtag-wch)

<img src="doc/chickenboot-rpi.png" width="640">  
<img src="doc/chickenboot-wch.png" width="640">  

# Licensing
Hardware design files are licensed under the [CERN-OHL-S-2.0](https://opensource.org/license/CERN-OHL-S-2.0) unless otherwise stated.  
Source code is licensed under the [AGPL-3.0](https://opensource.org/license/agpl-3-0) unless otherwise stated.  
  
# Funding
This project is supported by [The Open Source Firmware Foundation](https://www.osfw.foundation/)  
