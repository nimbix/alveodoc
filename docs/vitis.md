# Introduction

The Vitis™ Application Acceleration Development flow, enables up to 25X better performance/watt for data center application acceleration leveraging FPGAs. Nimbix Inc® has partnered with Xilinx® to deliver an opportunity to test drive Vitis and see how FPGA-based acceleration can speed-up your OpenCL C, C/C++ and RTL Kernels. The Nimbix Cloud provides a purpose-built compute cloud for big data and computation. It now hosts the Vitis development environment for companies who want to streamline evaluations and accelerate FPGA kernels. 

Nimbix offers a variety of FPGA resources including the [Alveo Accelerator card](https://www.xilinx.com/products/boards-and-kits/alveo.html). See full list of available hardware here: [Xilinx FPGAs on Nimbix](boards.md)

# Design Examples

A variety of examples are available on the [Xilinx GitHub](https://github.com/Xilinx/Vitis_Accel_Examples)

## Hello World

This page will use a simple example to walkthrough the Vitis tools and runtime available on the [JARVICE](https://www.nimbix.net/platform/) platform which powers the NIMBIX cloud. 

### Start new Vitis Accel session on JARVICE

1. Login to the [JARVICE portal for Xilinx](https://xilinx-cloud.jarvice.com/)
2. Review the JARVICE environment:
    * [JARVICE Runtime Directories](https://jarvice.readthedocs.io/en/latest/nae/#runtime-directories-and-files)<br>**NOTE:** Only data in a user's vault is persistent between jobs. NIMBIX recommends using `/tmp` for best I/O performance (resulting data must be copied back to your vault before terminating the job). Please refer to `JARVICE Runtime Directories` for additional information
    * [Transferring Data](https://nimbix.zendesk.com/hc/en-us/articles/115004155683-How-to-Transfer-Data-to-and-from-Nimbix)
    * [Onboarding](https://nimbix.zendesk.com/hc/en-us/categories/115000891926-Onboarding)
3. Click on `Xilinx Vitis Unified Software Platform 2019.2` application

    ![Vitis App](vitis-app.png)

4. Select the `Desktop Mode Without FPGA` option

    ![Vitis Workflows](vitis-workflow.png)

5. `SUBMIT` the job.
6. Click on the Desktop Preview to join the Vitis session

    ![Vitis connect](vitis-connect.png)

### Clone Vitis Accel Examples for 2019.2

7. Open terminal
8. Clone `Hello World` into your vault
```bash
git clone --depth=1 https://github.com/Xilinx/Vitis_Accel_Examples /data/Vitis_Accel_Examples
```  

### Vitis Accel SW emulation

Like most accelerators, there are separate binaries for the host software and accelerated kernel. The Vitis Accel compiler will compile C/C++/VHDL/Verilog source into an `*.xclbin` binary for the Alveo accelerator card. The compilation process for FPGAs can exceed several hours to create a custom architecture for a design. To alleviate compilation times, Vitis Accel provides a software (sw_emu) and hardware (hw_emu) emulator to test and verify kernel designs. This section will go over using the software emulator.

1. Open a terminal
2. Compile kernel for sw emulation
```bash
# Create local build directory in /tmp
TMP_WORK_DIR=$(mktemp -d)
# Compile for SW emulation
cd /data/Vitis_Accel_Examples/hello_world
make all BUILD_DIR=${TMP_WORK_DIR} TARGET=sw_emu DEVICE=xilinx_u200_xdma_201830_2
```
### Run test with software emulator
```bash
# Setup environment for SW emulation
export XCL_EMULATION_MODE=sw_emu
emconfigutil --platform xilinx_u200_xdma_201830_2 --nd 1
# Run emulation test (not on Alveo hardware)
./host ${TMP_WORK_DIR}/vadd.xclbin
```

### Compile for Alveo hardware

1. Click on `Xilinx Vitis Unified Software Platform 2019.2` application

    ![Vitis App](vitis-app.png)

2. Select the `Batch Mode` option

    ![Vitis Workflows](vitis-workflow.png)

3. Fill in the `Command` box with:
```bash
TMP_WORK_DIR=$(mktemp -d) && make -C /data/Vitis_Accel_Examples/hello_world all BUILD_DIR=${TMP_WORK_DIR} TARGET=hw DEVICE=xilinx_u200_xdma_201830_2 && cp ${TMP_WORK_DIR}/*.xclbin /data/Vitis_Accel_Examples/hello_world
```

   ![Vitis Batch](vitis-batch.png)

A batch (non-interactive) job will start on the JARVICE Dashboard to build the Hello World `*.xclbin` file. This job will shutdown after completion. The `*.xclbin` file will be saved in the vault at `/data/Vitis_Accel_Examples/hello_world`

**NOTE** Ensure data is being written to the vault `/data` when using batch/non-interactive jobs. All other data (e.g. written to `${HOME}`) will be lost when the job ends.

### Run Hello World on Alveo

1. Click on `Xilinx Vitis Unified Software Platform 2019.2` application

    ![Vitis App](vitis-app.png)

2. Select the `Desktop Mode With FPGA` option

    ![Vitis Workflows](vitis-workflow.png)

3. Open terminal and run `Hello World`
```bash
# List available Xilinx accelerators
xbutil list
# Get general info
xbutil query
# Run Hello World
cd /data/Vitis_Accel_Examples/hello_world
./host vadd.xclbin
``` 
### Shutdown Vitis Accel

   ![Vitis Shutdown](vitis-shutdown.png)

