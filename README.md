# LLVM-Cross-Compiler-for-RISC-V-on-x86
## RISC-V Toolchain Setup and Sanity Test
This guide will walk you through setting up a RISC-V toolchain using the GNU toolchain and LLVM, followed by a sanity test to verify your installation.
## Installation

To install the necessary dependencies, run the following commands:

```bash
sudo apt-get update
sudo apt-get -y dist-upgrade

sudo apt-get install autoconf automake autotools-dev curl python3 python3-pip libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build git cmake libglib2.0-dev libslirp-dev
sudo apt-get -y install \
    binutils build-essential libtool texinfo \
    gzip zip unzip patchutils curl git \
    make cmake ninja-build automake bison flex gperf \
    grep sed gawk python-is-python3 bc \
    zlib1g-dev libexpat1-dev libmpc-dev \
    libglib2.0-dev libfdt-dev libpixman-1-dev th
`````

### Step 1: Clone the Repositories
```bash
git clone https://github.com/riscv/riscv-gnu-toolchain
mkdir _install
export PATH=`pwd`/_install/bin:$PATH
hash -r
git clone https://github.com/llvm/llvm-project.git riscv-llvm
`````

### Step 2: Build the GNU Toolchain
```bash
pushd riscv-gnu-toolchain
./configure --prefix=`pwd`/../_install --enable-multilib
make linux -j`nproc`
make -j`nproc` build-qemu
popd
`````

### Step 3: Build LLVM
```bash
pushd riscv-llvm
ln -s ../../clang llvm/tools || true
mkdir _build
cd _build
cmake -G Ninja -DCMAKE_BUILD_TYPE="Release" \
    -DBUILD_SHARED_LIBS=True -DLLVM_USE_SPLIT_DWARF=True \
    -DCMAKE_INSTALL_PREFIX="../../_install" \
    -DLLVM_OPTIMIZED_TABLEGEN=True -DLLVM_BUILD_TESTS=False \
    -DDEFAULT_SYSROOT="../../_install/sysroot" \
    -DLLVM_DEFAULT_TARGET_TRIPLE="riscv64-unknown-linux-gnu" \
    -DLLVM_TARGETS_TO_BUILD="RISCV" \
    ../llvm
cmake --build . --target install
popd
`````

### Sanity Test for RISC-V LLVM
Step 1: Compile and Run a Simple C Program
Create a simple hello.c program:
```bash
cat >hello.c <<END
#include <stdio.h>

int main(){
    printf("Hello RISCV!\n");
    return 0;
}
END
`````

### Compile the program:

```bash
clang -O -c hello.c 
riscv64-unknown-linux-gnu-gcc hello.o -o hello -march=rv64gc -mabi=lp64d
qemu-riscv64 hello
`````

### Step 2: Test with the llama Repo
Compile your C program in the llama repo
```bash
clang -O3 -c run.c
riscv64-unknown-linux-gnu-gcc run.o -o run -march=rv64gc -mabi=lp64d -lm
`````

### Common Issues
- LD_LIBRARY_PATH Error
- If you encounter the following error:
- configure: error:  *** LD_LIBRARY_PATH shouldn't contain the current directory when *** building glibc. Please change the environment variable *** and run configure again.
```bash
export LD_LIBRARY_PATH=/usr/local/cuda-11.7/lib64
`````

### Relocation Error
- If you encounter errors similar to:
```bash
riscv64-unknown-linux-gnu-gcc hello.o -o hello -march=rv64gc -mabi=lp64d
`````

### Error shows

/data/HD_riscv_vsison2/riscv/_install/bin/../lib/gcc/riscv64-unknown-linux-gnu/13.2.0/../../../../riscv64-unknown-linux-gnu/bin/ld: hello.o: Relocations in generic ELF (EM: 62)
/data/HD_riscv_vsison2/riscv/_install/bin/../lib/gcc/riscv64-unknown-linux-gnu/13.2.0/../../../../riscv64-unknown-linux-gnu/bin/ld: hello.o: Relocations in generic ELF (EM: 62)
/data/HD_riscv_vsison2/riscv/_install/bin/../lib/gcc/riscv64-unknown-linux-gnu/13.2.0/../../../../riscv64-unknown-linux-gnu/bin/ld: hello.o: error adding symbols: file in wrong format
collect2: error: ld returned 1 exit status


### This indicates that Clang is using the x86 architecture. Ensure that the sysroot of RISC-V Clang is in the path by running:
```bash
export PATH=`pwd`/_install/bin:$PATH
hash -r
`````











