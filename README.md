# PES_ASIC_CLASS
This Repository Guides you to complete ASIC flow from scratch (FACULTY : Mahesh Awati, GUIDE : Kunal Ghosh)

## The COURSE files are present under those respective day folders 

### Solutions to frequenty occuring errors are in Error_solution.md

## Install the Prerequisites(for ubuntu)

```
sudo apt update
sudo apt upgrade
chmod +x run_ubuntu.sh
./run_ubuntu.sh
```
The installed contents will be available at ~/riscv_toolchain

# Introduction
### Flow : HLL -> ALP -> Binary -> (HDL) -> GDS
#### 1. HLL -> High level language (c , c++) 
- A high-level programming language is a type of programming language that is designed to be more human-readable and user-friendly compared to low-level languages like assembly or machine code.

#### 2. ALP -> Assembly level program
- Assembly language is a low-level programming language that is closely related to the architecture of a specific computer's central processing unit (CPU). Assembly language programs are written using mnemonic codes that represent specific machine instructions which the machine can understand.

#### 3. HDL -> Hardware Description Language
- It is a specialized programming language used for designing and describing digital hardware circuits. HDLs allow engineers to model and simulate complex digital systems before physical implementation, aiding in the design and verification of integrated circuits and FPGA configurations.
- Verilog, System Verilog, VHDL

#### 4. GDS -> Graphic Data System (layout)
- Format used in the semiconductor industry to represent the layout and design of integrated circuits at a highly detailed level. GDSII files contain information about the geometric shapes, layers, masks, and other essential details that make up the physical layout of a chip.
- Tools : Klayout, Magic

##### The Hardware needs to understand what the Application software is saying it to do.This relation can be eshtablished by System Software

____System Software____
- OS : Operating System : Handles IO, memory allocation, Low level system function
- Compiler : Convert the input to hardware dependent instruction
- Assembler : Convert the instructions provided by compiler to Binary format
- HDL : A program that understands the Binary pattern and map it to a netlist
- GDS : Layout

# COURSE 
<details>
<summary>DAY 1: Introduction to RISCV ISA and GNU Compiler Toolchain</summary>
<br>

## Introduction to Risc-v Basic Keywords
- **Instruction Set Architecture(ISA)**
  - An Instruction Set Architecture (ISA) refers to the set of instructions that a computer's central processing unit (CPU) can understand and execute. It defines the interface between software and hardware, specifying the operations that a CPU can perform, the data types it can manipulate, and the memory addressing modes it supports.

- **Risc-V ISA**
  - Risc-V ISA is an open-source ISA that has simpler and fixed length instructions that allows us to create custom processors for specific needs without being tied to proprietary architectures
 
- **Tools Used for the flow**
  - As we are aware of the flow, we will be using Risc-v ISA ALP and the RTL used will be picorv32a (We will be using rv64i during initial stages)

# Goal : Any High level Program that is written should be able to get executed in our CHIP

### List of well-known extensions present in Risc-V ISA

``` rv32i``` ``` rv64i``` ```rv32imc``` ```rv64imc``` ```rv32imafdc``` ```rv64imafdc``` ```rv32imcb``` ```rv64imcb``` ```rv32imc_sv32``` ```rv64gcv```

### Extensions and their Applications

- **I (Integer)** :The I set includes the base integer instruction set for RISC-V. It provides fundamental integer arithmetic and logical operations, data movement, and control flow instructions.
  - ADD, SUB, AND, OR, XOR, ADDI, SLTI, JAL, BEQ, LW

- **M (Multiply and Divide)** : The M set adds integer multiplication and division instructions to the base integer set. These instructions are particularly useful for arithmetic-heavy computations.
  - MUL, MULH, DIV, REM
  
- **A (Atomic)** : The A set introduces atomic memory access instructions. These instructions enable multiple operations on memory locations to be performed atomically, ensuring that other processors or threads cannot observe intermediate states.
  - LR (Load-Reserved), SC (Store-Conditional), AMO (Atomic Memory Operation)
  
- **F (Single-Precision Floating-Point)**: The F set adds single-precision floating-point instructions. These instructions enable arithmetic operations on 32-bit floating-point numbers.
  - FADD.S, FSUB.S, FMUL.S, FDIV.S, FCVT.W.S, FCVT.S.W

- **D (Double-Precision Floating-Point)** : The D set includes double-precision floating-point instructions. These instructions allow arithmetic operations on 64-bit floating-point numbers.
  - FADD.D, FSUB.D, FMUL.D, FDIV.D, FCVT.W.D, FCVT.D.W

- **C (Compressed)** : The C set introduces a compressed instruction format that reduces the size of code. Compressed instructions maintain the same functionality as their non-compressed counterparts but use shorter encodings.
  - C.ADDI4SPN, C.LWSP, C.ADDI, C.SW, C.JALR, C.BEQZ

- **G (Atomic and Lock-Free Operations)** : The G set, also known as the "GAS Set," is an alternative to the A set. It focuses on providing atomic and lock-free instructions to simplify hardware implementation.
  - LRV (Load-Reserved Variant), SCV (Store-Conditional Variant), AMO (Atomic Memory Operation Variants)

- **V (Vector)** :The V set adds vector instructions to the ISA, enabling Single Instruction, Multiple Data (SIMD) operations. These instructions allow efficient parallel processing of data elements in vectors.
  - VADD, VMUL, VFMADD, VLW, VSW

- **S (Supervisor)** : The S set, often used in privileged modes, includes instructions for managing and interacting with the supervisor-level operations of the system, such as handling exceptions and interrupts.
  - ECALL, EBREAK, SRET, MRET, WFI

- **B (Bit Manipulation)** : The B set introduces instructions for bit manipulation operations, allowing efficient manipulation of individual bits in registers and memory.
  - ANDI, ORI, XORI, SLLI, SRLI, SRAI

## 1. Create a simple C program That calculates sum from 1 to N -> sum_1_to_N.c

_____Compile it using C compiler_____
```
gcc sum_1_to_N.c -o 1_to_N.o
./1_to_N.o
```
-o allows you to name your output file
![1](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/9fbea236-80cc-42cc-9cdb-1a51504aeef0)



_____compile using riscv compiler and view the output_____
```
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o 1_to_N.o sum_1_to_N.c
spike pk 1_to_N.o
```

![2](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/8c6fcff4-ec1a-45fe-a462-c6f56776d07a)


- ```-O<number>``` : level of optimisation required
- ```-mabi``` : specifies the ABI (Application Binary Interface) to be used during code generation according to the requirements
- ```-march``` : specifies target architecture

_______We can check the different options available for all these fields using the commands_______ 
go to the directory where riscv64-unkonwn-elf is present
- -O1 : ``` riscv64-unkonwn-elf --help=optimizer```
- -mabi : ```riscv64-unknown-elf-gcc --target-help```
- -march : ```riscv64-unknown-elf-gcc --target-help```

_____To view the disassembled ALP code_____
```
riscv64-unknown-elf-objdump -d 1_to_N.o
```

_____To debug the ALP generated by the compiler_____
```
spike -d pk 1_to_N.o
```
![3](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/ac79c66e-a094-406b-b3f8-bb41a6773145)


- press ENTER : shows the first line and successive ENTER shows successive lines
- reg 0 a2 : checks content of register a2 0th core
- q : quit the debug process

##### Difference between the ALP commands when used different optimizers
- use the command ```riscv64-unknown-elf-objdump -d 1_to_N.o | less```
- use ``` /instance``` to search for an instance 
- press ENTER
- press ```n``` to search next occurance
- press ```N``` to search for previous occurance. 
- use ```esc :q``` to quit

_____Contents of main when used -O1 optimizer_____
![4](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/7ebf50b4-bd19-4ae5-b8ae-85b8e4c18bd7)




## Integer number Representation (n-bit)
- Range of Unsigned numbers : [0, (2^n)-1 ]
* Range of signed numbes : Positive : [0 , 2^(n-1)-1]
                         Negative : [-1 to 2^(n-1)]

## 2. create a C program that shows the maximum and minimum values of 64bit unsigend and signed numbers

```
sign_unsign.c
```
![5](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/8fa82c41-7198-407c-b22f-e235b32da9f6)


[Back to COURSE](https://github.com/yagnavivek/PES_ASIC_CLASS/tree/main#course)

</details>
<details>
<summary>DAY 2 : Introduction to ABI and Basic Verification Flow </summary>
<br>

## BASICS :

Instructions that act on signed or unsigned integers are called Base Integer Instructions
There are 47 Base Integer Instructions present in RISC-V ISA

### Types of Instruction based on encoding format

1. **R-Type (Register-Type):**
   - These instructions operate on registers and have a fixed format for their operands.
   - Examples: ADD, SUB, AND, OR, XOR, SLL, SRL, SRA, SLT, SLTU

2. **I-Type (Immediate-Type):**
   - These instructions have an immediate operand and one register operand.
   - Examples: ADDI, SLTI, SLTIU, XORI, ORI, ANDI, SLLI, SRLI, SRAI, LB, LH, LW, LBU, LHU, JALR

3. **S-Type (Store-Type):**
   - These instructions are used for storing values from registers to memory.
   - Examples: SB, SH, SW

4. **B-Type (Branch-Type):**
   - These instructions perform conditional branching based on comparisons.
   - Examples: BEQ, BNE, BLT, BGE, BLTU, BGEU

5. **U-Type (Upper Immediate-Type):**
   - These instructions have a larger immediate field for encoding larger constants.
   - Examples: LUI, AUIPC

6. **J-Type (Jump-Type):**
   - These instructions are used for unconditional jumps and function calls.
   - Examples: JAL

<img width="1000" height="420" alt="image" src="https://github.com/yagnavivek/PES_ASIC_CLASS/assets/93475824/e69043fb-684e-42eb-9e21-fd51943c1ec1">

**[number]** represents number of bits occupied by that field

1. **Opcode [7] :** The opcode is a field within a machine language instruction that indicates the operation to be performed by the instruction. It defines the type of operation, such as arithmetic, logic, memory access, or control flow. Opcodes are used by the CPU to determine how to execute the instruction.

2. **rd (Destination Register) [5]:** The "rd" field represents the destination register in an assembly language instruction. It indicates the register where the result of the operation will be stored. After executing the instruction, the computed value will be placed in this register.

3. **rs1 (Source Register 1) [5]:** The "rs1" field represents the first source register in an assembly language instruction. It indicates the register that holds the value used in the operation. For instructions that involve two operands, "rs1" typically corresponds to the first operand.

4. **rs2 (Source Register 2) [5]:** The "rs2" field represents the second source register in an assembly language instruction. It indicates the register that holds the value used in the operation. For instructions that involve three operands, "rs2" typically corresponds to the second operand.

5. **func7 and func3 (Function Fields)[7] [3]:** These fields further refine the operation specified by the opcode. The "func7" field is used to distinguish different variations of instructions within the same opcode category. The "func3" field is used to specify a more specific operation within the opcode category. Together, these fields allow for a finer level of instruction differentiation.

6. **imm (Immediate Value):** The "imm" field represents an immediate value that is part of the instruction. Immediate values are constants that are embedded within the instruction itself. They can be used for various purposes, such as specifying offsets, constants, or small data values directly within the instruction.


#### ABI : Application Binary Interface

The instructions generated by compiler using a target ISA can be accessed by OS and User directly
- The parts of ISA accessible to User : User ISA
- The parts of ISA accessible to OS : system ISA
The access is done using Sysytem calls with the help of ABI

==> If we want to access hardware resources of processor, it has to be done via registers using ABI(names)

### ABI Names : 
- ABI names for registers serve as a standardized way to designate the purpose and usage of specific registers within a software ecosystem. These names play a critical role in maintaining compatibility, optimizing code generation, and facilitating communication between different software components.

<img width="1000" height="600" src="https://github.com/yagnavivek/PES_ASIC_CLASS/assets/93475824/27d13974-1b70-4207-a2fb-05b232027323">

#### Data can be stored in register by 2 methods
1. Directly store in registers
2. Store into registers from memory

To store 64 bits of data from mem to reg, we use 8*8bit stores ie., m[0],m[1]......m[7]. 

- ___RISC-V uses Little Endian format to store the data ie., Least significant Byte is stored in m[0]___

## Simulate a C program using ABI function call (using registers) and execute 

The required program files are under day 2 folder

![6](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/32d6563b-34a0-4b35-bf6f-8e4701945fd3)


![7](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/334d937c-c4a2-40a5-b537-29227327ae3b)

Here we can observe that at 5th line, inorder to comute the result ,its going to the "load"  function

### Further we will see how to run a C program on on RISC-V CPU

- Input : C Program loaded into memory to RISC-V CPU in Hex format

CPU processes the contents of the memory and provides with output using iverilog 

- Risc-V CPU : ```Picorv32.v```
- Testbench for verification : ```testbench.v```
- Tool : ```iverilog```
- script : ```rv32im.sh``` : has the commands to get the c-program, ALP, converts into hex format, loads into memory of riscv cpu, passes it iverilog and provides the output




[Back to COURSE](https://github.com/yagnavivek/PES_ASIC_CLASS/tree/main#course)
</details>

</details>
<details>
<summary>DAY 3 : Introduction to verilog RTL and Design Synthsis </summary>
<br>

# Introduction to open-source simulator iverilog
**Simulator:** RTL design is checked for adherence to the spec by simulating the design. Simulator is the tool used for simulating the design

**Design:** Design is the actual verilog codeor set of verilog codes which has the intended functionality to meet with the required specification

**Testbench:** Testbench is the setup to apply stimulus to the design to check its functionality
### How do simulator works?
- It looks for the changes in input signal
- upon change to the input the out put is evaluated

![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/048716ed-331f-40d8-ab65-4905bd75043c)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/a9b0ffc3-bcd4-42d9-89ee-252b5d4f6cda)

# Labs using iverilog and gtkwave
## Introduction to lab
- Make new directory `mkdir VLSI`
- `git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git`
- This should create a folder `sky130RTLDesignAndSynthesisWorkshop` in `VLSI` directory
- You could see two folders under `sky130RTLDesignAndSynthesisWorkshop`
   1. my_lib: It contains all the standard cell libraries and verilog module
   2. verilog_files: It contains all the source code and testbench required for the lab
  
![1](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/b3d9dff5-6ebc-4560-834e-7ffdfd31b8cf)



## Introduction iverilog gtkwave
- Go to verilog_files directory
- Load Design and Testbench using the command `iverilog good_mux.v tb_good_mux.v`
### good_mux.v
``` v
module good_mux (input i0 , input i1 , input sel , output reg y);
always @ (*)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
### tb_good_mux.v
``` v
timescale 1ns / 1ps
module tb_good_mux;
	// Inputs
	reg i0,i1,sel;
	// Outputs
	wire y;

        // Instantiate the Unit Under Test (UUT)
	good_mux uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.y(y)
	);

	initial begin
	$dumpfile("tb_good_mux.vcd");
	$dumpvars(0,tb_good_mux);
	// Initialize Inputs
	sel = 0;
	i0 = 0;
	i1 = 0;
	#300 $finish;
	end

always #75 sel = ~sel;
always #10 i0 = ~i0;
always #55 i1 = ~i1;
endmodule
```

- Upon loading sucessfully `a.out` will be generated
- Execute the generated file it would dump `gtkwave tb_good_mux.vcd` file

![2](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/d81fe442-35bb-458d-9ffd-2aa93531d37b)


- Load the vcd file to simulator using the command `gtkwave tb_good_mux.vcd`
  
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/3c17ad4f-a662-48b0-8f87-641f448c546e)


# Introduction to Yosys and Logic synthesis
## Introduction to yosys
Yosys is an synthesizer which is used to convert RTL to netlist

![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/c7c8c923-3756-4a5f-8d8e-bc1f197122c8)

**`netlist` is the representation of `DESIGN` in the form of standard cells present in `.lib`**

- To verify synthesis Netlist need to be fed to iverilog along with testbench
- vcd file generated from iverilog need to be fed to gtkwave simulator
- The output we get should be same as the output we got during RTL simulator

![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/cf9df685-8243-4b32-ba07-ca872e1abcb2)

![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/6b37c624-302d-4685-bbc8-37213c120baf)

## Introduction to logic synthesis
Logic synthesis is a vital step in digital circuit design where high-level descriptions of circuits are transformed into specific implementations using logic gates. It optimizes circuits for factors like performance, area, power, and cost. The process includes library mapping, optimization, technology mapping, timing analysis, and verification. It's an iterative process often done with specialized software tools, enabling efficient hardware design.

**.lib:** 
- Logic synthesis tools use a library of standard cells. These cells are predefined logic gates with different functionalities and characteristics
- It will also contain fast and slow version of same gate
### why fast and slow version of same gate?
Fast and slow versions of gates are essential in digital circuit design to balance between clock frequency and timing constraints. Fast gates have shorter propagation delays and are used to reduce setup and hold time violations, allowing for higher clock frequencies. Slow gates, with longer delays, can be used to intentionally slow down critical paths or address timing issues. The Tclk formula helps calculate the maximum clock frequency while considering these factors.

**Tclk formula:** ![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/43cac247-0106-427a-a9f5-786f6aab2197)

- **t_setup:** The setup time is the minimum time before the clock edge when the input data must be stable.
- **t_hold:** The hold time is the minimum time after the clock edge during which the input data must remain stable.
- **t_propagation:** This term represents the propagation delay of the logic gates in the critical path.
- **Tcq:** This term represents the clock-to-q delay of the flip-flops or registers used in the design. It's often a fixed value based on the chosen flip-flop technology.

# Lab using Yosys and Sky130 PDKs
Steps to realize good_mux(design) in terms of standard cells avilable in library sky130_fd_sc_hd__tt_025C_1v80.lib
+ Go to verilog_files directory
+ once you get to verilog_files directory, Invoke yosys by using the command `yosys`
![4](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/f2813547-5fdd-449f-8b92-b010f450884d)



  1. Read library: `read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib`
     
     ![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/da09cd7f-36a1-42a9-9792-7e76cd664463)

  3. Read design: `read_verilog good_mux.v`
  
     ![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/c2db93cc-f39e-41c6-8236-163cec43e13b)


  5. Synthesis: `synth -top good_mux`
     
     ![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/ec8f4e23-e2b2-4c3c-bacd-8c706f0f5a5c)


  7. Generate netlist: `abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib`
     
     ![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/55483ac8-ae5f-4584-a247-fca1b6b9835b)


  9. Logic realized: `show`
      
      ![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/f12f77b7-72e3-461a-a163-2fe70a165f7a)


  11. Write netlist: `write_verilog -noattr good_mux_netlist.v`, `!gvim good_mux_netlist.v`
      
      ![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/e8f05a55-7a9a-4564-a08d-bcf0154a56ae)


### good_mux_netlist.v
``` v
/* Generated by Yosys 0.32+51 (git sha1 6405bbab1, gcc 12.3.0-1ubuntu1~22.04 -fPIC -Os) */

module good_mux(i0, i1, sel, y);
  wire _0_;
  wire _1_;
  wire _2_;
  wire _3_;
  input i0;
  wire i0;
  input i1;
  wire i1;
  input sel;
  wire sel;
  output y;
  wire y;
  sky130_fd_sc_hd__mux2_1 _4_ (
    .A0(_0_),
    .A1(_1_),
    .S(_2_),
    .X(_3_)
  );
  assign _0_ = i0;
  assign _1_ = i1;
  assign _2_ = sel;
  assign y = _3_;
endmodule
```

[Back to COURSE](https://github.com/Vinodkumar8318/Ppes_asic_class/tree/main#course)

</details>
<details>
<summary>DAY 4 : Timing Libs, Hirearchical vs Flat Synthsis and Efficient Flop Coding Styles </summary>
<br>

# Introduction to timing dot libs
### sky130_fd_sc_hd__tt_025C_1v80.lib

+ **sky130:** This indicates that the library is associated with the SkyWater 130nm process technology. Process technology refers to the manufacturing process used to create integrated circuits (ICs) and determines factors like transistor size and performance characteristics.
  
+ **fd_sc_hd:** These letters likely stand for different aspects of the library. "fd" might refer to "Foundation," suggesting that this library contains fundamental building blocks for digital IC design. "sc" could stand for "Standard Cells," which are pre-designed logic gates used in IC design. "hd" could refer to "high-density" libraries, which typically contain smaller, more compact cell designs for achieving higher logic density in ICs.
  
+ **tt_025C:** This part of the name could refer to the library's operating conditions or temperature and voltage settings. "tt" might stand for "typical temperature," and "025C" could refer to 25 degrees Celsius, a common temperature for IC specifications. These conditions are important for characterizing the library's performance.
  
+ **1v80:** This likely represents the supply voltage for the library. In this case, "1v80" indicates a supply voltage of 1.8 volts, which is a common voltage level for digital ICs.

### Libraries contain

+ **Standard Cells:** This library is likely to include a variety of standard cells, which are pre-designed building blocks for digital logic. Standard cells consist of logic gates (e.g., AND, OR, XOR), flip-flops, latches, multiplexers, and other fundamental digital components. These cells are characterized for the specific process technology (in this case, SkyWater 130nm) and operating conditions (temperature and voltage).
  
+ **Timing Information:** The library will provide timing information for each standard cell. This information includes parameters like propagation delay, setup time, hold time, and other timing characteristics. Designers use this data to ensure that signals propagate correctly through the logic gates.
  
+ **Power Characteristics:** Power consumption data is crucial for estimating the energy usage of a design. The library will typically include information on dynamic power (power consumed when the circuit is switching) and static power (power consumed when the circuit is idle).
  
+ **Pin and I/O Information:** The library will specify the input and output pins for each standard cell, including pin names, directions (input or output), and electrical characteristics.
  
+ **Library Files:** These library files often come in various formats, including Liberty (.lib) files, which contain detailed timing and power information, and Verilog models, which allow designers to use these standard cells in their digital designs.
  
+ **Characterization Data:** The library may include data characterizing how the standard cells perform under different operating conditions, including variations in temperature and supply voltage. This helps designers account for variability in their designs.
  
+ **Technology Files:** These files might also include information about the specific characteristics of the SkyWater 130nm process technology, such as transistor models, interconnect information, and other process-related data.

# Hierarchical vs Flat Synthesis
### Hierarchical Synthesis
- Hierarchical synthesis involves dividing the design into logical modules or blocks and synthesizing each module separately.
- These modules can have their own hierarchies, and they communicate through well-defined interfaces
- It enhances reusability, as individual modules can be reused in other designs.
- Supports better scalability for large, complex designs.

### Steps to Hierarchical Synthesis
- Go to verilog_files directory
- once you get to verilog_files directory, Invoke yosys by using the command `yosys`
- once yosys is invoked follow the above sequence of commands
  ``` sh
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog multiple_modules.v
  synth -top multiple_modules
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show multiple_modules
  write_verilog -noattr multiple_modules_hier.v
  !gvim multiple_modules_hier.v
  ```
  ![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/c394adbb-654b-4c8e-a065-2b07752ad0d8)


  **multiple_modules_hier.v**
  ``` v
  /* Generated by Yosys 0.32+51 (git sha1 6405bbab1, gcc 12.3.0-1ubuntu1~22.04 -fPIC -Os) */

  module multiple_modules(a, b, c, y);
    input a;
    wire a;
    input b;
    wire b;
    input c;
    wire c;
    wire net1;
    output y;
    wire y;
    sub_module1 u1 (
      .a(a),
      .b(b),
      .y(net1)
    );
    sub_module2 u2 (
      .a(net1),
      .b(c),
      .y(y)
    );
  endmodule
 
  module sub_module1(a, b, y);
    wire _0_;
    wire _1_;
    wire _2_;
    input a;
    wire a;
    input b;
    wire b;
    output y;
    wire y;
    sky130_fd_sc_hd__and2_0 _3_ (
      .A(_1_),
      .B(_0_),
      .X(_2_)
    );
    assign _1_ = b;
    assign _0_ = a;
    assign y = _2_;
  endmodule

  module sub_module2(a, b, y);
    wire _0_;
    wire _1_;
    wire _2_;
    input a;
    wire a;
    input b;
    wire b;
    output y;
    wire y;
    sky130_fd_sc_hd__or2_0 _3_ (
      .A(_1_),
      .B(_0_),
      .X(_2_)
    );
    assign _1_ = b;
    assign _0_ = a;
    assign y = _2_;
  endmodule
  ```
### Flat Synthesis
- In flat synthesis, the entire design is synthesized as a single, monolithic entity.
- All modules, submodules, and logic are flattened into a single level of hierarchy.
- This means that all components, regardless of their intended functionality, are combined into a single giant netlist
- Best suited for simple designs where complexity is low and maintainability isn't a significant concern.

### Steps to Flat Synthesis
- Go to verilog_files directory
- once you get to verilog_files directory, Invoke yosys by using the command `yosys`
- once yosys is invoked follow the above sequence of commands
  ``` sh
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog multiple_modules.v
  synth -top multiple_modules
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  flatten
  show
  write_verilog -noattr multiple_modules_flat.v
  !gvim multiple_modules_flat.v
  ```
 ![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/88dc8c1a-1e14-49b6-8638-8442c76a434a)


  **multiple_modules_flat.v**
  ``` v
  /* Generated by Yosys 0.32+51 (git sha1 6405bbab1, gcc 12.3.0-1ubuntu1~22.04 -fPIC -Os) */

  module multiple_modules(a, b, c, y);
    wire _0_;
    wire _1_;
    wire _2_;
    wire _3_;
    wire _4_;
    wire _5_;
    input a;
    wire a;
    input b;
    wire b;
    input c;
    wire c;
    wire net1;
    wire \u1.a ;
    wire \u1.b ;
    wire \u1.y ;
    wire \u2.a ;
    wire \u2.b ;
    wire \u2.y ;
    output y;
    wire y;
    sky130_fd_sc_hd__and2_0 _6_ (
      .A(_1_),
      .B(_0_),
      .X(_2_)
    );
    sky130_fd_sc_hd__or2_0 _7_ (
      .A(_4_),
      .B(_3_),
      .X(_5_)
    );
    assign _4_ = \u2.b ;
    assign _3_ = \u2.a ;
    assign \u2.y  = _5_;
    assign \u2.a  = net1;
    assign \u2.b  = c;
    assign y = \u2.y ;
    assign _1_ = \u1.b ;
    assign _0_ = \u1.a ;
    assign \u1.y  = _2_;
    assign \u1.a  = a;
    assign \u1.b  = b;
    assign net1 = \u1.y ;
  endmodule
  ```
# Various Flop Coding Styles and optimization
## Flop coding styles
**Asynchronous Reset D Flip-Flop**
- When an asynchronous reset input is activated (set to '1'), regardless of the clock signal, the stored value is forced to '0'.
- Otherwise, on the positive edge of the clock signal, the stored value is updated with the data input.
### dff_asyncres_syncres.v
``` v
module dff_asyncres_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk , posedge async_reset)
begin
	if(async_reset)
		q <= 1'b0;
	else if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```

**Asynchronous Set D Flip-Flop**
- When an asynchronous set input is activated (set to '1'), regardless of the clock signal, the stored value is forced to '1'.
- Otherwise, on the positive edge of the clock signal, the stored value is updated with the data input.
### dff_async_set.v
``` v
module dff_async_set ( input clk ,  input async_set , input d , output reg q );
always @ (posedge clk , posedge async_set)
begin
	if(async_set)
		q <= 1'b1;
	else	
		q <= d;
end
endmodule
```

**Synchronous Reset D Flip-Flop**
- When a synchronous reset input is activated (set to '1') at the positive edge of the clock signal, the stored value is forced to '0'.
- Otherwise, on the positive edge of the clock signal, the stored value is updated with the data input.
### dff_syncres.v
``` v
module dff_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk )
begin
	if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```

**D Flip-Flop with Asynchronous Reset and Synchronous Reset**
- This flip-flop combines both asynchronous and synchronous reset features.
- When the asynchronous reset input is activated (set to '1'), the stored value is immediately forced to '0'.
- When the synchronous reset input is activated (set to '1') at the positive edge of the clock signal, the stored value is forced to '0'.
- Otherwise, on the positive edge of the clock signal, the stored value is updated with the data input.
### dff_asyncres_syncres.v
``` v
module dff_asyncres_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk , posedge async_reset)
begin
	if(async_reset)
		q <= 1'b0;
	else if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```
## Flop synthesis simulations 
**Asynchronous Reset D Flip-Flop**

**Simulation**

Go to verilog_files directory where the design and test_bench are present

Run the following commands to simulate dff_asyncres.v
```
iverilog dff_asyncres.v tb_dff_asyncres.v
./a.ot
gtkwave tb_dff_asyncres.vcd
```
![5](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/ea5b709f-7c08-4c1c-93b0-97408ac4efb6)



![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/a987a880-f563-4042-92b5-4cf9dccbc2fb)

**Synthsis**

Go to verilog_files directory and invoke yosys

Once you invoke yosys, Run following commands to Synthsis dff_asyncres
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_asyncres.v
  synth -top dff_asyncres
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/d0887d1f-4349-4d60-a9de-79f61c91ce1d)

**Asynchronous set D Flip-Flop**

**Simulation**

Go to verilog_files directory where the design and test_bench are present

Run the following commands to simulate dff_async_set.v
```
iverilog dff_async_set.v tb_dff_async_set.v
./a.out
gtkwave tb_dff_async_set.vcd
```
![6](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/3d17d0a1-229d-49b2-8f1d-992f0fe608d4)



![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/8d8d9bec-f350-459d-b761-256ca2888ca7)

**Synthsis**

Go to verilog_files directory and invoke yosys

Once you invoke yosys, Run following commands to Synthsis dff_async_set
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_async_set.v
  synth -top dff_async_set
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/54670bd8-c75f-4b62-a709-50a325be135c)

## Interesting optimisations 
 **mult_2.v**
``` v
module mul2 (input [2:0] a, output [3:0] y);
	assign y = a * 2;
endmodule
```
**Synthsis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog mult_2.v
synth -top mul2
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr mul2_netlist.v
!gvim mul2_netlist.v
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/4928faea-f84e-47d1-9699-53fd8e531f4d)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/f3682614-8642-4d18-922c-0d9e85c0aa7e)

**mul2_netlist.v**
``` v
/* Generated by Yosys 0.32+51 (git sha1 6405bbab1, gcc 12.3.0-1ubuntu1~22.04 -fPIC -Os) */

module mul2(a, y);
  input [2:0] a;
  wire [2:0] a;
  output [3:0] y;
  wire [3:0] y;
  assign y = { a, 1'h0 };
endmodule
```

 **mult_8.v**
``` v
module mult8 (input [2:0] a , output [5:0] y);
	assign y = a * 9;
endmodule
```
**Synthsis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog mult_8.v
synth -top mult8
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr mult8_netlist.v
!gvim mult8_netlist.v
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/d5e08dad-a78b-4782-940d-a2502577ee15)

![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/c19b157a-0afc-4434-b828-9fe57c73e90f)


**mult8_netlist.v**
``` v
/* Generated by Yosys 0.32+51 (git sha1 6405bbab1, gcc 12.3.0-1ubuntu1~22.04 -fPIC -Os) */

module mult8(a, y);
  input [2:0] a;
  wire [2:0] a;
  output [5:0] y;
  wire [5:0] y;
  assign y = { a, a };
endmodule
```
[Back to COURSE](https://github.com/Vinodkumar8318/Ppes_asic_class/tree/main#course)


</details>
<details>
<summary>DAY 5 : Combinational and sequential Optimizations </summary>
<br>
	
# Introduction to optimizations
1. **CCombinational optimization**
    - Combinational optimization is a branch of mathematical optimization that focuses on selecting the best combination of discrete options to optimize a given function. 
    - Two methods within computational optimization are:
       + **Constant Propagation:** This technique identifies and replaces variables or expressions with their constant values, reducing redundancy in code and improving performance.
       + **Boolean Optimization:** This method simplifies boolean expressions or logic circuits by reducing the number of logical gates or terms while preserving the same logical behavior, which is useful in digital circuit design and logical reasoning.

2. **Sequential logic optimization**
    - Sequential logic optimization is the process of enhancing the performance and efficiency of digital circuits containing flip-flops and state elements.
    - Methods under this optimization umbrella include:
       + **Sequential Constant Propagation:** Identifies and propagates constant values through flip-flops to reduce redundant state transitions.
       + **State Optimization:** Reduces the number of states in finite state machines (FSMs) by merging equivalent states, simplifying the circuit.
       + **Sequential Logic Cloning:** Replicates portions of sequential logic to alleviate bottlenecks and improve circuit throughput.
       + **Retiming:** Adjusts the placement of flip-flops within a circuit to optimize timing, balance critical paths, and enhance overall performance.

# Combinational logic optimizations
**opt_check.v**
``` v
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```
**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/312e8cba-af4c-47f3-9fa8-97a567b2b0bd)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/4ae736ef-87e9-4c0c-a1d3-ecd74d550f34)


**opt_check2.v**
``` v
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```
**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog opt_check2.v
synth -top opt_check2
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/a1589c26-5120-4d92-86c3-b8501913b323)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/db473fc2-9cd8-4446-81df-7bc470307190)


**opt_check3.v**
``` v
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```
**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog opt_check3.v
synth -top opt_check3
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/1977da41-8f5a-4727-aff4-629d2efd55c8)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/28522f20-3950-4db2-ae5c-b4b73f374d93)


**multiple_module_opt.v**
``` v
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule


module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule


module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 


endmodule
```
**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog multiple_module_opt.v

synth -top multiple_module_opt
flatten
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/d4977886-10bf-4bd4-87cb-47f76d6dcb61)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/f66c3166-a21d-4372-b9ca-00145ecf414d)


# Sequential logic optimizations
**dff_const1.v**
``` v
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end

endmodule
```
**Simulate**
```
iverilog dff_const1.v tb_dff_const1.v
./a.out
gtkwave tb_dff_const1.vcd
```
![1](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/185f91b6-705f-4d86-8cfb-a44c5841f2dc)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/f3e25841-4def-4e25-8d3d-100b65359406)


**Synthesis**
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_const1.v
  synth -top dff_const1
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/b63d621e-9f7e-4027-afd4-36c1288732ec)



![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/a86fd1ac-0e9d-4e7b-8a86-89af55b337c2)


**dff_const2.v**
``` v
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end

endmodule
```
**Simulate**
```
iverilog dff_const1.v tb_dff_const1.v
./a.out
gtkwave tb_dff_const2.vcd
```
![2](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/5a4a7044-0abb-492a-95b0-0e8a4a70685c)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/cd075a4e-52c2-4751-ac7e-595fa0b4ec7a)


**Synthesis**
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_const2.v
  synth -top dff_const2
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/ad255d91-9cd5-4a09-991e-ea2aa6cb7331)



![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/9180b110-d3b5-46f1-9c3a-5bc865c1d5bd)



**dff_const3.v**
``` v
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```

**Simulate**
```
iverilog dff_const3.v tb_dff_const2.v
./a.out
gtkwave tb_dff_const3.vcd
```

![3](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/1b54b546-d173-4430-9bb1-72755a1b6a46)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/d271d98d-13b4-4a97-aa41-b6e8826c189f)

**Synthesis**
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_const3.v
  synth -top dff_const3
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/544b92bf-5ad0-4d86-a246-f72b58b675f0)



![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/3cc2d7da-a0f5-4a02-827b-b2c16d2d891f)



# Sequential optimzations for unused outputs
**counter_opt.v**
``` v
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```
**Synthesis**
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog counter_opt.v
  synth -top counter_opt
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```

![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/990f6547-62d2-4f9c-9981-bec0a848c92c)



![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/68710b8a-8804-43dd-be6d-9c5987450e4b)



**counter_opt2.v**
``` v
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = (count[2:0] == 3'b100);

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```
**Synthesis**
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog counter_opt2.v
  synth -top counter_opt
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/f26f248b-7235-440b-9da3-a6e7915b23a4)



![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/be559ba2-b100-4563-a091-c0120552da14)


[Back to COURSE](https://github.com/Vinodkumar8318/Ppes_asic_class/tree/main#course)

</details>
<details>
<summary>DAY 6 : GLS, Blocking vs Non blocking and synthsis - Simulation Mismatch </summary>
<br>

# GLS Synthesis-Simulation mismatch and Blocking Non-blocking statements
## GLS Concepts And Flow Using Iverilog
### Gate level simulation
+ Gate-level simulation is a method used in electronics design to test and verify digital circuits at the level of individual logic gates and flip-flops.
+ It's crucial for checking functionality, timing, power consumption, and generating test patterns for integrated circuits.
+ It operates at a lower abstraction level than higher-level simulations and is essential for debugging and ensuring circuit correctness.

### To perform gate-level simulation using Icarus Verilog (iverilog):

1. Write RTL code.
2. Synthesize to generate gate-level netlist.
3. Create a testbench in Verilog.
4. Compile both netlist and testbench.
5. Run simulation with compiled files.Debug and iterate as needed.
6. Perform timing analysis if necessary.
7. Generate test vectors for manufacturing tests.

## Synthesis-Simulation mismatch
+ Synthesis-simulation mismatch is when there are differences between how a digital circuit behaves in simulation at the RTL level and how it behaves after gate-level synthesis.
+ This can occur due to optimization, clock domain issues, library differences, or other factors.
+ To address it, ensure consistent tool versions, check synthesis settings, debug with simulation tools, and follow best practices in RTL coding and design.
+ Resolving these mismatches is crucial for reliable hardware implementation.

## Blocking And Non-Blocking Statements
**Blocking Statements**
+ Blocking statements execute sequentially in the order they appear within a procedural block or always block.
+ When a blocking assignment or operation is encountered, the simulation halts and waits for it to complete before moving on to the next statement.
+ Blocking assignments are typically used to describe combinational logic, where the order of execution doesn't matter, and each assignment depends on the previous one.

**Example (Blocking Assignment):** `a = b + c; // b and c must be known before calculating 'a'`

**Non-Blocking Statements**
+ Non-blocking statements allow concurrent execution within a procedural block or always block, making them suitable for describing synchronous digital circuits.
+ When a non-blocking assignment or operation is encountered, the simulation does not wait for it to complete. Instead, it schedules the assignments to occur in parallel.
+ Non-blocking assignments are typically used to model sequential logic, like flip-flops and registers, where parallel execution is required.

**Example (Non-Blocking Assignment):** 
```
always @(posedge clk)
  begin
    b <= a; // Concurrently scheduled assignment
    c <= b; // Concurrently scheduled assignment
  end
```
## Caveats With Blocking Statements
**Caveats with blocking statements in hardware description languages like Verilog include:**
+ **Sequential Execution:** Blocking statements execute sequentially, which may not accurately represent concurrent hardware behavior in the design.

+ **Order Dependency:** The order of blocking statements can affect simulation results, leading to race conditions or unintended behavior.

+ **Combinational Logic Only:** Blocking statements are primarily used for modeling combinational logic, making them less suitable for sequential or synchronous logic.

+ **Limited for Testbenches:** In testbench code, excessive use of blocking statements can lead to simulation race conditions that don't reflect real-world hardware behavior.

+ **Initialization Issues:** In some cases, initializing variables with blocking assignments can lead to unexpected results due to order-dependent initialization.

To mitigate these issues, designers often use non-blocking statements for modeling sequential logic and adopt good coding practices to minimize order dependencies and improve code clarity.


# Labs on GLS and Synthesis-Simulation Mismatch


**ternary_operator_mux.v**
``` v
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
endmodule
```
**RTL Simulation**
```
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```

![4](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/f8986bb2-ec81-4725-9e3c-7e2573e038e3)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/bcfc1cff-8cd4-4b4f-b005-f32c85f5eb8b)


**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr ternary_operator_mux_netlist.v
show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/1c75a33d-7f11-4ae0-a3cd-93900beb5cd3)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/31614d96-b38d-4c3e-8cd7-634715c91792)



**GLS**
To to Gate level simulation, Invoke iverilog with verilog modules
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_netlist.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```

![5](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/d875ffe3-a28c-4347-aac0-08251509bd8b)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/a4efbdd2-eadb-49af-a121-c5e92d421234)



**bad_mux.v**
``` v
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
**RTL Simulation**
```
iverilog bad_mux.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```

![6](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/1b0c0c0f-e405-4f1e-bb5a-ea0acef119db)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/652704cd-47e4-4037-a2e5-260ec79b7a7d)


**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_mux.v
synth -top bad_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr bad_mux_netlist.v
show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/63cfa473-8ce7-4650-9a9f-14b13641a847)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/e4dbcfcb-de35-4441-8dfc-66e02c0c8056)


**GLS**
To to Gate level simulation, Invoke iverilog with verilog modules
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_netlist.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```

![7](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/42038072-8e1d-4849-9671-024c60e0cd50)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/fc348231-d5b4-43f8-8b10-a3f8ecef9aed)




# Labs on synth-sim mismatch for blocking statement
**blocking_caveat.v**
``` v
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```
**RTL Simulation**
```
iverilog blocking_caveat.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```

![8](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/526c538c-f136-4956-9654-0ecdd88e8890)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/565826fe-9abc-4c61-8aeb-9e0d7b920549)


**Synrhesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog blocking_caveat.v
synth -top blocking_caveat
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr blocking_caveat_netlist.v
show
```
![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/d60080e7-5467-4e82-9097-f4b1a49fa6a8)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/d10c1f53-9138-4678-a0e9-672f87023c25)


**GLS**
To to Gate level simulation, Invoke iverilog with verilog modules
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat_netlist.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```

![9](https://github.com/madhumadhu1318/PES_ASIC_CLASS/assets/90201844/0f281217-77bd-45d4-9fc7-5141c11e8b08)


![image](https://github.com/Vinodkumar8318/pes_asic_class/assets/142583979/9efe2896-c476-490e-8c9e-e0c8bd464e18)


[Back to COURSE](https://github.com/Vinodkumar8318/Ppes_asic_class/tree/main#course)










