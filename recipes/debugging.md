# 调试配置

> **适用摘要**: 配置 PlatformIO 项目的调试探针（J-Link、OpenOCD、ST-Link）和 semihosting。

## 触发意图

- "配置调试"
- "debug probe"
- "J-Link"
- "OpenOCD"
- "ST-Link"
- "semihosting"
- "printf 调试"
- "GDB 调试"

## 前置条件

| 条件 | 要求 |
|---|---|
| 调试探针 | J-Link、ST-Link、CMSIS-DAP 等 |
| PlatformIO | 已安装 |

## 分步说明

### 基本调试

```bash
# 启动调试会话
platformio debug

# 指定环境
platformio debug -e nucleo_f401re
```

PlatformIO 会自动：
1. 构建带调试信息的固件 (`-g` flag)
2. 启动 GDB server
3. 连接 GDB 客户端

### 调试探针配置

**自动检测** (大多数情况):
```ini
[env:nucleo_f401re]
platform = ststm32
board = nucleo_f401re
; PlatformIO 自动检测 ST-Link on-board
```

**手动指定协议**:
```ini
[env:myboard]
; J-Link
debug_tool = jlink

; ST-Link
debug_tool = stlink

; OpenOCD
debug_tool = openocd

; CMSIS-DAP
debug_tool = cmsis-dap

; Black Magic Probe
debug_tool = blackmagic
```

**指定调试接口**:
```ini
[env:myboard]
debug_tool = jlink
debug_interface = swd    ; SWD (默认)
; debug_interface = jtag  ; JTAG
```

### Semihosting

Semihosting 允许通过调试探针进行 printf 输出，无需额外串口。

**STM32 示例**:
```ini
[env:nucleo_l152re]
platform = ststm32
framework = stm32cube
board = nucleo_l152re

; 启用 semihosting
extra_scripts =
    pre:enable_semihosting.py

debug_extra_cmds =
    monitor arm semihosting enable
    monitor arm semihosting_fileio enable

; 测试命令（semihosting 输出）
test_testing_command =
    ${platformio.packages_dir}/tool-openocd/bin/openocd
    -s ${platformio.packages_dir}/tool-openocd
    -f openocd/scripts/board/st_nucleo_l1.cfg
    -c init
    -c "arm semihosting enable"
    -c "reset run"
```

**enable_semihosting.py**:
```python
Import("env")

env.Append(
    BUILD_UNFLAGS=[
        "-lnosys",
        "--specs=nosys.specs",
    ],
    LINKFLAGS=[
        "--specs=rdimon.specs",
    ],
    LIBS=[
        "rdimon",
    ],
)
```

### 自定义 OpenOCD 配置

```ini
[env:custom_board]
platform = ststm32
board = custom_board
debug_tool = openocd

; 自定义 OpenOCD 配置
debug_server =
    openocd
    -f interface/stlink.cfg
    -f target/stm32f4x.cfg
    -c "adapter speed 4000"

; 自定义 GDB 启动命令
debug_init_cmds =
    define pio_reset_halt_target
        monitor reset halt
    end
    define pio_reset_run_target
        monitor reset
    end
    target extended-remote $DEBUG_PORT
    monitor reset halt
    $LOAD_CMDS
    $INIT_BREAK
```

### 调试 ESP32

```ini
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino

; ESP32 内置 JTAG 调试
debug_tool = esp-builtin

; 或使用外部 J-Link
; debug_tool = jlink
```

### 调试 AVR

```ini
[env:uno]
platform = atmelavr
board = uno
framework = arduino

; AVR 使用 avarice + GDB
debug_tool = avr-stub
; 或使用外部 debugWIRE
; debug_tool = jlink
```

### 调试构建标志

```ini
[env:debug]
build_flags =
    -g              ; 调试信息
    -O0             ; 无优化（便于调试）
    -DDEBUG         ; 自定义调试宏

[env:release]
build_flags =
    -Os             ; 大小优化
    -DNDEBUG        ; 禁用 assert
```

## 串口监视器

```bash
# 打开串口监视器
platformio device monitor

# 指定端口和波特率
platformio device monitor --port /dev/ttyUSB0 --baud 115200

# 在 platformio.ini 中配置
[env:myboard]
monitor_port = /dev/ttyUSB0
monitor_speed = 115200
monitor_filters = colorize, log2file  ; 可选过滤器
```

## 常见错误

| 错误 | 原因 | 解决方法 |
|---|---|---|
| `Debug probe not found` | 探针未连接或驱动未安装 | 检查 USB 连接，安装驱动 |
| `Failed to launch GDB` | GDB server 启动失败 | 检查 `debug_tool` 配置 |
| `Cannot access memory` | Flash 未解锁或保护 | 先执行 mass erase |
| Semihosting 无输出 | 未启用 semihosting | 检查 `enable_semihosting.py` 和 `debug_extra_cmds` |
| 调试速度慢 | SWD 时钟太低 | 设置 `debug_speed = 4000` |
| `No source available` | 缺少调试信息 | 确保 `build_flags = -g` |

## 参考项目

- `resources/EXAM/unit-testing/semihosting/` — Semihosting 完整示例
- `resources/EXAM/unit-testing/semihosting/enable_semihosting.py` — 构建脚本
- `resources/EXAM/unit-testing/semihosting/platformio.ini` — 调试配置
- `resources/platformio_ini_reference.md` — 完整配置参考
