# Rust Setup for Pico 2/ RP2350 #

no-std code planned

After installing Rust on your Dev Computer, you need the matching toolchain for the RP2350 Kernel.

### Installation of the Target ###

```rustup target add thumbv8m.main-none-eabihf```

### Creating a Test Project ###
We are using ```rp-hal``` ekosystem as an abtraction layer for the RP2350 to create a 

```
cargo new pico-blink --bin
cd pico-blink
```

### Creating some files ###
```
mkdir .cargo
touch .cargo/config.toml
touch memory.x
```

#### config.toml ####
```
[target.'cfg(all(target_arch = "arm", target_os = "none"))']
runner = "elf2uf2-rs deploy -t -s --family rp2350-arm-s"

rustflags = [
  "-C", "linker=flip-link",
  "-C", "link-arg=-Tlink.x",
  "-C", "link-arg=-nostartfiles",
]

[build]
target = "thumbv8m.main-none-eabihf"
```

#### memory.x ####
```
MEMORY
{
  FLASH : ORIGIN = 0x10000000, LENGTH = 4096K
  RAM   : ORIGIN = 0x20000000, LENGTH = 520K
}

SECTIONS {
  .start_block : ALIGN(4)
  {
    __start_block_addr = .;
    KEEP(*(.start_block));
  } > FLASH
} INSERT AFTER .vector_table;
```

### Building and transfering the code on the Pico ###

The exact step-by-step procedure:

1. Press and hold the BOOTSEL button: Press and hold the small white button on your Raspberry Pi Pico 2.
2. Plug in the USB cable: Connect the Pico 2 to your MacBook using a data cable.
3. Release the BOOTSEL button: Once the cable is connected, you can release the button. The Pico will now appear on your Mac as an external USB drive named RP2350.
4. Execute the terminal command: Open your terminal (within the project folder) and initiate the process:

```
cargo run
```

The onboard LED should blink!
