# a3cw135-devkit-oobe-legacy-baseline

This is the baseline Golden Hardware Reference Design (GHRD) for Agilex 3 FPGA and SoC C-Series Development Kit.

## Description

Agilex 3 GHRD is a reference design for Intel Agilex 3 System On Chip (SoC) FPGA.

The GHRD is part of the Golden System Reference Design (GSRD), which provides a complete solution, including exercising soft IP in the fabric, booting to U-Boot, then Linux, and running sample Linux applications.
Refer to the [Altera FPGA Developer Site](https://altera-fpga.github.io/latest/ed-demo-list/ed-list/) for information about GSRD.

The design uses HPS First configuration mode.

## Baseline feature
This reference design demonstrates the following system integration between Hard Processor System (HPS) and FPGA IPs:
- Hard Processor System (HPS) enablement and configuration
  - Enable dual core Arm Cortex-A55 processor
  - HPS Peripheral and I/O (SD/MMC, EMAC, MDIO, USB, I3C, JTAG, UART, and GPIO)
  - HPS Clock and Reset
  - HPS FPGA Bridge and Interrupt
- HPS EMIF configuration (Inline ECC for LPDDR4 is enabled by default)
- System integration with FPGA IPs
  - Peripheral subsystem that consists of System ID, Programmable I/O (PIO) IP for controlling PushButton and LEDs.
  - Debug subsystem that consists of JTAG-to-Avalon Master IP to allow System-Console debug activity and FPGA content access through JTAG
  - 256KB of FPGA On-Chip Memory

## Project Details

- **Family**: Agilex 3
- **Quartus Version**: 25.1.1
- **Development Kit**: Agilex 3 FPGA and SoC C-Series Development Kit DK-A3W135BM16AEA
- **Device Part**: A3CW135BM16AE6S
- **Category**: Golden Hardware Reference Design (GHRD)
- **Source**: Quartus Prime Pro
- **URL**: https://www.github.com/altera-fpga/agilex3c-ed-gsrd
- **Design Package**: a3cw135-devkit-oobe-legacy-baseline.zip
- **Documentations**: Link TBD - Refer to this README.
## GHRD Overview
![GHRD_overview](/images/agilex3_ghrd_overview.svg)

## Hard Processor System (HPS)
The GHRD HPS configuration matches the board schematic.
Refer to [Hard Processor System Technical Reference Manual: Agilex 3 SoCs](https://www.intel.com/content/www/us/en/docs/programmable/848530/current) and [Hard Processor System Component Reference Manual: Agilex 3 SoCs](https://www.intel.com/content/www/us/en/docs/programmable/851703/current) for more information on HPS configuration.

## HPS External Memory Interfaces (EMIF)
The GHRD HPS EMIF configuration matches the board schematic.
Refer to [External Memory Interfaces (EMIF) IP User Guide: Agilex 3 FPGAs and SoCs](https://www.intel.com/content/www/us/en/docs/programmable/847458/current) for more information on HPS EMIF configuration.

## Bridges
Bridges are used to move data between FPGA fabric and HPS logic.
Refer to [HPS Bridges](https://www.intel.com/content/www/us/en/docs/programmable/851703/current/hps-fpga-bridges.html)

The HPS address map and the FPGA address map are the same for Agilex 3. Refer to [Total Address Map Graphical](https://www.intel.com/content/www/us/en/docs/programmable/848530/current/total-address-map-graphical.html) for more information.

Therefore, when accessing HPS logic in uboot or linux, the base address would be the same as, when using [Debug Subsystem](#Debug-Subsystem) from FPGA fabric.

| Bridge   | Use Case |
| :-- | :-- |
| F2SDRAM  | move data from FPGA fabric to HPS logic (non-coherent). Can access HPS EMIF. |
| LWH2F    | move data from HPS logic to FPGA fabric. Access FPGA peripherial Control Status Register (CSR). |
| H2F      | move data from HPS logic to FPGA fabric. Access FPGA Onchip Memory as scratch pad.    |

## Debug Subsystem
The GHRD JTAG master interfaces allows you to access peripherals in the FPGA with System Console, through the JTAG master module. This access does not rely on HPS software drivers.

Refer to this [Guide](https://www.intel.com/content/www/us/en/docs/programmable/683819/current/analyzing-and-debugging-designs-with-84752.html) for information about system console.

## Peripherial subsystem
| Peripheral | Address Offset | Size (bytes) | Attribute | Interrupt Number
| :-- | :-- | :-- | :-- | :-- |
| sysid | 0x0001_0000 | 8 | Unique system ID   | None |
| led_pio | 0x0001_0080 | 16 | LED outputs   | None |
| button_pio | 0x0001_0060 | 16 | Push button inputs | 1 |

### Notes
- There are 1 user push-button inputs and 2 LED outputs that is connected to fpga pins on Agilex 3 FPGA and SoC C-Series Development Kit.
  -  Only the lower bit of LED outputs are available for software to control. The most significant bit of the LED is used in GHRD top module as heartbeat led. This LED blinks when the fpga design is loaded. Users will not be able to control this LED with HPS software, for example U-Boot or Linux.
- The peripherial is accessed via the LWH2F bridge and have offset of 0x0_2000_0000. Refer to [Total Address Map Graphicals](https://www.intel.com/content/www/us/en/docs/programmable/848530/current/total-address-map-graphical.html) for more information.
- The FPGA IRQ has offset of 17 when mapped to Generic Interrupt Controller (GIC) in device tree structure(dts). Refer to [GIC Shared Peripheral Interrupts Map for the SoC HPS](https://www.intel.com/content/www/us/en/docs/programmable/848530/current/gic-shared-peripheral-interrupts-map.html)

## Binaries location
After build, the bitstream (sof) can be found in output_files folder.
