# 创建新的 PlatformIO 项目

> **适用摘要**: 从零创建一个新的 PlatformIO 项目，包含正确的目录结构、platformio.ini 配置和入口文件。

## 触发意图

- "创建 PlatformIO 项目"
- "新建 PlatformIO 工程"
- "初始化嵌入式项目"
- "从头开始写固件"
- "创建 Arduino 项目"

## 前置条件

| 条件 | 要求 |
|---|---|
| PlatformIO Core | 已安装 (`pip install platformio`) |
| 目标板 | 已知 board ID（如 `uno`, `esp32dev`, `nucleo_f401re`） |
| 框架 | 已知框架（如 `arduino`, `stm32cube`, `espidf`） |

## 调用链

```
Step 1: 确定目标平台、框架和开发板
Step 2: 创建项目目录结构
Step 3: 编写 platformio.ini
Step 4: 创建 src/main.cpp 入口文件
Step 5: 添加必要的库依赖
Step 6: 构建验证
```

## 分步说明

### Step 1: 确定目标

向用户确认：
- **目标板**: 如 Arduino Uno、ESP32 DevKit、STM32 Nucleo 等
- **框架**: Arduino、STM32Cube、ESP-IDF 等
- **语言**: C++ (.cpp) 或 C (.c) 或 Arduino (.ino)

如不确定 board ID，运行：
```bash
pio boards <keyword>
# 例如: pio boards esp32
```

### Step 2: 创建目录结构

```
MyProject/
├── platformio.ini
├── src/
│   └── main.cpp
├── include/
├── lib/
└── test/
```

### Step 3: 编写 platformio.ini

**Arduino 项目 (如 ESP32)**:
```ini
[env:esp32dev]
platform = espressif32
framework = arduino
board = esp32dev
monitor_speed = 115200
```

**STM32Cube 项目**:
```ini
[env:nucleo_f401re]
platform = ststm32
framework = stm32cube
board = nucleo_f401re
```

**多环境项目**:
```ini
[env]
framework = arduino
monitor_speed = 115200

[env:uno]
platform = atmelavr
board = uno

[env:esp32]
platform = espressif32
board = esp32dev
```

### Step 4: 创建入口文件

**Arduino 框架** (`src/main.cpp`):
```cpp
#include <Arduino.h>

void setup() {
    Serial.begin(115200);
    pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
    digitalWrite(LED_BUILTIN, HIGH);
    delay(1000);
    digitalWrite(LED_BUILTIN, LOW);
    delay(1000);
}
```

**STM32Cube 框架** (`src/main.c`):
```c
#include "stm32f4xx_hal.h"

int main(void) {
    HAL_Init();
    // SystemClock_Config();
    // Peripheral init...

    while (1) {
        HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
        HAL_Delay(500);
    }
}
```

### Step 5: 添加库依赖

```ini
[env:myboard]
lib_deps =
    ; 从 PlatformIO Registry
    adafruit/Adafruit NeoPixel@^1.10.0
    ; 内置库
    SPI
    Wire
    ; Git 仓库
    https://github.com/user/repo.git
    ; 本地路径
    /path/to/local/lib
```

### Step 6: 构建验证

```bash
# 构建
platformio run

# 构建并上传
platformio run -t upload

# 打开串口监视器
platformio device monitor
```

## 常见错误

| 错误 | 原因 | 解决方法 |
|---|---|---|
| `Unknown board ID` | board ID 拼写错误 | 运行 `pio boards <keyword>` 查找 |
| `Framework 'arduino' is not compatible` | 框架与平台不匹配 | 检查平台支持的框架列表 |
| `Library not found` | lib_deps 名称错误 | 在 https://platformio.org/lib/ 搜索确切名称 |
| `src/main.cpp not found` | 缺少入口文件 | 创建 `src/main.cpp` 或 `src/main.c` |
| 上传失败 | 串口被占用或波特率错误 | 关闭串口监视器，检查 `upload_port` |

## 参考项目

- `resources/EXAM/wiring-blink/` — 最简 Arduino blink 示例（含多平台 platformio.ini）
- `resources/EXAM/cicd-setup/` — 完整项目结构示例（含 src 模块化 + 测试 + CI/CD）
- `resources/project_structure.md` — 项目结构详解
