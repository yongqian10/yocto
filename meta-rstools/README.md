# meta-rstools

`meta-rstools` is a Layer for the Yocto project to add a simple way for fully accessing the FPGA Manager of the Intel (ALTERA) **Cyclone V SoC-FPGA (SoCFPGA)** and **Intel Arria 10 SX SoC-FPGA**.

With a single command it is possible to change FPGA configuration or to read and write the *ARM AXI-Bridge* to the FPGA fabric. 
The layer is a part of [*rsYocto*](https://github.com/robseb/rsyocto)

<br>

![Alt text](FPGAConfigurationAction.gif?raw=true "Write FPGA Configuration")

**Write the FPGA configuration with a single Linux command** 

___
## Features 

* **The layer adds following features to the final Linux command set:**
  *  **FPGA Status**
      * Reading the Status of the FPGA fabric 
      ````bash
      FPGA-status 
      ````
      * Help- and Info- Output with the suffix `-h` 
        ````bash
          Command to read current Status mode of the FPGA fabric
          FPGA-status
                  read the status with detailed output
          FPGA-status -d
                  POWER UP:        0
                  RESET:           1
                  CONFIG:          2
                  INIT:            3
                  USER:            4
                  UNKNOWN:         5
        ````
  * **FPGA Configuration Mode / MSEL** 
    * Reading the Configuration mode of the FPGA (selected with the MSEL-Bit Switch)
        ````bash
        FPGA-status
        ````
     * Help- and Info- Output with the suffix `-h`  
          ````bash
          Command to read the selected FPGA configuration mode.
          This is set with the MSEL-Switch.
          FPGA-readMSEL
                  read MSEL with detailed output
          FPGA-readMSEL -d
                  read MSEL as decimal value
          ````
 * **FPGA Fabric Reset** 
    * Resetting the FPGA fabric (remove the running configuration)
        ````bash
        FPGA-resetFabric
        ````
     * Help- and Info- Output with the suffix `-h`  
          ````bash
          Command to reset the FPGA fabic
          A reset clears the current configuration
          of the FPGA fabric
          After a reset is the FPGA in the RESET Mode
          FPGA-resetFabric -r
                  reset the FPGA with detailed output
          FPGA-status -d
                  read the status as decimal value
                  0 => ERROR: Resting of the FPGA failed!
                  1 => Success: FPGA config is deleted
          ````
  * **Write FPGA Configuration** 
    * Writing a new FPGA configuration
        ````bash
        FPGA-writeConfig
        ````
     * Help- and Info- Output with the suffix `-h`  
          ````bash
            Command to change the FPGA fabric configuration
            for Cyclone V use following RBF (.rbf) Config File Setings
            MSEL=00100: PP16 with no AES and no Data compression
            MSEL=00101: PP16 with AES and no Data compression
            FPGA-writeConfig -f [config rbf file path] {-b [optinal]}
                    change the FPGA config with a selected .rbf file
            FPGA-writeConfig -r {-b [optinal]}
                    restore to the boot up FPGA configuration
                    this Conf File is located: /usr/rsyocto/running_bootloader_fpgaconfig.rbf
                    suffix: -b -> only decimal result output
                                                    Error:  0
                                                    Succses:1
          ````
      * Required MSEL-Bit Switch Selection to allow Linux to change the FPGA configuration:
        * `MSEL= 00100`: Passive parallel x16 with no AES and Data compression
        * `MSEL= 00101`: Passive parallel x16  with AES and Data compression

 * **AVALON/AXI Bridge Read** 
    * Reading a Address (32 Bit register) of the HPS-to-FPGA- or Lightweight-HPS-to-FPGA Bridge interface
        ````bash
        FPGA-readBridge
        ````
     * Help- and Info- Output with the suffix `-h`  
        ````bash
          read a register on the Lightweight HPS-FPGA Brige
                e.g.: FPGA-readBridge -lw 0a

          FPGA-readBridge -hf [offset module address hex]
                read a register on the HPS to FPGA AXI Bridge
                e.g.: FPGA-readBridge -lw 0a

                suffix: -b -> only decimal result output
         ````
* **AVALON/AXI Bridge Write** 
    * Writing a 32 Bit Value to the HPS-to-FPGA- or Lightweight-HPS-to-FPGA Bridge interface
        ````bash
        FPGA-writeBridge
        ````
     * Help- and Info- Output with the suffix `-h`  
        ````bash
          Command write to address of a HPS-FPGA Bridge
          address
          FPGA-readBridge -lw [offset module address hex]
                  read the register on the Lightweight HPS-FPGA Brige
                  e.g.: FPGA-writeBridge -lw 0a

          FPGA-writeBridge -hf [offset module address hex] [value dec]
                  write to the HPS to FPGA AXI Bridge Interface with a dec values
                  e.g.: FPGA-writeBridge -hf 0a 255
          FPGA-writeBridge -hf [offset module address hex] -h [value hex]
                  write to the HPS to FPGA AXI Bridge Interface with a hex values
                  e.g.: FPGA-writeBridge -hf 0a -h ff
          FPGA-writeBridge -hf [offset module address hex] -b [Bit pos] [value]
                  Set or Reset a Bit on HPS to FPGA AXI Bridge register
                  e.g.: FPGA-writeBridge -hf 0a -b 8 0

                  suffix: -b -> only decimal result output
                                                  Error:  0
                                                  Succses:1
         ````
* **GPI Register Read** 
    * Reading the 32 Bit direct access Register (written by the FPGA)
        ````bash
        FPGA-gpiRead
        ````
     * Help- and Info- Output with the suffix `-h`  
        ````bash
             Command to read general-purpose input signals (gpi) from
             the FPGA fabric.
             The FPGA can set the 32 Bit gpi (input) register
              FPGA-gpiRead
                      read gpi with detailed output
              FPGA-gpiRead -d
                      read gpi as decimal value
         ````
* **GPO Register Write** 
    * Writing the 32 Bit direct access Register to the FPGA
        ````bash
        FPGA-gpoWrite
        ````
     * Help- and Info- Output with the suffix `-h`  
        ````bash
               Command to write the general-purpose output signals (gpo) to
              the FPGA fabric
              The HPS get write a 32 Bit register to the FPGA
              FPGA-gpoWrite -h [uint]
                      writing the whole 32 Bit gpo as HEX
                      e.g.: FPGA-gpoWrite -h ffffffff
              FPGA-gpoWrite -d [uint]
                      writing the whole 32 Bit gpo as DEC
                      e.g.: FPGA-gpoWrite -d 12345
              FPGA-gpoWrite -b [bit pos] [value]
                      writing a specific Bit position
                      e.g.: FPGA-gpoWrite -b 16 1
                                (to set the Bit 16 of the gpo Regiser)
         ````
 ### For more informations and examples about the usage of these commands please follow the [*rsYocto*](https://github.com/robseb/rsyocto) documentation. 
 <br>
         
## Add the layer to the Yocto Project   
* Following steps are required to add the *meta-rstool* layer to the Yocto project
  * Be sure, that the HPS Bridges are enabled before Linux boots
    * To realise that enable the Bridges with following u-boot boot-script command
        ````bash
          echo --- enable HPS-to-FPGA, FPGA-to-HPS, LWHPS-to-FPGA bridges ---
          bridge enable;
        ````
  1. Check out this repository in your `poky`-folder
       ````bash
          git clone https://github.com/robseb/meta-rstools.git
       ````
  2. Include the layer to Yocto
    * Open `poky/build/conf/bblayers.conf` and add following snippet to the `bblayers.conf` file: 
       ````
          /home/<user name>/poky/meta-rstools \
       ````
  3. Include the rstools-command to the Yocto Linux
    * Open `poky/build/conf/local.conf` and add following snippet to the `conf.local` file: 
      * For Intel Cyclone V Soc-FPGAs:
        ````
         IMAGE_INSTALL_append += "mselfpgacy5 readbridgesfpgacy5 readfgpipgacy5 resetfabricfpgacy5 statusfpgacy5 writebridgefpgacy5 writeconfigfpgacy5 writegpofpgacy5 "
        ````
      * For the Intel Arria 10 SX :
        ````
        IMAGE_INSTALL_append = " mselfpgaa10 readbridgesfpgaa10 readfgpipga10  statusfpgaa10 writebridgefpgaa10  "
        ````
  4.  Use bitbake to build the Linux (**done**)

## Source Code 
The source code of this layer is available in my [*rstoolsCY5*](https://github.com/robseb/rstools) and [*rstoolsA10*](https://github.com/robseb/rstoolsA10)  repository.


<br>

# Author
* **Robin Sebastian**


*meta-rstools* and *rstools* are projects, that I have fully developed on my own. No companies are involved in this projects.
Today I'm a Master Student of electronic engineering with the major embedded systems. 

[![Gitter](https://badges.gitter.im/rsyocto/community.svg)](https://gitter.im/rsyocto/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)
[![Email me!](https://img.shields.io/badge/Ask%20me-anything-1abc9c.svg)](mailto:git@robseb.de)
