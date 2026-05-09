# How to compile verilator

This is done to work with vga-playground

```bash
cd verilator 

# Get flex
wget https://github.com/westes/flex/releases/download/v2.6.4/flex-2.6.4.tar.gz
cd flex-2.6.4
emconfigure ./configure --host i686-pc-linux-gnu
# DO not make
cd ..

# make clean
export EXPORTED_RUNTIME_METHODS=addRunDependency,removeRunDependency,FS_createPath,FS_createDataFile,FS_createPreloadedFile,FS_createLazyFile,FS_createDevice,FS_unlink,FS,callMain,writeStackCookie,checkStackCookie
export SFLAGS="-s EXPORTED_RUNTIME_METHODS=$EXPORTED_RUNTIME_METHODS -s INVOKE_RUN=0 -s MAIN_MODULE=0 -s MODULARIZE=1 -s EXPORT_NAME=verilator_bin -s EXPORT_ES6=1 -s EXIT_RUNTIME=0 -s ALLOW_MEMORY_GROWTH=1"
export CFLAGS="-I$(pwd)/flex-2.6.4/src $SFLAGS"
export CPPFLAGS="-I$(pwd)/flex-2.6.4/src $SFLAGS"
export LDFLAGS="$SFLAGS"
emconfigure ./configure --prefix=/ --host i686-pc-linux-gnu
emmake make -j20 verilator_bin

# Copy to vga-playground
cp bin/verilator_bin ../tt_maze/test/vga-playground/src/verilator/verilator_bin.js
cp bin/verilator_bin.wasm ../tt_maze/test/vga-playground/src/verilator/verilator_bin.wasm
# To debug
cp bin/verilator_bin_dbg ../tt_maze/test/vga-playground/src/verilator/verilator_bin.js
cp bin/verilator_bin_dbg.wasm ../tt_maze/test/vga-playground/src/verilator/verilator_bin.wasm
cp include/verilated_std.sv ../tt_maze/test/vga-playground/src/verilator/
cp include/include/verilated_std_waiver.vlt ../tt_maze/test/vga-playground/src/verilator/


# Compile the test

em++ -s EXPORTED_RUNTIME_METHODS=addRunDependency,removeRunDependency,FS_createPath,FS_createDataFile,FS_createPreloadedFile,FS_createLazyFile,FS_createDevice,FS_unlink,FS,callMain,writeStackCookie,checkStackCookie -s INVOKE_RUN=0 -s MAIN_MODULE=0 -s MODULARIZE=1 -s EXPORT_NAME=verilator_bin -s EXPORT_ES6=1 -s EXIT_RUNTIME=0 -s FORCE_FILESYSTEM=1 -s EXPORTED_FUNCTIONS=_main -s RUNTIME_LOGGING=2 -s ASSERTIONS=2 -s -Xlinker -gc-sections -o test.js test.cpp

cp test.js ../tt_maze/test/vga-playground/src/verilator/verilator_bin.js
cp test.wasm ../tt_maze/test/vga-playground/src/verilator/verilator_bin.wasm

```
Need to encapsulate the main function. Modify Verilator.cpp at the end:

```c++
#include <emscripten.h>
extern "C" {
  EMSCRIPTEN_KEEPALIVE
  int main(int argc, char** argv) {...}
}
```
