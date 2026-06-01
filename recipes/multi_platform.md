# 多平台构建

> **适用摘要**: 同一份代码在多个开发板/平台上构建，共享公共配置，隔离平台特有设置。

## 触发意图

- "多平台构建"
- "同时支持多个开发板"
- "跨平台编译"
- "一套代码多个板子"
- "Arduino Uno 和 ESP32 同时构建"

## 前置条件

| 条件 | 要求 |
|---|---|
| 项目 | 已有 platformio.ini |
| 目标板 | 已知所有目标 board ID |

## 调用链

```
Step 1: 识别公共配置
Step 2: 使用 [env] 公共节
Step 3: 为每个平台创建 [env:name]
Step 4: 处理平台特有代码（条件编译）
Step 5: 构建所有环境
```

## 分步说明

### Step 1: 识别公共配置

哪些设置所有平台共享？
- `framework` (如果相同)
- `monitor_speed`
- `build_flags` (公共部分)
- `lib_deps` (公共库)

### Step 2: 使用公共节

```ini
; 所有环境共享的设置
[env]
framework = arduino
monitor_speed = 115200
build_flags = -D APP_VERSION="1.0.0"
```

### Step 3: 为每个平台创建环境

```ini
[env:uno]
platform = atmelavr
board = uno

[env:esp32dev]
platform = espressif32
board = esp32dev

[env:nucleo_f401re]
platform = ststm32
board = nucleo_f401re

[env:native]
platform = native
test_ignore = test_embedded
```

### Step 4: 平台特有代码

在源代码中使用条件编译：

```cpp
#include <Arduino.h>

void setup() {
    Serial.begin(115200);

#if defined(ARDUINO_ARCH_ESP32)
    // ESP32 特有初始化
    WiFi.begin("SSID", "PASS");
#elif defined(ARDUINO_ARCH_AVR)
    // AVR 特有初始化
    // 注意 AVR 内存限制
#elif defined(ARDUINO_ARCH_STM32)
    // STM32 特有初始化
#endif
}

void loop() {
    // 通用代码
}
```

**平台宏参考**:

| 平台 | 宏 |
|------|-----|
| ESP32 | `ARDUINO_ARCH_ESP32` |
| ESP8266 | `ARDUINO_ARCH_ESP8266` |
| AVR (Uno, Mega) | `ARDUINO_ARCH_AVR` |
| STM32 | `ARDUINO_ARCH_STM32` |
| SAMD | `ARDUINO_ARCH_SAMD` |
| RP2040 | `ARDUINO_ARCH_RP2040` |

### Step 5: 构建所有环境

```bash
# 构建所有环境
platformio run

# 构建特定环境
platformio run -e uno
platformio run -e esp32dev

# 上传特定环境
platformio run -e esp32dev -t upload
```

## 完整示例

```ini
; platformio.ini — 多平台 blink 项目

[env]
framework = arduino
monitor_speed = 115200

[env:uno]
platform = atmelavr
board = uno

[env:featheresp32]
platform = espressif32
board = featheresp32

[env:teensy31]
platform = teensy
board = teensy31
```

```cpp
// src/main.cpp
#include <Arduino.h>

void setup() {
    pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
    digitalWrite(LED_BUILTIN, HIGH);
    delay(1000);
    digitalWrite(LED_BUILTIN, LOW);
    delay(1000);
}
```

## 常见错误

| 错误 | 原因 | 解决方法 |
|---|---|---|
| 某平台构建失败 | 使用了不兼容的 API | 用 `#ifdef` 隔离平台特有代码 |
| 库在某平台不可用 | 库不支持该架构 | 用 `lib_deps` 按环境指定，或用条件编译排除 |
| 内存溢出 (AVR) | AVR 内存有限 | 减小 buffer 大小，使用 `PROGMEM` |
| 构建时间过长 | 环境太多 | 只构建需要的环境: `platformio run -e uno` |

## 参考项目

- `resources/EXAM/wiring-blink/` — 多平台 blink 示例（Uno, ESP32, Teensy 三环境配置）
- `resources/platform_list.md` — 平台列表
- `resources/framework_list.md` — 框架列表
