# RISC-V-using-MYTH

### Local machine Installation.
* Info ->
https://github.com/kunalg123/riscv_workshop_collaterals/blob/master/run.sh
 
    sudo apt-get install git vim -y
    sudo apt-get install autoconf automake autotools-dev curl libmpc-dev         
    libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo     
    gperf libtool patchutils bc zlib1g-dev git libexpat1-dev gtkwave -y
    cd
    pwd=$PWD
    mkdir riscv_toolchain
    wget "https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz"
    tar -xvzf riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14.tar.gz 
    export PATH=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:$PATH
    sudo apt-get install device-tree-compiler -y
    git clone https://github.com/riscv/riscv-isa-sim.git
    cd riscv-isa-sim/
    mkdir build
    cd build
    ../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14
    make
    sudo make install
    cd $pwd/riscv_toolchain
    git clone https://github.com/riscv/riscv-pk.git
    cd riscv-pk/
    mkdir build
    cd build/
    ../configure --prefix=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14 --host=riscv64-unknown-elf
    make
    sudo make install
    export PATH=$pwd/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/riscv64-unknown-elf/bin:$PATH
    cd $pwd/riscv_toolchain
    git clone https://github.com/steveicarus/iverilog.git
    cd iverilog/
    git checkout --track -b v10-branch origin/v10-branch
    git pull 
    chmod 777 autoconf.sh 
    ./autoconf.sh 
    ./configure 
    make
    sudo make install 

# Day 1

## Introduction
* Overall instruction flow of any CPU -> ISA to RTL to Layout.
* Leafpad is used to edit the .c files.
* GCC is used as the C compiler
* The GNU Compiler Collection is an optimizing compiler produced by the GNU Project supporting various programming languages, hardware architectures and operating systems. The Free Software Foundation distributes GCC as free software under the GNU General Public License.
* a.out is the default executable name generated by the gcc.
* A sample program ->

![image](https://user-images.githubusercontent.com/72557903/199459925-6774e5a0-8693-4200-8269-590e454e81f4.png)
![image](https://user-images.githubusercontent.com/72557903/199460130-e94556d3-9355-4103-87d9-bbd652182360.png)
* *mv,ret* -> Also called pseudo-instructions.

* Disassembler ->A program for converting machine code into a low-level symbolic language.
* -**O1** option
![image](https://user-images.githubusercontent.com/72557903/199468809-03bdf650-234a-4638-b0b2-d1e75b5c6583.png)

* Main section in the assembly code.
![image](https://user-images.githubusercontent.com/72557903/199469644-284f5e6a-5fa3-4d58-9967-c8684db8822b.png)

* Byte-addressing is used here. Each intructions are spaced by 4.
* We see that there are 15 instructions in the 'main' section.
* -**Ofast** option reduces the number of instructions from 15 to 12.
![image](https://user-images.githubusercontent.com/72557903/199475224-fa36f5d9-2641-4013-9913-6aa8b61a9ce5.png)

* -d option for debugger
* pk is the proxy kernel. It can allow a regular linux application to run on a bare metal target simulator by providing a linux startup environment and emulating linux system calls.
* bbl is the berkely boot loader. It is code meant to be run at system start up which then can load a linux kernel or some other OS kernel or the proxy kernel.
* pc -> Program counter
* sp -> Stack pointer
* *lui* -> load upper limit instruction.

![image](https://user-images.githubusercontent.com/72557903/199481112-4d248672-2331-4cdf-9553-543d9e34196f.png)
![image](https://user-images.githubusercontent.com/72557903/199480064-309c4264-c0a1-4128-b34d-c315e4cab19c.png)

### Integer number representation.

![image](https://user-images.githubusercontent.com/72557903/199486642-521d3937-6ac4-4036-b3b3-2013585a431c.png)

* Number of patterns that can be represented by RISCV64 is 2^64. 
* For unsigned numbers ->
* Lowest number that can be represented is (0)h
* Maximum nnumber that (FFFF...FF)h

* For signed numbers ->
* As the standard, we use the twos compliment for our representation of signed numbers.
* Ranges from (-2^63) to (2^63-1)
* Data types ->
![image](https://user-images.githubusercontent.com/72557903/199522646-87c62d64-bcc9-4abb-8e10-d76aeef427a0.png)

# DAY 2

## Application binary interface (ABI)

![image](https://user-images.githubusercontent.com/72557903/199526433-240a2ff0-568f-4301-b355-172b63b155a5.png)

* Why are there 64-bit for registers in RISCV64?
* Memory allocation for double words -> 
* We can load the value to the registers from the memory and directly as well.
* RISC-V is little-endian (Little-endian is an order in which the "little end" (least significant value in the sequence) is stored first.)
* Each memory location is 8-bits wide, so to store a doubleword we need 8 memory locations. So consecutive doublewords will be stored in m(0),m(8),m(16), and so on.
* *ld* is used to load contents from memory to the 64-bit register.
* *add* is used to add contents of two registers and store it in the final register.
* Register sizes is 64 bit in a 64 bit RISCV but the instruction size is still 32 bit.
* To write the data back to memory, we use the *sd* opcode.

![image](https://user-images.githubusercontent.com/72557903/199654148-d8de5aa3-c2c0-4232-ade9-c89712fb5f7a.png)

* *In intel architecture, 8bytes is a quadword while in RISC-V architecture, 8bytes is doubleword and hence the instruction ld is load doubleword which loads 64bits.

* *Registers hold the operands or instruction that CPU is currently processing. Memory holds the instructions and the data that the currently executing program in CPU requires. Register holds the small amount of data around 32-bits to 64-bits. Memory of the computer can range from some GB to TB.
* For lots of info on RISC-V ->
* https://riscv.org/wp-content/uploads/2017/05/riscv-spec-v2.2.pdf

* These instructions that perform operations on unsigned integers are part of the RV64I.
* These have subtypes ->
* R-type -> Instructions that perform operations on registers.
* I-type -> Immediate + Registers.
* S-type -> ...

![image](https://user-images.githubusercontent.com/72557903/199656991-da502d96-1739-4814-a891-36ae37c9b833.png)


* A 1 to 9 counting program.
* Main program ->
![image](https://user-images.githubusercontent.com/72557903/199661932-a158b0a0-b5fc-49b2-be7f-acb07489035e.png)

* Load function (load.S)
![image](https://user-images.githubusercontent.com/72557903/199663108-da0b7261-c5d0-40d3-9c25-8fdc1960e372.png)

* After using RISCV GCC
![image](https://user-images.githubusercontent.com/72557903/199669818-37907876-af7e-4d0d-bc2b-90e73f6d094b.png)
![image](https://user-images.githubusercontent.com/72557903/199670056-b5a41458-1eb9-4142-936d-3718148db233.png)

### Basic verification flow using iverilog.

*

