# RISC-V_MYTH_Workshop - Building Risc-V core

An informative workshop and project on how to build Risc-V CPU core organized by VSD Corp and Redwood EDA. This workshop helped me learn the basics of RISC-V core and its RV32I instruction set. This workshop was a day by day learning of RISC-V concepts and its practical experience in Open-source tools. Here, we used languages like C, Assembly Language Programming for software implementation and TL-Verilog for HDL implementation. The tools used were: Spike and Makerchip IDE open-source platform developed by Redwood EDA.

# Contents

[1. Introduction to RISC-V](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/README.md#1-introduction-to-risc-v)

[2. Day 1: Instruction set architecture](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava#2-day-1-instruction-set-architecture)

[3. Day 2: Application Binary Interface and RISC-V specifications](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava#3-day-2-application-binary-interface-and-risc-v-specifications)

[4. Day 3: Digital logic with TL-Verilog in Makerchip IDE](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava#4-day-3-digital-logic-with-tl-verilog-in-makerchip-ide)

[5. Day 4: Coding a RISC-V CPU subset](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava#5-day-4-coding-a-risc-v-cpu-subset)

[6. Day 5: Pipelining and completing your CPU](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava#6-day-5-pipelining-and-completing-your-cpu)

[7. Acknowledgements](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava#7-acknowledgements)

# 1. Introduction to RISC-V

RISC-V is an open standard instruction set architecture (ISA) based on established reduced instruction set computer (RISC) principles. Unlike most other ISA designs, the RISC-V ISA is provided under open source licenses that do not require fees to use. Notable features of the RISC-V ISA include a load–store architecture, bit patterns to simplify the multiplexers in a CPU, IEEE 754 floating-point, a design that is architecturally neutral, and placing most-significant bits at a fixed location to speed sign extension. In this workshop we learnt the basic concepts of RISC-V architecture and RISC instruction set. We also did hands on C programming, Assembly Language and TL-verilog.

# 2. Day 1: Instruction set architecture

Day 1 was all about the introduction to RISC-V ISA and GNU Compiler. We were intrduced to VSD-IAT platform developed by jnaapti. Initially, the oration was given on the overall flow of the architecture. It was like how OS is used as interface between user and the machine. Then the higher level language like C/C++ through compiler is converted to Instructions. Instruction act as an abstract interface between software(program) and hardware. This Interface is called as instruction Set Architecture(ISA). This instruction is converted to Assembly Programming via assembler then into machine(binary format). It is then sent to RTL coding and then to Physical Design Implementation. 

The various types of instructions used in RISC-V :

- **RV64I:** Base Integer Instruction Set, 64-bit. Eg - lui, sd, add, jalr, ld etc.
- **RV64M:** Standard Multiply Extension for Integer Multiplication and Division. Eg - mulw, divw etc.
- **RV64IM:** If any CPU performs base as well as multiply and division it is Base and Mutiple extension. 
- **RV64F and RV64D:** Single and double precision floating point extensions. Eg - fadd.s, fcvt.d.s, fmv.x.d etc

If it includes all the above then: **RV64IMFD**

GNU Compiler is required for RISC-V and simulator. We need to set it up on Ubuntu which is installed in VM Virtual Box for Windows OS. Here we installed GNU compiler and Spike siulator which will be required for debugging as well.

# Labs

Testing with simple codes: Running basic C code to find unsigned highest interger

**1. To compile using RISC-V GNU compiler:**
```
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o unsignedhighest.o unsignedhighest.c
```

**2. To see AL code or dissembled file:**
```
riscv64-unknown-elf-objdump -d unsignedhighest.o | less
```

**3. To open debugger:**
```
spike -d pk unsignedhighest.o
```

**4. To decrease the number of instructions we use Ofast instead of O1 in the above command which is used to compile RISC-V:**
```
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o unsignedhighest.o unsignedhighest.c
```

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day%201/unsigned_highest.png)

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day%201/Signed_higest_lowest.png)

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day%201/printf_subroutine.png)

**There was also description of how to represent integer numbers and sizes**

8 bits = 1 byte

32 bits = 4 bytes = 1 word

64 bits = 8 bytes = 2 word(double word)

- Integer representation:

  - Total number of patterns represented by RV64 = 2^64
  - RISC=V 64 double words fr unsigned or positive numbers can be represented from: 0 to 2^64 - 1
  - For all the integers:
    - Positive numbers: 0 to 2^63-1
    - Negative numbers: -2^63 to -1

**Data Types, there respective sizes and format specifier**

| Data types | Memory | Format Specifier |
| --- | --- | --- |
| Unsigned int |    4   |        %u        |
|     int    |    4   |        %d        |
| unsigned long long int | 8 |   %llu      |
| long long int | 8 | %lld |
 
# 3. Day 2: Application Binary Interface and RISC-V specifications

An application binary interface (ABI) is an interface between two binary program modules; often, one of these modules is a library or operating system facility, and the other is a program that is being run by a user. It is kind of similar to Application Program Interface(API) which provides an interaction of Application programs with standard libraries. There are two main parts of ISA: User and System ISa and User ISA. User ISA can directly interact with hardware.

RISC-V can access hardware resources through 32 registers each having different specifications. These registers varies from x(0) to x(31). Length of registers can be defined through : XLEN. 

-XLEN = 32 bit (RV32)

-XLEN = 64 bit (RV64)

There are two ways in which data can be loaded into the registers:
1. Load data directly into registers.
2. Load data to memory first and then to registers.

RISC-V belongs to **little-endian** memory addressing system which is **byte addreseable**. Hence,

Address of 1st double word = M[0]

Address of 2nd double word = M[8]

Address of 3rd double word = M[16]........ so on
                 
**Register calling convention for RISC-V**

| Register | ABI Name | Usage | Saver |
| --- | --- | --- | --- |
| X0 | zero | Hard-wired zero | - |
| X1 | ra | return address | caller |
| X2 | sp | stack pointer | callee |
| X3 | gp | global pointer | - |
| X4 | tp | thread pointer | - |
| X5-7 | t0-2 | temporaries | caller |
| X8 | s0/fp | saved register/frame pointer | callee |
| X9 | s1 | saved register |callee|
| X10-11 | a0-1 | function arguments/return values | caller |
| X12-17 | a2-7 | function arguments | caller |
| X18-27 | s2-11 | saved registers | callee |
| X28-31 | t3-6 | temporaries | callee|

There three instructions which come under RV64I i.e base integer instructions: 

-R-type: Instructions operating on registers are called R-type instructions.

-I-type: Instructions operating on registers and immediate values are I-type instructions.

-S-type: Used only for storing operations.

5 bits are used to represent the registers. Hence:

Total no. of registers = 2^5 = 32 registers

Therefore 32 int registers in RISC-V architecture (0-31).

# Labs

**Using ABI to test while calling code 1to9_custom.c and load.S**

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day2/sum%20of%20numbers%201to9.png)

**Finding sum using RISCV using iverilog**

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day2/Finding%20sum%20using%20RISCV%20using%20iverilog.png)


# 4. Day 3: Digital logic with TL-Verilog in Makerchip IDE

An introduction to TL-Verilog was provided and some basic concepts of Digital logic was brushed up. TL-Verilog stands for Transactional Level Verilog and is an updated or you can say extension of System Verilog and proves to be very efficient, bug free and easy to learn all at the same time. Using TL-Verilog for HDL implementation was very much easy and efficient. The main advantage of using TL-verilog is pipelining. It made the use of pipeline very easy without any bugs. 

## PROS:

- Length of the code is reduced and hence less bug prone
- Easy to pipeling. 
- TL-Verilog is good for architectural and synthesizable verification models. too.
- Easy for staging without use of Flip Flops i.e they are already implied from the context.
- Easy debugging and automated clock gating signal is very useful.

This day the most difficult day as it invloved all the basic concepts which would be used in the next two days for building thr RISC-V core. So understanding the concepts was paramount. It took a lot of time infact the whole day but it was necessary. We started off with a very simple logic of OR logic, mux, counter, fibonacci series etc and ended on three important designs which are combinational and sequential calculator and cycle calculator.

# Labs

## Combinational Calculator:

The design of calculator where we designed the addition, subtraction, multiplication and division using 2:1 mux

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day3_5/combinational%20calculator.png)

## Sequential Calculator:

A real calculator remembers the last result, and uses it for the next calculation, so we used the sequential logic were flip flops came into the picture without specifying them (the beauty of TL-Verilog).

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day3_5/sequential%20calculator.png)

## Cycle Calculator:

With the use of pipelining we designed the cycle calculator where we included both calculator as well as counter.

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day3_5/Cycle_calculator.png)

# 5. Day 4: Coding a RISC-V CPU subset

Day 3 was very hectic due to the basic concepts. However, I can say that it paid off as it was the first step towards a successfull building of the core. On this day, learning was a bit fast and smooth. We learnt the basic arhcitecture of the RISC-V core and the implementation plan for the same. The plan included with:

- Program Counter (Next PC, Fetch)
- Imem-Rd (Instruction Memory)
- Instruction Decoder
- Register File Read
- Arithmatic Logic Unit (ALU)
- Register File Write
- Branch

## Instruction decoder:

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day3_5/full_instruction%20decode.png)

## After implementing all the above parameters for building a core here is the final lab of the day:

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day3_5/implementing%20all%20the%20parameters.png)

We tested the core using testbench to ensure that the code which we designed was working properly. Here is the code:

```
*passed = |cpu/xreg[10]>>5$value == (1+2+3+4+5+6+7+8+9);
```

# 6. Day 5: Pipelining and completing your CPU

Here comes the final day of the workshop where what we did was to piepline the CPU. Based on the water fall diagram, we provided the pipeline concept to the core. In doing so, we encuntered some hazards:

- Read follow by write
- Branching hazard

For which we resolved the issue by using register file bypass method and branching method where instead of going back 1 stage we went back 2 stages. We tested the code with the same manner as to load and store assembly language code and also imbibed JUMP method.

## Final RISC-V CPU core:

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day3_5/RISC-V%20core.png)

## Jumps Solution:

![alt text](https://github.com/RISCV-MYTH-WORKSHOP/risc-v-myth-workshop-august-mukuljava/blob/master/Day3_5/jumps_solution.png)

# 7. Acknowledgements

- [Kunal Ghosh](https://github.com/kunalg123), Co-founder (VSD Corp. Pvt. Ltd)
- [Steve Hoover](https://github.com/stevehoover), Founder, Redwood EDA
- [Shivam Potdar](https://github.com/shivampotdar), GSoC 2020 Student,FOSSi Foundation
- [Vineet Jain](https://github.com/vineetjain07), GSoC 2020 Student,FOSSi Foundation
