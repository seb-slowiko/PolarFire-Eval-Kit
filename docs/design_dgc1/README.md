## Mi-V Extended Subsystem Design Guide Configuration 1: SPI Write & Boot
This folder contains Tcl scripts that build Libero SoC v2023.1 MIV_ESS DGC1 design project for the PolarFire Eval Kit. The script is executed in Libero SoC to generate the sample design. 
> This design is compatible with Libero SoC v2023.1. Using older versions of Libero SoC will result in errors.

#### PF_Eval_Kit_MIV_RV32_BaseDesign

| Config  | Description |
| :------:|:----------------------------------------|
| DGC1    | This design uses the **MIV_RV32** core configured as follows: <ul><li>RISC-V Extensions: IMC</li><li>Multiplier: MACC (Pipelined)</li><li>Interfaces: AHBL Initiator (mirrored), APB3 Initiator</li><li>Reset Vector Address: 0x4000_0000</li><li>Internal IRQs: 6</li><li>TCM: Enabled</li><li>TCM APB Target (TAS): Enabled</li><li>System Timer: Internal MTIME enabled, Internal MTIME IRQ enabled</li><li>Debug: enabled</li></ul>This design uses the **MIV_ESS** core configured as follows: <ul><li>Bootstrap: Enabled</li><li>Bootstrap Source: SPI</li><li>uDMA: Disabled</li><li>GPIO: Enabled, 2 GPIO_IN and 4 GPIO_OUT (fixed config)</li><li>I2C: Disabled</li><li>PLIC: Disabled</li><li>SPI: Disabled</li><li>Timer: Disabled</li><li>UART: Enabled</li><li>Watchdog: Disabled</li></ul>|

> This design configuration is only available for the PolarFire Eval Kit (Revision D with production silicon devices).


## <a name="quick"></a> Instructions

#### Running Libero SoC in GUI mode, with Script Arguments
    1. Open Libero SoC
    2. Execute the selected script, Project -> Execute Script
    3. Select the directory that the script is located in, using the "..."
    4. Select the script and select "Open"
    5. In the arguments text box, enter "DGC1 SYNTHESIZE PS"
    6. Select the "Run" button to execute the script
    7. Once complete, a script report will be generated.

In this example, the arguments "DGC1 SYNTHESIZE PS" are entered to take the production silicon (PS) die project through to Synthesis.

Libero executes the script and opens the Mi-V sample project targeted for a production silicon (PS) die. The script adds Timing constraints to the project for Synthesis, Place and Route, and Timing Verification. Additionally, I/O Constraints are added to the project for Place and Route. The project can now be taken through the remainder of the Libero SoC design flow.

## <a name="Script arguments"></a> Script Arguments
The complete set of script arguments are documented here.

#### First argument:
| Argument                  |  Description   |
| ------------------------- |:---------------|
| DGC1                      | Generate a MIV_ESS example design from the *MIV_ESS v2.0 Design Guide* (accessible from the Libero catalog)  |


#### Second argument:
| Argument                  |  Description   |
| ------------------------- |:---------------|
| SYNTHESIZE                | Run synthesis on the design  |
| PLACE_AND_ROUTE           | Run place and route on the design  |
| GENERATE_BITSTREAM        | Generate the bitstream for the design|
| EXPORT_PROGRAMMING_FILE   | Export the programming file (.job) |


#### Third argument:
| Argument                  |  Description   |
| ------------------------- |:---------------|
| PS | Build a base design targeted for 'PS' die |
| ES | Build a base design targeted for 'ES' die |


## <a name="Software Provided"></a> Software Provided
There are two programs included with this configuration:
* **miv-rv32i-systick-blinky.hex**: A Hex program configured to run from TCM's address (0x4000_0000). The program is initialized in the LSRAM component at 0x8000_0000 and it is accessible over the AHB interface.

    > The example hex program was created using  miv-rv32i-systick-blinky in release mode (miv32i-Release). For more information about the project go to bare metal example: [miv-rv32i-systick-blinky](https://mi-v-ecosystem.github.io/_redirects/mi-v-soft-risc-v/miv-rv32i-systick-blinky)
* **miv-rv32-ess-bootloader.elf**: The supplied Bootloader .elf file is used to copy data from the LSRAM (SRC_MEM) @0x8000_0000 to external SPI Flash memory on PolarFire Eval Kit board

    > The .elf program was compiled using 'miv-rv32-ess-bootloader' in Bootstrap mode. For more information about the project go to bare metal example: [miv-rv32-ess-bootloader](https://mi-v-ecosystem.github.io/_redirects/mi-v-soft-risc-v/miv-rv32-bootloader)

#### Running a Bootloader .elf program for DGC1
The provided program, *miv-rv32-ess-bootloader.elf* , is available in the Libero project folder after the *create_project* .tcl script has been run for the Design Configuration 1 (DGC1). The program can be used to transfer a program stored in LSRAM to an external SPI Flash. The MIV_ESS can then copy the code to the MIV_RV32 Tightly Coupled Memory (TCM), then the MIV_RV32 can boot the copied code.

The sources are available from the [Mi-V Soft processor Bare Metal examples](https://mi-v-ecosystem.github.io/_redirects/mi-v-soft-risc-v/miv-rv32-bare-metal-examples)
To run the Bootloader .elf program, follow the steps below or refer to the *MIV_ESS v2.0 Design Guide* (accessible from the Libero catalog) for more detailed instructions:
> A DGC1 Libero design directory is required to have been created to access the .elf file. The hardware needs to be programmed with DGC1 bitstream.

    1. Open SoftConsole (v2021.1 or above)
    2. From Run > Debug Configurations, double click GDB OpenOCD Debugging
    3. In the Main window, select C/C++ Applications using the Browse button, then navigate to your Libero's project directory ./MIV_RV32_DGC1_BD and select the Bootloader .elf file
    4. Select "Disable auto build" option
    5. Select the Debugger tab to set up OpenOCD and GDB
        a. To set up OpenOCD, check the Start OpenOCD locally check box and browse to manually set the OpenOCD path <SoftConsole-install-directory>\openocd\bin\openocd.exe
        b. To set up GDB, browse to the GDB path <SoftConsole-install-directory>\riscv-unknownelf-gcc\bin\riscv64-unknown-elf-gdb.exe.
    6. Click Apply
    7. Click Debug to launch the debug session. 

## <a name="Design Guide Configuration - DGC1: SPI Write & Boot"></a> Design Guide Configuration - DGC1: SPI Write & Boot

### Features
The Libero designs include the following features:
* A soft RISC-V processor operating at 50 MHz
* A RISC-V debug block allowing on-target debug using SoftConsole
* An Extended Subsystem with integrated peripherals
* Target SRAM/TCM memory (32kB)
* User peripherals: MIV_ESS (Bootstrap, SPI, GPIO, UART)

### Boot Sequence Operation
A more detailed description of the boot sequence can be found in this section.

> Pre-requisites:
> * The board needs to be programmed with DGC1 bitstream. Refer to this section, run the [Libero Design](#Running Libero SoC in GUI mode, with Script Arguments)
> * Initiating the Boot Sequence for DGC1, requires data to be previously written to external memory SPI Flash. Use the provided Bootloader .elf program *miv-rv32-ess-bootloader.elf* to write the *miv-rv32i-systick-blinky.hex* program in the LSRAM to the external memory SPI Flash.

    1. Once the board has powered-on, hold SW8 to enable the Bootstrap functionality in the MIV_ESS. Then press and release SW6 or SW7 to perform a system reset request or reset cycle the board.
    2. MIV_ESS copies a program from the SPI Flash device to the MIV_RV32 Tightly-Coupled Memory (TCM) via the TCM APB Target (TAS) interface.
    3. When the transfer from SPI Flash is complete, MIV_ESS releases MIV_RV32 core from reset and MIV_RV32 is allowed to boot the program from TCM.
    4. The LEDs on the PolarFire Eval Kit will start blinking, signifying Bootstrap has completed its transfer and SW6 can then be released.   

### Peripherals - MIV_ESS

| Peripheral                       | Address Start | Address End    |
| ------------------------------:  |:-------------:|:--------------:|
| PLIC                             | 0x7000_0000   | 0x70FF_FFFF    |
| UART                             | 0x7100_0000   | 0x71FF_FFFF    |
| Timer                            | 0x7200_0000   | 0x72FF_FFFF    |
| CoreTimer_0 / MIV_ESS_APBSLOT3   | 0x7300_0000   | 0x73FF_FFFF    |
| CoreTimer_1 / MIV_ESS_APBSLOT4   | 0x7400_0000   | 0x74FF_FFFF    |
| GPIO                             | 0x7500_0000   | 0x75FF_FFFF    |
| SPI                              | 0x7600_0000   | 0x76FF_FFFF    |
| uDMA                             | 0x7800_0000   | 0x78FF_FFFF    |
| WDOG                             | 0x7900_0000   | 0x79FF_FFFF    |
| I2C                              | 0x7A00_0000   | 0x7AFF_FFFF    |
| MIV_ESS_APBSLOTB_BASE            | 0x7B00_0000   | 0x7BFF_FFFF    |
| MIV_ESS_APBSLOTC_BASE            | 0x7C00_0000   | 0x7CFF_FFFF    |
| MIV_ESS_APBSLOTD_BASE            | 0x7D00_0000   | 0x7DFF_FFFF    |
| MIV_ESS_APBSLOTE_BASE            | 0x7E00_0000   | 0x7EFF_FFFF    |
| MIV_ESS_APBSLOTF_BASE            | 0x7F00_0000   | 0x7FFF_FFFF    |
    
### Memory Sources
    
| Memory Source                    | Address Start | Address End | Size   |
| -------------------------------: |:-------------:|:-----------:|:------:|
| TCM                              | 0x4000_0000   | 0x4000_7FFF | 32kB   | 
| LSRAM                            | 0x8000_0000   | 0x8000_7FFF | 32kB   |


