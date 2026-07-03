cmake_minimum_required(VERSION 3.18)
project(MultiConsoleWasmCore CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Optimize execution loops while stripping code bloat for tiny sizes
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -flto -fno-rtti -fno-exceptions")

# Force execution outputs explicitly optimized for WASM runtimes
set(EMSCRIPTEN_LINK_FLAGS
    "-s WASM=1"
    "-s INITIAL_MEMORY=134217728" # Allocate 128MB base buffer array block
    "-s ALLOW_MEMORY_GROWTH=1"   # Enable dynamic growth to safely buffer heavy ISO / CHD arrays
    "-s WASM_BIGINT=1"           # Expose high-speed 64-bit integer processing
    "-s EXPORTED_FUNCTIONS=['_allocate_dc_boot','_allocate_dc_flash','_allocate_dc_elf','_notify_elf_omission','_allocate_ps1_bios','_allocate_ps2_bios','_allocate_n64_bios','_allocate_n64_cartridge','_allocate_dc_game_media','_allocate_ps1_game_media','_allocate_ps2_game_media','_validate_and_lock_firmware']"
    "-s EXPORTED_RUNTIME_METHODS=['ccall','cwrap','WebAssembly']"
)

string(REPLACE ";" " " EMSCRIPTEN_LINK_FLAGS_STR "${EMSCRIPTEN_LINK_FLAGS}")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${EMSCRIPTEN_LINK_FLAGS_STR}")
set(CMAKE_EXECUTABLE_SUFFIX ".js")

add_executable(multi_core_loader 
    src/main.cpp
)
