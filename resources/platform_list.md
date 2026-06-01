# PlatformIO 支持平台列表

## 嵌入式平台

| 平台 ID | 芯片系列 | 架构 | 说明 |
|---------|---------|------|------|
| `atmelavr` | ATmega328P, ATmega2560 | 8-bit AVR | Arduino Uno, Mega |
| `atmelmegaavr` | ATmega4809 | 8-bit megaAVR | Arduino Uno WiFi Rev2 |
| `atmelsam` | SAMD21, SAMD51, SAME51 | 32-bit ARM | Arduino Zero, Adafruit Feather |
| `chipsalliance` | SweRV EH1 | RISC-V | CHIPS Alliance cores |
| `espressif32` | ESP32, ESP32-S2, ESP32-S3, ESP32-C3 | 32-bit Xtensa/RISC-V | ESP32 DevKit, NodeMCU |
| `espressif8266` | ESP8266 | 32-bit Xtensa | NodeMCU, Wemos D1 |
| `freescalekinetis` | MK20DX, MKL27Z | 32-bit ARM | Teensy 3.x, FRDM boards |
| `gd32v` | GD32VF103 | 32-bit RISC-V | GigaDevice RISC-V |
| `heltec-cubecell` | ASR650x | 32-bit ARM | LoRa/CubeCell boards |
| `intel_arc32` | Intel Curie | 32-bit ARC | Arduino 101 |
| `intel_mcs51` | 8051 | 8-bit 8051 | Classic 8051 cores |
| `kendryte210` | K210 | 64-bit RISC-V | Sipeed Maix |
| `lattice_ice40` | iCE40 | FPGA | Lattice iCE40 |
| `maxim32` | MAX32620, MAX32630 | 32-bit ARM | Maxim MCUs |
| `microchippic32` | PIC32MX, PIC32MZ | 32-bit MIPS | chipKIT boards |
| `nordicnrf51` | nRF51822 | 32-bit ARM | BLE SoC |
| `nordicnrf52` | nRF52832, nRF52840 | 32-bit ARM | BLE SoC |
| `nxplpc` | LPC1768, LPC11U24 | 32-bit ARM | mbed boards |
| `nxpimxrt` | i.MX RT1060 | 32-bit ARM | Teensy 4.x |
| `openhw` | CV32E40P | 32-bit RISC-V | OpenHW cores |
| `raspberrypi` | RP2040 | 32-bit ARM | Raspberry Pi Pico |
| `renesas-ra` | RA4M1, RA6M1 | 32-bit ARM | Arduino Uno R4 |
| `riscv_gap` | GAP8 | 32-bit RISC-V | GreenWaves GAP8 |
| `shakti` | Shakti | 32-bit RISC-V | IIT Madras Shakti |
| `sifive` | FE310, FU540 | 32/64-bit RISC-V | HiFive1 |
| `siliconlabsefm32` | EFM32 | 32-bit ARM | Silicon Labs Gecko |
| `ststm8` | STM8 | 8-bit STM8 | STM8S, STM8L |
| `ststm32` | STM32F0-F7, STM32G0, STM32H7, STM32L0, STM32WL | 32-bit ARM | Nucleo, Discovery, BluePill |
| `teensy` | MK20DX, MK66FX, IMXRT1062 | 32-bit ARM | Teensy 3.x, 4.x |
| `timsp430` | MSP430 | 16-bit MSP430 | LaunchPad |
| `titiva` | TM4C123 | 32-bit ARM | Tiva C LaunchPad |
| `wiznet7500` | W7500 | 32-bit ARM | WIZnet W7500 |

## 桌面平台

| 平台 ID | 架构 | 说明 |
|---------|------|------|
| `native` | Host machine | 编译为本机可执行文件，用于单元测试 |
| `linux_arm` | ARM | Linux ARM 设备 |
| `linux_i686` | x86 | 32-bit Linux |
| `linux_x86_64` | x86_64 | 64-bit Linux |
| `windows_x86` | x86 | Windows 本机 |

## 查找板 ID

```bash
# 搜索所有板
pio boards

# 按关键词搜索
pio boards esp32
pio boards stm32
pio boards arduino

# 查看板详情
pio boards --json esp32dev
```

## 常见板 ID 速查

| 板名 | Board ID | 平台 |
|------|----------|------|
| Arduino Uno | `uno` | atmelavr |
| Arduino Mega | `megaatmega2560` | atmelavr |
| Arduino Nano | `nanoatmega328` | atmelavr |
| ESP32 DevKit | `esp32dev` | espressif32 |
| ESP32-S3 | `esp32-s3-devkitc-1` | espressif32 |
| ESP8266 NodeMCU | `nodemcuv2` | espressif8266 |
| STM32 BluePill | `bluepill_f103c8` | ststm32 |
| STM32 Nucleo F401RE | `nucleo_f401re` | ststm32 |
| STM32 Nucleo F411RE | `nucleo_f411re` | ststm32 |
| Raspberry Pi Pico | `pico` | raspberrypi |
| Teensy 4.0 | `teensy40` | teensy |
| Seeed XIAO | `seeed_xiao_m0` | atmelsam |
