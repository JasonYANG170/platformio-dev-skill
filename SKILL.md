---
name: platformio-dev-skill
description: >-
  AI Skill for PlatformIO embedded development. Used when users need to create, configure,
  or debug PlatformIO projects, including multi-platform builds, unit testing, CI/CD pipelines,
  library management, and debugging configurations.
  Supports 40+ platforms (AVR, STM32, ESP32, RISC-V, RP2040, etc.) and 25+ frameworks
  (Arduino, STM32Cube, ESP-IDF, Zephyr, Mbed, etc.).
  Trigger words: "PlatformIO", "platformio.ini", "pio", "unit test", "CI/CD", "Arduino", "ESP32", "STM32"
tags:
  - platformio
  - embedded
  - arduino
  - stm32
  - esp32
  - risc-v
  - unit-testing
  - ci-cd
  - multi-platform
license: Apache-2.0
compatibility: Requires PlatformIO Core (CLI) or PlatformIO IDE (VS Code extension)
metadata:
  author: Community
  version: "1.0.0"
---

# platformio-dev-skill

AI Skill for PlatformIO embedded development. Provides scenario-driven recipes, configuration references, testing guides, and common pitfalls. Supports 40+ platforms and 25+ frameworks.

## Core Principles

1. **platformio.ini is the single source of truth** — All build config, dependencies, upload settings, and test filters live here
2. **Environments isolate builds** — Each `[env:name]` section is an independent build configuration; use multiple envs for multi-platform or test separation
3. **Standard project structure** — `src/` for source, `lib/` for project libraries, `include/` for public headers, `test/` for tests
4. **Never guess board IDs** — Check `resources/platform_list.md` or run `pio boards` to find valid board identifiers
5. **Libraries are declarative** — Use `lib_deps` in platformio.ini, not manual copies; PlatformIO handles dependency resolution
6. **Test environments mirror build environments** — Use `test_ignore` to separate embedded vs desktop tests per environment
7. **Build flags propagate globally** — `build_flags` in `[env]` affects all compilation units; use `build_src_flags` for source-only flags
8. **Extra scripts extend the build** — Python scripts via `extra_scripts` can modify the build process at pre/post stages

## When to Use

**Applicable:**
- Creating new PlatformIO projects (any platform/framework)
- Configuring multi-platform builds (same code, multiple boards)
- Setting up unit testing (Unity, GoogleTest, ArduinoFake)
- Building CI/CD pipelines (GitHub Actions, GitLab CI)
- Managing library dependencies
- Configuring debug probes (J-Link, OpenOCD, ST-Link)
- Custom board definitions or build scripts
- Migration from Arduino IDE to PlatformIO

**Not applicable:**
- MounRiver Studio projects (use ch57x-dev-skill instead)
- Pure desktop application development
- PCB design or hardware schematics

---

## Scenario Quick Reference (Recipes)

When user intent matches a scenario below, **read the corresponding recipe first** — it contains the complete call chain, step-by-step instructions, common errors, and code examples.

### Project Setup

| recipe | scenario | 推荐模板例程 |
|---|---|---|
| `recipes/new_project.md` | Create a new PlatformIO project from scratch | `resources/EXAM/wiring-blink/` |
| `recipes/multi_platform.md` | Build same code for multiple boards/platforms | `resources/EXAM/wiring-blink/` |

### Testing

| recipe | scenario | 推荐模板例程 |
|---|---|---|
| `recipes/unit_testing.md` | Set up unit tests (Unity, GoogleTest, ArduinoFake) | `resources/EXAM/unit-testing/calculator/` |
| `recipes/ci_cd.md` | CI/CD pipeline with GitHub Actions | `resources/EXAM/cicd-setup/` |

### Configuration

| recipe | scenario | 推荐模板例程 |
|---|---|---|
| `recipes/library_management.md` | Add and manage library dependencies | `resources/EXAM/cicd-setup/` |
| `recipes/build_flags.md` | Build flags, defines, and advanced build config | `resources/EXAM/unit-testing/arduino-mock/` |
| `recipes/debugging.md` | Debug probe configuration (J-Link, OpenOCD, ST-Link) | `resources/EXAM/unit-testing/semihosting/` |
| `recipes/custom_board.md` | Custom board definitions and build scripts | `resources/EXAM/unit-testing/semihosting/` |

---

## Platform-Framework Example Mapping

Each platform has external examples in the PlatformIO GitHub repos. Pattern: `https://github.com/platformio/platform-{id}/tree/master/examples/{name}`. See `resources/EXAM/platforms/{id}.md` for full lists.

### 常用平台示例速查

| 平台 | 框架 | 典型示例 | 说明 |
|---|---|---|---|
| **espressif32** | arduino | arduino-blink, arduino-wifiscan, arduino-ble5-advertising, arduino-usb-keyboard | ESP32 最常用，27 个示例 |
| **espressif32** | espidf | espidf-hello-world, espidf-http-request, espidf-ble-eddystone, espidf-peripherals-uart, espidf-storage-sdcard | ESP-IDF 原生，含 BLE/安全/存储/ULP |
| **espressif8266** | arduino | arduino-blink, arduino-wifiscan, arduino-webserver, arduino-asyncudp | ESP8266 WiFi |
| **ststm32** | stm32cube | stm32cube-hal-blink, stm32cube-hal-iap, stm32cube-hal-usb-device-dfu, stm32cube-ll-blink | STM32 HAL/LL，46 个示例 |
| **ststm32** | arduino | arduino-blink, arduino-internal-libs | STM32 + Arduino |
| **ststm32** | zephyr | zephyr-blink, zephyr-ble-beacon, zephyr-net-echo-client, zephyr-drivers-can | Zephyr RTOS |
| **ststm32** | mbed | mbed-rtos-blink-baremetal, mbed-rtos-ethernet-tls | Mbed OS |
| **ststm32** | libopencm3 | libopencm3-blink, libopencm3-usb-cdcacm | 开源 ARM 库 |
| **ststm32** | spl | spl-blink, spl-uart-loopback, spl-flash | 标准外设库 |
| **ststm32** | cmsis | cmsis-blink | CMSIS 裸机 |
| **atmelavr** | arduino | arduino-blink, arduino-external-libs, assembly-blink | Arduino Uno/Mega |
| **nordicnrf52** | arduino | arduino-ble-led, arduino-bluefruit-bleuart | nRF52 BLE |
| **nordicnrf51** | zephyr | zephyr-ble-eddystone, zephyr-drivers-entropy | nRF51 BLE |
| **raspberrypi** | arduino | arduino-blink, arduino-external-libs | RP2040 |
| **teensy** | arduino | arduino-blink, arduino-hid-usb-mouse | Teensy USB |
| **sifive** | freedom-e-sdk | freedom-e-sdk_spi, freedom-e-sdk_timer-interrupt, freedom-e-sdk_multicore-hello | RISC-V SiFive |
| **gd32v** | arduino | arduino-blink, eval-blink, longan-nano-blink | GD32VF103 RISC-V |
| **renesas-ra** | arduino | arduino-blink, arduino-uno-r4-led-animation, arduino-wifiscan | Arduino UNO R4 |
| **renesas-ra** | fsp | fsp-blink, fsp-button-isr | Renesas FSP |
| **native** | — | hello-world | 桌面测试 |

### 框架-平台兼容性

| 框架 | 支持平台数 | 主要平台 |
|---|---|---|
| arduino | 17+ | atmelavr, espressif32/8266, ststm32, nordicnrf51/52, teensy, raspberrypi, renesas-ra |
| zephyr | 9+ | ststm32, nordicnrf51/52, nxplpc, nxpimxrt, sifive, teensy |
| mbed | 7+ | nxplpc, nxpimxrt, nordicnrf52, ststm32, freescalekinetis, riscv_gap |
| espidf | 1 | espressif32 |
| stm32cube | 1 | ststm32 |
| cmsis | 2 | ststm32, renesas-ra |
| spl | 2 | ststm32, ststm8 |
| libopencm3 | 2 | ststm32, titiva |
| freedom-e-sdk | 1 | sifive |
| fsp | 1 | renesas-ra |

---

## Platform-Specific Quick Reference

不同平台的代码模式差异很大。以下按平台分类总结关键配置和代码模式。

### ESP32 (espressif32)

**platformio.ini**:
```ini
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino          ; 或 espidf
monitor_speed = 115200
board_build.partitions = huge_app.csv  ; 可选：大 Flash 分区
```

**Arduino 框架入口**:
```cpp
#include <Arduino.h>
#include <WiFi.h>

void setup() {
    Serial.begin(115200);
    WiFi.begin("SSID", "PASS");
    pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
    digitalWrite(LED_BUILTIN, !digitalRead(LED_BUILTIN));
    delay(1000);
}
```

**ESP-IDF 框架入口** (参考 `resources/EXAM/frameworks/espidf.md`):
```c
#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "driver/gpio.h"

void app_main(void) {
    gpio_set_direction(GPIO_NUM_2, GPIO_MODE_OUTPUT);
    while(1) {
        gpio_set_level(GPIO_NUM_2, 1);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
        gpio_set_level(GPIO_NUM_2, 0);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}
```

**ESP32 特有功能**: WiFi, BLE 5.0, ULP 协处理器, OTA, SPIFFS/LittleFS, HTTPS, MQTT
**外部示例**: `arduino-wifiscan`, `arduino-ble5-advertising`, `espidf-http-request`, `espidf-storage-sdcard`
**常见陷阱**: `board_build.partitions` 内存分配; `framework = espidf` 入口是 `app_main()` 不是 `main()`

---

### STM32 (ststm32)

**platformio.ini**:
```ini
[env:nucleo_f401re]
platform = ststm32
board = nucleo_f401re
framework = stm32cube        ; 或 arduino, mbed, zephyr, libopencm3, spl, cmsis
upload_protocol = stlink
debug_tool = stlink
```

**STM32Cube HAL 入口** (参考 `resources/EXAM/unit-testing/stm32cube/`):
```c
#include "stm32f4xx_hal.h"

static void SystemClock_Config(void);  // 由 STM32CubeMX 生成

int main(void) {
    HAL_Init();
    SystemClock_Config();

    __HAL_RCC_GPIOA_CLK_ENABLE();
    GPIO_InitTypeDef GPIO_InitStruct = {0};
    GPIO_InitStruct.Pin = GPIO_PIN_5;
    GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
    GPIO_InitStruct.Pull = GPIO_NOPULL;
    GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
    HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

    while(1) {
        HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
        HAL_Delay(500);
    }
}

// SysTick 中断（HAL 时基）
void SysTick_Handler(void) { HAL_IncTick(); }
```

**STM32 LL (低层) 入口**:
```c
#include "stm32f4xx_ll_bus.h"
#include "stm32f4xx_ll_gpio.h"

int main(void) {
    LL_AHB1_GRP1_EnableClock(LL_AHB1_GRP1_PERIPH_GPIOA);
    LL_GPIO_SetPinMode(GPIOA, LL_GPIO_PIN_5, LL_GPIO_MODE_OUTPUT);
    while(1) {
        LL_GPIO_TogglePin(GPIOA, LL_GPIO_PIN_5);
        for(volatile int i=0; i<100000; i++);
    }
}
```

**Arduino 框架** (STM32 + Arduino API):
```cpp
#include <Arduino.h>
void setup() { pinMode(LED_BUILTIN, OUTPUT); }
void loop() { digitalWrite(LED_BUILTIN, !digitalRead(LED_BUILTIN)); delay(500); }
```

**STM32 特有功能**: HAL/LL 双 API, DMA, USB OTG, CAN, 以太网, FSMC
**外部示例**: `stm32cube-hal-blink`, `stm32cube-hal-usb-device-dfu`, `stm32cube-hal-iap`, `libopencm3-usb-cdcacm`
**常见陷阱**: `SystemClock_Config()` 必须正确配置; `SysTick_Handler` 必须定义否则 HAL_Delay 卡死; 不同系列头文件不同 (`stm32f4xx_hal.h` vs `stm32f1xx_hal.h`)

---

### AVR / Arduino Uno (atmelavr)

**platformio.ini**:
```ini
[env:uno]
platform = atmelavr
board = uno
framework = arduino
; 内存优化
build_flags = -Os -ffunction-sections -fdata-sections -Wl,--gc-sections
```

**Arduino 入口**:
```cpp
#include <Arduino.h>

void setup() {
    Serial.begin(9600);
    pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
    digitalWrite(LED_BUILTIN, HIGH);
    delay(1000);
    digitalWrite(LED_BUILTIN, LOW);
    delay(1000);
    Serial.println("Hello");
}
```

**AVR 特有约束**:
- Flash: 32KB, RAM: 2KB — 必须省内存
- 使用 `PROGMEM` 存储字符串常量: `const char msg[] PROGMEM = "hello";`
- `Serial.print()` 占 RAM, 尽量用 `F()` 宏: `Serial.println(F("hello"));`
- 无 WiFi/BLE — 需要外接模块
- 外部示例: `arduino-blink`, `arduino-external-libs`, `assembly-blink`
- 常见陷阱: RAM 溢出导致随机崩溃; `String` 类碎片化内存

---

### ESP8266 (espressif8266)

**platformio.ini**:
```ini
[env:nodemcuv2]
platform = espressif8266
board = nodemcuv2
framework = arduino
monitor_speed = 115200
```

**Arduino 入口**:
```cpp
#include <Arduino.h>
#include <ESP8266WiFi.h>

void setup() {
    Serial.begin(115200);
    WiFi.begin("SSID", "PASS");
    pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
    digitalWrite(LED_BUILTIN, !digitalRead(LED_BUILTIN));
    delay(1000);
}
```

**ESP8266 特有功能**: WiFi, HTTP Server, OTA, SPIFFS
**外部示例**: `arduino-wifiscan`, `arduino-webserver`, `arduino-asyncudp`, `esp8266-rtos-sdk-blink`
**常见陷阱**: `yield()` 或 `delay()` 必须在长循环中调用避免 WDT 重置; 只有一个 ADC 引脚 (A0)

---

### RP2040 (raspberrypi)

**platformio.ini**:
```ini
[env:pico]
platform = raspberrypi
board = pico
framework = arduino
```

**Arduino 入口**:
```cpp
#include <Arduino.h>

void setup() {
    pinMode(LED_BUILTIN, OUTPUT);
    Serial.begin(115200);
}

void loop() {
    digitalWrite(LED_BUILTIN, !digitalRead(LED_BUILTIN));
    delay(500);
}
```

**RP2040 特有功能**: 双核 ARM Cortex-M0+, PIO 状态机, USB 原生支持
**外部示例**: `arduino-blink`, `arduino-external-libs`
**常见陷阱**: `setup1()`/`loop1()` 用于第二核; PIO 需要单独配置

---

### RISC-V SiFive (sifive)

**platformio.ini**:
```ini
[env:hifive1-revb]
platform = sifive
board = hifive1-revb
framework = freedom-e-sdk
```

**Freedom E SDK 入口** (参考 `resources/EXAM/frameworks/freedom-e-sdk.md`):
```c
#include <stdio.h>
#include "metal/cpu.h"

int main(void) {
    struct metal_cpu *cpu = metal_cpu_get(metal_cpu_get_current_hartid());
    while(1) {
        printf("Hello from RISC-V\n");
        for(volatile int i=0; i<1000000; i++);
    }
    return 0;
}
```

**外部示例**: `freedom-e-sdk_spi`, `freedom-e-sdk_timer-interrupt`, `freedom-e-sdk_multicore-hello`
**常见陷阱**: 不同框架 (freedom-e-sdk vs zephyr) API 完全不同; 多核启动需要额外配置

---

### Native (桌面测试)

**platformio.ini**:
```ini
[env:native]
platform = native
build_flags = -std=gnu++11
; 纯逻辑测试，无硬件 API
```

**入口**:
```cpp
#include <stdio.h>

int main() {
    printf("Hello from native\n");
    return 0;
}
```

**Native 特有约束**:
- 无 `Arduino.h`, `digitalRead()`, `WiFi` 等硬件 API
- 用于纯逻辑/算法单元测试
- 需要 mock 硬件时用 `lib_deps = ArduinoFake`
- `build_flags = -std=gnu++11` 是 C++ 测试必需
- 外部示例: `hello-world`
- 常见陷阱: 包含 `Arduino.h` 会编译失败; `lib_compat_mode = off` 可绕过框架兼容检查

---

### 平台选择速查

| 需求 | 推荐平台 | 推荐框架 |
|---|---|---|
| WiFi + 云连接 | espressif32 | arduino 或 espidf |
| BLE 可穿戴 | nordicnrf52 | arduino 或 zephyr |
| 低成本量产 | atmelavr | arduino |
| 高性能 + USB | ststm32 | stm32cube |
| RISC-V 学习 | sifive 或 gd32v | freedom-e-sdk 或 arduino |
| 双核 + PIO | raspberrypi | arduino |
| 桌面单元测试 | native | — (配合 ArduinoFake) |
| LoRaWAN | heltec-cubecell | arduino |
| 8051 遗留 | intel_mcs51 | — |

---

## Standard Project Structure

```
MyProject/
├── platformio.ini          # Build configuration (THE config file)
├── src/                    # Source code
│   └── main.cpp            # Entry point (.cpp, .c, or .ino)
├── include/                # Public headers
│   └── config.h
├── lib/                    # Project-specific libraries
│   └── MyLib/
│       ├── src/
│       │   └── MyLib.cpp
│       └── include/
│           └── MyLib.h
├── test/                   # Unit tests
│   ├── test_common/        # Tests for both embedded + desktop
│   ├── test_embedded/      # Hardware-specific tests
│   └── test_desktop/       # Desktop/native tests
├── data/                   # Filesystem data (SPIFFS, LittleFS)
├── docs/                   # Documentation
└── extra_scripts.py        # Custom build scripts (optional)
```

---

## platformio.ini Quick Reference

### Minimal Configuration

```ini
[env:myboard]
platform = atmelavr       ; Platform identifier
framework = arduino       ; Framework
board = uno               ; Board identifier
```

### Common Options

```ini
[env:myboard]
; --- Build ---
platform = atmelavr
framework = arduino
board = uno
build_flags = -D MY_DEFINE -O2 -Wall
build_src_flags = -DSRC_ONLY_FLAG
src_filter = +<*> -<.git/> -<svn/> -<example/>

; --- Upload ---
upload_port = /dev/ttyUSB0
upload_speed = 115200
upload_protocol = arduino

; --- Libraries ---
lib_deps =
    Adafruit/Adafruit NeoPixel@^1.10.0
    SPI
lib_ldf_mode = deep+       ; Library dependency finder mode
lib_compat_mode = strict   ; Framework compatibility check

; --- Testing ---
test_framework = unity     ; unity (default), googletest, or custom
test_ignore = test_desktop ; Skip these test dirs
test_build_src = true      ; Build src/ when testing

; --- Advanced ---
extra_scripts = pre:scripts/pre_build.py
monitor_speed = 115200     ; Serial monitor baud rate
```

### Multi-Environment Pattern

```ini
; Common settings
[env]
framework = arduino
monitor_speed = 115200

; Arduino Uno
[env:uno]
platform = atmelavr
board = uno

; ESP32
[env:esp32]
platform = espressif32
board = esp32dev

; Native (desktop) for unit tests
[env:native]
platform = native
test_ignore = test_embedded
```

---

## Testing Framework Quick Reference

### Unity (Default)

**基本模式** — 参考 `resources/EXAM/unit-testing/calculator/`:

```cpp
#include <unity.h>

void test_example(void) {
    TEST_ASSERT_EQUAL(42, answer);
    TEST_ASSERT_TRUE(condition);
    TEST_ASSERT_FLOAT_WITHIN(0.01, 3.14, value);
}

void setUp(void) {}    // Called before each test
void tearDown(void) {} // Called after each test

// For Arduino boards:
#ifdef ARDUINO
void setup() { delay(2000); UNITY_BEGIN(); RUN_TEST(test_example); UNITY_END(); }
void loop() {}
#else
int main() { UNITY_BEGIN(); RUN_TEST(test_example); return UNITY_END(); }
#endif
```

**RUN_UNITY_TESTS 包装函数模式** — 参考 `resources/EXAM/unit-testing/calculator/test/test_desktop/`:

```cpp
// 将测试注册封装到函数中，Arduino 和 native 都调用同一函数
void RUN_UNITY_TESTS() {
    UNITY_BEGIN();
    RUN_TEST(test_addition);
    RUN_TEST(test_subtraction);
    UNITY_END();
}

#ifdef ARDUINO
#include <Arduino.h>
void setup() { delay(2000); RUN_UNITY_TESTS(); }
void loop() {}
#else
int main(int argc, char **argv) { RUN_UNITY_TESTS(); return 0; }
#endif
```

**自定义 Unity 输出 (UART)** — 参考 `resources/EXAM/unit-testing/stm32cube/test/unity_config.h`:

```c
// 将 Unity 输出重定向到 UART（嵌入式常见做法）
// unity_config.h:
#define UNITY_OUTPUT_START()    MX_USART2_UART_Init()
#define UNITY_OUTPUT_CHAR(a)    unityOutputChar(a)
#define UNITY_OUTPUT_FLUSH()    unityOutputFlush()
#define UNITY_OUTPUT_COMPLETE() unityOutputComplete()

// unity_config.c:
void unityOutputChar(char c) { HAL_UART_Transmit(&huart2, (uint8_t*)&c, 1, 100); }
void unityOutputFlush(void) {}
```

**自定义 Unity 输出 (Semihosting)** — 参考 `resources/EXAM/unit-testing/semihosting/test/unity_config.c`:

```c
// 通过调试器输出，无需 UART
#define UNITY_OUTPUT_CHAR(a)    putchar(a)
#define UNITY_OUTPUT_FLUSH()    fflush(stdout)
```

### GoogleTest

```ini
[env:native]
platform = native
test_framework = googletest

; 也可在嵌入式环境使用
[env:esp32dev]
platform = espressif32
framework = arduino
board = esp32dev
test_framework = googletest
```

```cpp
#include <gtest/gtest.h>

// 自动发现，无需手动注册
TEST(CalculatorTest, Addition) {
    EXPECT_EQ(4, add(2, 2));
}

TEST(CalculatorTest, Division) {
    EXPECT_THROW(div(1, 0), std::runtime_error);
}
```

**GoogleMock** — 参考 `resources/EXAM/unit-testing/googletest/test/test_gmock/`:

```cpp
#include <gmock/gmock.h>

class MockTurtle : public Turtle {
public:
    MOCK_METHOD(void, PenUp, (), (override));
    MOCK_METHOD(void, Forward, (int distance), (override));
    MOCK_METHOD(void, PenDown, (), (override));
    MOCK_METHOD(int, GetX, (), (const, override));
};

TEST(PainterTest, CanDrawSomething) {
    MockTurtle turtle;
    EXPECT_CALL(turtle, PenDown()).Times(::testing::AtLeast(1));
    Painter painter(&painter);
    painter.DrawSomething();
}
```

**Arduino 双环境 GoogleTest** — 参考 `resources/EXAM/unit-testing/googletest/test/test_gtest/`:

```cpp
#if defined(ARDUINO)
void setup() {
    ::testing::InitGoogleTest();
    // 需要等待 Serial DTR/RTS
}
void loop() { if (RUN_ALL_TESTS()) delay(1000); }
#else
int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
#endif
```

### ArduinoFake (Mocking)

```ini
[env:native]
platform = native
build_flags = -std=gnu++11
lib_deps = ArduinoFake
```

**条件编译切换** — 参考 `resources/EXAM/unit-testing/arduino-mock/src/main.cpp`:

```cpp
#ifdef UNIT_TEST
    #include "ArduinoFake.h"
#else
    #include "Arduino.h"
#endif
```

**FakeIt When/Verify 模式** — 参考 `resources/EXAM/unit-testing/arduino-mock/test/test_my_service.cpp`:

```cpp
#include <fakeit.hpp>
using namespace fakeit;

void setUp(void) { ArduinoFakeReset(); }  // 每个测试前重置 mock

void test_client_request(void) {
    // Mock Arduino Client 接口
    When(Method(ArduinoFake(Client), stop)).AlwaysReturn();
    When(Method(ArduinoFake(Client), available)).Return(1, 1, 1, 0);  // 连续调用返回不同值
    When(OverloadedMethod(ArduinoFake(Client), read, int())).Return(2, 0, 0);
    When(OverloadedMethod(ArduinoFake(Client), connect, int(const char*, uint16_t))).Return(1);

    Client* clientMock = ArduinoFakeMock(Client);
    MyService service(clientMock);
    String response = service.request("myserver.com");

    TEST_ASSERT_EQUAL_STRING("200", response.c_str());

    // 验证调用次数和参数
    Verify(Method(ArduinoFake(Client), stop)).Once();
    Verify(Method(ArduinoFake(Client), available)).Exactly(4_Times);
    Verify(OverloadedMethod(ArduinoFake(Client), connect, int(const char*, uint16_t))
        .Using("myserver.com", 80)).Once();
}
```

**STM32Cube 硬件测试** — 参考 `resources/EXAM/unit-testing/stm32cube/`:

```c
// setUp/tearDown 用于硬件初始化/反初始化
void setUp(void) {
    GPIO_InitTypeDef GPIO_InitStruct = {0};
    __HAL_RCC_GPIOA_CLK_ENABLE();
    GPIO_InitStruct.Pin = LED_PIN;
    GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
    HAL_GPIO_Init(LED_GPIO_PORT, &GPIO_InitStruct);
}

void tearDown(void) {
    HAL_GPIO_DeInit(LED_GPIO_PORT, LED_PIN);
}
```

---

## CI/CD Quick Reference (GitHub Actions)

```yaml
name: Build & Test
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: "3.11" }
      - name: Install PlatformIO
        run: pip install platformio
      - name: Run unit tests
        run: platformio test --environment native -f unit -v
      - name: Build firmware
        run: platformio run
```

---

## Critical Pitfalls (Must Read)

### 1. Board ID Must Exist

```ini
; ❌ WRONG — typo in board ID
board = arduino_uno

; ✅ CORRECT — exact board ID from registry
board = uno
```

Run `pio boards` or check https://platformio.org/boards/ to find valid IDs.

### 2. Framework Must Match Platform

```ini
; ❌ WRONG — Arduino not available on native
[env:native]
platform = native
framework = arduino

; ✅ CORRECT — native has no framework
[env:native]
platform = native
```

### 3. lib_deps Version Pinning

```ini
; ❌ WRONG — no version, may break on update
lib_deps = Adafruit NeoPixel

; ✅ CORRECT — pin major version
lib_deps = Adafruit/Adafruit NeoPixel@^1.10.0

; ✅ EXACT version
lib_deps = Adafruit/Adafruit NeoPixel@1.10.4
```

### 4. Test Environment Separation

```ini
; ❌ WRONG — running hardware tests on native
[env:native]
platform = native
; No test_ignore, tries to run all tests including hardware ones

; ✅ CORRECT — filter tests per environment
[env:native]
platform = native
test_ignore = test_embedded

[env:uno]
platform = atmelavr
board = uno
test_ignore = test_desktop
```

### 5. Build Flags Format

```ini
; ❌ WRONG — missing -D prefix
build_flags = MY_DEFINE=1

; ✅ CORRECT — proper flag format
build_flags = -DMY_DEFINE=1

; ✅ MULTIPLE flags
build_flags = -DDEBUG -O2 -Wall -Wextra
```

### 6. src_filter Syntax

```ini
; ❌ WRONG — trying to exclude with wrong syntax
src_filter = -<examples/>

; ✅ CORRECT — include src root, exclude subdirs
src_filter = +<*> -<examples/> -<tests/>

; ✅ INCLUDE specific subdirs only
src_filter = +<src/> +<lib/>
```

### 7. Library Dependency Finder Modes

```ini
; Options:
lib_ldf_mode = off       ; No auto-detection, use lib_deps only
lib_ldf_mode = chain     ; Follow #include chains (default)
lib_ldf_mode = chain+    ; Chain + deep search in conditional blocks
lib_ldf_mode = deep      ; Deep search all source files
lib_ldf_mode = deep+     ; Deep + follow conditional compilation
```

### 8. Monitor Port Auto-Detection

```ini
; ❌ WRONG — hardcoded port may not exist
monitor_port = COM3

; ✅ CORRECT — let PlatformIO auto-detect
; (omit monitor_port entirely)

; ✅ EXPLICIT when needed
monitor_port = /dev/ttyUSB0
monitor_speed = 115200
```

### 9. Extra Scripts Path

```ini
; ❌ WRONG — relative path confusion
extra_scripts = ../scripts/build.py

; ✅ CORRECT — relative to project root
extra_scripts = pre:tools/pre_build.py

; ✅ PRE and POST hooks
extra_scripts =
    pre:scripts/pre_build.py
    post:scripts/post_build.py
```

### 10. Native Platform Limitations

```ini
; native platform compiles for the HOST machine, not a microcontroller
; ❌ WRONG — using hardware-specific APIs
[env:native]
platform = native
; analogRead(), digitalWrite() won't work without ArduinoFake

; ✅ CORRECT — use for pure logic testing
[env:native]
platform = native
lib_deps = ArduinoFake
build_flags = -std=gnu++11
```

---

## Execution Workflow

| Step | Name | Description |
|------|------|-------------|
| 1 | Plan | Understand requirements: target board, framework, peripherals, testing needs |
| 2 | Recipe | Check if a matching recipe exists in `recipes/`; if yes, follow its steps |
| 3 | Query | For config options not covered by recipes, check `resources/` references |
| 4 | Validate | Verify board ID, framework compatibility, library versions, pin assignments |
| 5 | Confirm | Present plan to user: platformio.ini structure, libraries, test strategy |
| 6 | Execute | **New project:** create directory structure and platformio.ini. **Existing project:** edit in place. |
| 7 | Build | Run `platformio run` to compile; fix any errors |
| 8 | Test | Run `platformio test` for unit tests; verify on hardware if applicable |
| 9 | Debug | Use `platformio debug` or serial monitor for troubleshooting |

### Step 6 Detail — Project Creation Strategy

**When the target directory has no existing project (first-time creation):**

1. **Select the closest example** from `resources/EXAM/` based on user requirements:
   - Arduino blink / 基础项目 → `resources/EXAM/wiring-blink/`
   - 多平台构建 → `resources/EXAM/wiring-blink/`（含 Uno, ESP32, Teensy 三环境）
   - Unity 单元测试 → `resources/EXAM/unit-testing/calculator/`（双环境：embedded + native）
   - GoogleTest 测试 → `resources/EXAM/unit-testing/googletest/`（含 gtest + gmock）
   - ArduinoFake mock → `resources/EXAM/unit-testing/arduino-mock/`（mock Arduino API）
   - STM32Cube HAL 测试 → `resources/EXAM/unit-testing/stm32cube/`
   - Semihosting 调试 → `resources/EXAM/unit-testing/semihosting/`（OpenOCD semihosting）
   - CI/CD 流水线 → `resources/EXAM/cicd-setup/`（GitHub Actions + 单元测试 + 集成测试 + 部署）

2. **Copy the entire example directory** to the user's project directory, preserving the full structure（包括 `src/`, `lib/`, `test/`, `platformio.ini`, `.github/` 等）

3. **Modify the copied code** to match user requirements — 调整 platformio.ini（平台、板卡、库依赖）、修改源代码、添加/删除测试环境等

4. **Explain what was copied and why**, so the user understands the starting point

**When the target directory already contains a project:**

Edit existing files in place. Do not overwrite unless explicitly asked.

---

## Failure Strategies

| Situation | Action |
|---|---|
| Board ID not found | Run `pio boards <keyword>` to search, or check `resources/platform_list.md` |
| Framework mismatch | Check platform docs for supported frameworks |
| Library not found | Verify exact name on https://platformio.org/lib/ |
| Build fails | Check `platformio run -v` for verbose output; read error messages carefully |
| Upload fails | Verify `upload_port`, `upload_speed`, `upload_protocol`; check cable/connection |
| Tests fail on native | Ensure `lib_deps = ArduinoFake` and `build_flags = -std=gnu++11` |

## References

- Scenario recipes → `recipes/` directory
- platformio.ini reference → `resources/platformio_ini_reference.md`
- Project structure → `resources/project_structure.md`
- Testing frameworks → `resources/testing_frameworks.md`
- Platform list → `resources/platform_list.md`
- Framework list → `resources/framework_list.md`
- Common pitfalls → `resources/pitfalls.md`
- Example projects → `resources/EXAM/`（所有模板例程）
  - 基础 blink（多平台） → `resources/EXAM/wiring-blink/`
  - CI/CD 完整项目 → `resources/EXAM/cicd-setup/`
  - Unity 双环境测试 → `resources/EXAM/unit-testing/calculator/`
  - GoogleTest + GoogleMock → `resources/EXAM/unit-testing/googletest/`
  - ArduinoFake mock（FakeIt） → `resources/EXAM/unit-testing/arduino-mock/`
  - STM32Cube HAL 硬件测试 → `resources/EXAM/unit-testing/stm32cube/`
  - Semihosting 调试测试 → `resources/EXAM/unit-testing/semihosting/`
- Platform docs → `resources/EXAM/platforms/`（40+ 平台，含外部示例链接）
- Framework docs → `resources/EXAM/frameworks/`（25+ 框架，含平台兼容性）
- External examples → `https://github.com/platformio/platform-{id}/tree/master/examples/{name}`
- Official docs → https://docs.platformio.org/
