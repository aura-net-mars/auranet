# Rust Setup for Pico 2/ RP2350 #

no-std code planned

After installing Rust on your Dev Computer, you need the matching toolchain for the RP2350 Kernel.

### Installation of the Target ###

```rustup target add thumbv8m.main-none-eabihf```

### Creating a Test Project ###
We are using ```rp-hal``` ekosystem as an abtraction layer for the RP2350 to create a blinking LED program

```
cargo new pico-blink --bin
cd pico-blink
```

```
pico-blink/
├── .cargo/
│   └── config.toml   
│   └── main.rs      
├── build.rs          
├── Cargo.toml         
└── memory.x
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

### Cargo.toml ###
```
[package]
name = "pico-blink"
version = "0.1.0"
edition = "2021"

[dependencies]
cortex-m = "0.7.7"
cortex-m-rt = "0.7.3"
embedded-hal = "1.0.0"
panic-halt = "1.0.0"
rp235x-hal = { version = "0.3.0", features = ["rt", "critical-section-impl", "binary-info"] }
```

### main.rs ###
```
#![no_std]
#![no_main]

use embedded_hal::delay::DelayNs;
use embedded_hal::digital::OutputPin;
use panic_halt as _;
use rp235x_hal as hal;

// Importend for the RP2350 Kernel
#[link_section = ".start_block"]
#[used]
pub static IMAGE_DEF: hal::block::ImageDef = hal::block::ImageDef::secure_exe();

#[hal::entry]
fn main() -> ! {
    // Hardware-Register übernehmen (wie in C die Basis-Adressen)
    let mut pac = hal::pac::Peripherals::take().unwrap();
    let mut watchdog = hal::Watchdog::new(pac.WATCHDOG);

    // Taktgeber (Clocks) mit 12 MHz externem Quarz starten
    let clocks = hal::clocks::init_clocks_and_plls(
        12_000_000u32,
        pac.XOSC,
        pac.CLOCKS,
        pac.PLL_SYS,
        pac.PLL_USB,
        &mut pac.RESETS,
        &mut watchdog,
    )
    .unwrap();

    // Timer für die Pause (Delay) einrichten
    let mut timer = hal::Timer::new_timer0(pac.TIMER0, &mut pac.RESETS, &clocks);

    // GPIO-Pins freischalten
    let sio = hal::Sio::new(pac.SIO);
    let pins = hal::gpio::Pins::new(
        pac.IO_BANK0,
        pac.PADS_BANK0,
        sio.gpio_bank0,
        &mut pac.RESETS,
    );

    // Onboard-LED (GPIO 25) als Ausgang konfigurieren
    let mut led_pin = pins.gpio25.into_push_pull_output();

    // Endlosschleife (entspricht while(1) in C)
    loop {
        led_pin.set_high().unwrap();
        timer.delay_ms(500);
        led_pin.set_low().unwrap();
        timer.delay_ms(500);
    }
}
```

### biuld.rs ###
```
use std::env;
use std::fs::File;
use std::io::Write;
use std::path::PathBuf;

fn main() {
    let out = &PathBuf::from(env::var_os("OUT_DIR").unwrap());
    File::create(out.join("memory.x"))
        .unwrap()
        .write_all(include_bytes!("memory.x"))
        .unwrap();
    println!("cargo:rustc-link-search={}", out.display());
    println!("cargo:rerun-if-changed=memory.x");
}
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
