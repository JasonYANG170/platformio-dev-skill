# PlatformIO 支持框架列表

## Arduino 系列

| 框架 ID | 说明 | 支持平台 |
|---------|------|---------|
| `arduino` | Arduino 核心框架 | atmelavr, atmelsam, espressif32, espressif8266, ststm32, nordicnrf51/52, teensy, raspberrypi, renesas-ra |
| `energia` | Energia (Arduino for TI) | timsp430, titiva |

## 厂商 SDK

| 框架 ID | 说明 | 支持平台 |
|---------|------|---------|
| `espidf` | Espressif IoT Development Framework | espressif32 |
| `esp8266-nonos-sdk` | ESP8266 Non-OS SDK | espressif8266 |
| `esp8266-rtos-sdk` | ESP8266 RTOS SDK | espressif8266 |
| `stm32cube` | STM32Cube HAL/LL | ststm32 |
| `cmsis` | ARM CMSIS | ststm32, atmelsam, nordicnrf51/52 |
| `spl` | Standard Peripheral Library (STM32) | ststm32 |
| `fsp` | Renesas Flexible Software Package | renesas-ra |
| `gd32vf103-sdk` | GigaDevice GD32VF103 SDK | gd32v |
| `wd-riscv-sdk` | Western Digital RISC-V SDK | chipsalliance |
| `kendryte-freertos-sdk` | Kendryte FreeRTOS SDK | kendryte210 |
| `kendryte-standalone-sdk` | Kendryte Standalone SDK | kendryte210 |
| `freedom-e-sdk` | SiFive Freedom E SDK | sifive |
| `shakti-sdk` | IIT Madras Shakti SDK | shakti |

## RTOS / 操作系统框架

| 框架 ID | 说明 | 支持平台 |
|---------|------|---------|
| `freertos` | FreeRTOS | ststm32, espressif32, atmelsam, esp8266-rtos-sdk |
| `mbed` | Arm Mbed OS | nxplpc, nxpimxrt, nordicnrf51/52, ststm32 |
| `zephyr` | Zephyr RTOS | nordicnrf51/52, ststm32, nxplpc, raspberrypi |

## 轻量级框架

| 框架 ID | 说明 | 支持平台 |
|---------|------|---------|
| `libopencm3` | LibOpenCM3 (open-source ARM) | ststm32 |
| `simba` | Simba (lightweight RTOS) | atmelavr, ststm32 |
| `pumbaa` | Pumbaa (Python on microcontrollers) | atmelavr, ststm32 |
| `wiringpi` | WiringPi (Raspberry Pi GPIO) | raspberrypi |

## RISC-V 框架

| 框架 ID | 说明 | 支持平台 |
|---------|------|---------|
| `freedom-e-sdk` | SiFive Freedom E SDK | sifive |
| `gd32vf103-sdk` | GigaDevice SDK | gd32v |
| `pulp-os` | PULP OS | riscv_gap |
| `pulp-runtime` | PULP Runtime | riscv_gap |
| `pulp-sdk` | PULP SDK | riscv_gap |
| `wd-riscv-sdk` | Western Digital SDK | chipsalliance |
| `shakti-sdk` | Shakti SDK | shakti |
| `kendryte-freertos-sdk` | Kendryte FreeRTOS | kendryte210 |
| `kendryte-standalone-sdk` | Kendryte Standalone | kendryte210 |

## 常用框架详解

### Arduino

最流行的嵌入式框架，跨平台兼容。

```ini
[env:myboard]
framework = arduino
```

特点：
- `setup()` + `loop()` 模式
- 丰富的库生态
- 跨平台 API (digitalRead, analogRead, Serial, etc.)
- 最低学习曲线

### STM32Cube

ST 官方 HAL/LL 库。

```ini
[env:nucleo_f401re]
framework = stm32cube
```

特点：
- HAL (Hardware Abstraction Layer) API
- LL (Low-Level) 寄存器操作
- STM32CubeMX 代码生成兼容
- 完整的外设驱动

### ESP-IDF

Espressif 官方 IoT 开发框架。

```ini
[env:esp32dev]
framework = espidf
```

特点：
- FreeRTOS 内置
- WiFi/BLE 原生支持
- 组件化架构
- 生产级特性 (OTA, 安全启动, 加密)

### Mbed OS

Arm 官方嵌入式操作系统。

```ini
[env:nucleo_f401re]
framework = mbed
```

特点：
- RTOS 内置
- 网络栈 (TCP/IP, MQTT, HTTP)
- 文件系统
- 在线 IDE 兼容

### Zephyr

Linux Foundation 支持的 RTOS。

```ini
[env:nrf52840]
framework = zephyr
```

特点：
- 高度可配置
- 丰富的驱动模型
- BLE 5.0 支持
- 安全认证 (PSA, FIPS)

## 框架选择建议

| 场景 | 推荐框架 |
|------|---------|
| 快速原型 | Arduino |
| 生产级 ESP32 | ESP-IDF |
| STM32 产品开发 | STM32Cube |
| 低功耗 BLE | Zephyr 或 Mbed |
| 学习/教育 | Arduino |
| 高性能 RISC-V | 厂商 SDK (freedom-e-sdk 等) |
| 需要 RTOS | FreeRTOS, Zephyr, Mbed |
| 跨平台移植 | Arduino 或 Mbed |
