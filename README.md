# LLVM-Cross-Compiler-for-RISC-V-on-x86
## RISC-V Toolchain Setup and Sanity Test
This guide will walk you through setting up a RISC-V toolchain using the GNU toolchain and LLVM, followed by a sanity test to verify your installation.
### Prerequisites
    ''' 
sudo apt-get install autoconf automake autotools-dev curl python3 python3-pip libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build git cmake libglib2.0-dev libslirp-dev
    sudo apt-get -y install \
        binutils build-essential libtool texinfo \
        gzip zip unzip patchutils curl git \
        make cmake ninja-build automake bison flex gperf \
        grep sed gawk python-is-python3 bc \
        zlib1g-dev libexpat1-dev libmpc-dev \
        libglib2.0-dev libfdt-dev libpixman-1-dev

