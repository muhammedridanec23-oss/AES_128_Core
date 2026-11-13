# AES-128 Core — FPGA and ASIC Implementation

A hardware implementation of the **AES-128 (Advanced Encryption Standard)** encryption algorithm using Verilog HDL, with functional verification in Xilinx Vivado and ASIC physical design using OpenLane.

This project explores the implementation of AES-128 encryption for FPGA and ASIC platforms.

## 1. Project Overview

AES-128 is a symmetric-key block cipher that encrypts a 128-bit plaintext block using a 128-bit secret key.

The algorithm operates on a 4 × 4 byte state matrix and performs an initial key addition followed by ten encryption rounds.

This project implements the AES-128 encryption datapath as a clocked RTL core.

### Key Specifications

| Parameter | Description |
|---|---|
| Algorithm | AES-128 Encryption |
| Block Size | 128 bits |
| Key Size | 128 bits |
| Number of Rounds | 10 |
| RTL Language | Verilog HDL |
| Verification | Xilinx Vivado Simulator |
| FPGA Tool | Xilinx Vivado |
| FPGA Board | Digilent Arty A7-100T |
| ASIC Flow | OpenLane |
| Synthesis Tool | Yosys |

## 2. AES-128 Architecture

The AES-128 encryption algorithm consists of the following transformations:

- **SubBytes:** Substitutes each byte using the AES S-Box.
- **ShiftRows:** Cyclically shifts the rows of the AES state matrix.
- **MixColumns:** Performs column-wise transformation using arithmetic in GF(2^8).
- **AddRoundKey:** XORs the state with the corresponding round key.

The encryption process consists of:

1. Initial AddRoundKey.
2. Nine standard AES rounds containing SubBytes, ShiftRows, MixColumns, and AddRoundKey.
3. Final AES round containing SubBytes, ShiftRows, and AddRoundKey (without MixColumns).

The core uses a finite-state machine with the following states:

- IDLE
- ROUNDS
- FINISH

The controller manages the encryption process and asserts the `done` signal when the ciphertext is ready.

### Key Schedule

The AES core receives the expanded key schedule through a 1408-bit input.

The key schedule contains eleven 128-bit round keys:

- Round Key 0: `key_schedule[1407:1280]`
- Round Key 1–9: Intermediate 128-bit slices.
- Round Key 10: `key_schedule[127:0]`

The expanded keys must be supplied in the correct order.

The core performs the initial AddRoundKey operation using Round Key 0, followed by the remaining nine standard rounds and the final round using Round Key 10.

## 3. Repository Structure

| File / Directory | Description |
|---|---|
| `AES_128_main.v` | Main Verilog RTL implementation |
| `AES_128_Core_TB.v` | AES-128 testbench |
| `Openlane Configuration File/` | OpenLane configuration files |
| `Yosys Synthesis Reports/` | RTL synthesis reports |
| `Floorplanning reports/` | Floorplanning-stage reports |
| `Placement Reports/` | Placement-stage reports |
| `CTS reports/` | Clock Tree Synthesis reports |
| `Routing reports/` | Routing-stage reports |
| `DRC reports/` | Design Rule Check reports |
| `LVS reports/` | Layout Versus Schematic reports |
| `Antenna Violation Report/` | Antenna-check reports |
| `Final Gate Level Netlist/` | Final synthesized gate-level netlist |
| `Final GDSII/` | Final GDSII layout output |

## 4. RTL Interface

The main module is `aes_128_core`.

| Port | Direction | Width | Description |
|---|---|---|---|
| `clk` | Input | 1 bit | Clock signal |
| `rst` | Input | 1 bit | Active-high asynchronous reset |
| `start` | Input | 1 bit | Starts encryption |
| `plaintext` | Input | 128 bits | Input plaintext |
| `key_schedule` | Input | 1408 bits | Expanded AES round keys |
| `ciphertext` | Output | 128 bits | Encrypted output |
| `done` | Output | 1 bit | Encryption completion indicator |

### Operation Sequence

1. Apply the clock and assert reset.
2. Deassert reset.
3. Provide the 128-bit plaintext.
4. Provide the 1408-bit expanded key schedule.
5. Assert the `start` signal.
6. Wait for the `done` signal.
7. Read the resulting 128-bit ciphertext.

## 5. RTL Simulation Using Xilinx Vivado

Functional verification of the AES-128 core was performed using **Xilinx Vivado Simulator (XSim)**.

### Simulation Procedure

1. Open Xilinx Vivado.
2. Create a new RTL project.
3. Add `AES_128_main.v` under Design Sources.
4. Add `AES_128_Core_TB.v` under Simulation Sources.
5. Set the testbench module as the simulation top.
6. Select **Run Simulation → Run Behavioral Simulation**.
7. Observe the simulation waveform.
8. Verify the `done` signal and resulting `ciphertext`.

The testbench includes AES-128 test vectors and compares the generated ciphertext against the expected results.

Successful verification is indicated by the testbench reporting that all test cases have passed.

## 6. FPGA Implementation

The AES-128 RTL is intended for FPGA implementation using Xilinx Vivado.

### Target FPGA

**Digilent Arty A7-100T**

### Implementation Procedure

1. Create a new Vivado project targeting the Arty A7-100T.
2. Add the AES-128 RTL source.
3. Create a synthesizable FPGA top-level wrapper.
4. Connect the clock, reset, plaintext, key schedule, and start signals.
5. Connect the ciphertext and completion signals to suitable output interfaces.
6. Add the appropriate XDC pin constraints.
7. Run synthesis.
8. Run implementation.
9. Generate the FPGA bitstream.
10. Program the FPGA and verify the encryption output.

**Note:** The AES core expects an expanded 1408-bit key schedule. An FPGA system must provide the expanded keys, either through a key-expansion module or another suitable input mechanism.

The simulation testbench is not intended to be used as the FPGA top-level module.

## 7. ASIC Implementation Using OpenLane

The ASIC implementation uses OpenLane to perform the RTL-to-GDSII physical-design flow.

### ASIC Design Flow

1. RTL preparation
2. Logic synthesis using Yosys
3. Floorplanning
4. Placement
5. Clock Tree Synthesis (CTS)
6. Global Routing
7. Detailed Routing
8. Design Rule Checking (DRC)
9. Layout Versus Schematic (LVS)
10. GDSII generation

### Physical Design Outputs

The repository includes reports and implementation artifacts generated during the physical-design process, including:

- Synthesis reports
- Floorplanning reports
- Placement reports
- CTS reports
- Routing reports
- DRC reports
- LVS reports
- Antenna-check reports
- Final gate-level netlist
- Final GDSII layout

The OpenLane configuration files are provided in the repository.

## 8. Tools and Technologies

| Tool / Technology | Purpose |
|---|---|
| Verilog HDL | RTL design |
| Xilinx Vivado | FPGA synthesis and implementation |
| Vivado Simulator (XSim) | RTL functional verification |
| Digilent Arty A7-100T | FPGA target |
| OpenLane | ASIC physical-design flow |
| Yosys | Logic synthesis |
| Sky130 PDK | ASIC technology platform |
| KLayout | GDSII layout visualization |

## 9. Verification

The AES-128 core is verified using a Verilog testbench in Xilinx Vivado.

The testbench applies known AES-128 input vectors and checks the generated ciphertext against the expected values.

The simulation verifies the functional behavior of the RTL for the included test cases.

ASIC physical-design reports are provided separately for examining synthesis, placement, clock-tree synthesis, routing, and physical verification results.

## 10. Future Improvements

- Integrate an AES-128 key expansion module.
- Develop a complete FPGA interface for plaintext and key input.
- Add more AES-128 verification test vectors.
- Evaluate FPGA resource utilization and maximum operating frequency.
- Optimize the RTL for reduced area and improved timing.
- Analyze ASIC power, performance, and area (PPA).
- Further optimize the physical-design flow.

## 11. References

1. NIST, *Advanced Encryption Standard (AES)*, FIPS PUB 197. https://doi.org/10.6028/NIST.FIPS.197
2. OpenLane Documentation: https://openlane2.readthedocs.io/
3. AMD/Xilinx Vivado Design Suite Documentation: https://docs.amd.com/

## 12. Author

**Muhammed Ridan**

GitHub: https://github.com/muhammedridanec23-oss

Repository: https://github.com/muhammedridanec23-oss/AES_128_Core

---

**AES-128 Core | FPGA and ASIC Implementation**
