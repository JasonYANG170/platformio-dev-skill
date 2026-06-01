# 自定义开发板和构建脚本

> **适用摘要**: 定义自定义开发板、修改构建过程、使用 Python 脚本扩展 PlatformIO。

## 触发意图

- "自定义开发板"
- "custom board"
- "构建脚本"
- "extra_scripts"
- "修改构建过程"
- "自定义链接脚本"
- "board JSON"

## 前置条件

| 条件 | 要求 |
|---|---|
| PlatformIO Core | 已安装 |
| 项目 | 已有 platformio.ini |

## 分步说明

### 自定义板定义

当使用的开发板不在 PlatformIO registry 中时，可以创建自定义板定义。

**目录结构**:
```
MyProject/
├── boards/
│   └── my_custom_board.json
├── platformio.ini
└── src/
```

**boards/my_custom_board.json**:
```json
{
    "build": {
        "core": "arduino",
        "extra_flags": "-DARDUINO_MY_BOARD",
        "f_cpu": "80000000L",
        "mcu": "esp32",
        "variant": "esp32"
    },
    "connectivity": [
        "wifi",
        "bluetooth"
    ],
    "frameworks": [
        "arduino",
        "espidf"
    ],
    "name": "My Custom ESP32 Board",
    "upload": {
        "flash_size": "4MB",
        "maximum_ram_size": 327680,
        "maximum_size": 4194304,
        "protocol": "esptool",
        "speed": 921600
    },
    "url": "https://example.com/my-board",
    "vendor": "My Company"
}
```

**platformio.ini**:
```ini
[env:myboard]
platform = espressif32
board = my_custom_board   ; 使用 boards/ 目录下的定义
framework = arduino
```

### 使用构建脚本

**基本用法**:
```ini
[env:myboard]
extra_scripts =
    pre:scripts/pre_build.py    ; 构建前
    post:scripts/post_build.py  ; 构建后
```

**pre_build.py — 添加构建标志**:
```python
Import("env")

# 添加全局构建标志
env.Append(BUILD_FLAGS=[
    "-DCUSTOM_BUILD",
    "-DBUILD_TIME='\"2024-01-01\"'",
])

# 打印构建信息
print("="*40)
print("Board:", env["BOARD"])
print("Platform:", env["PLATFORM"])
print("Framework:", env.get("PIOFRAMEWORK"))
print("="*40)
```

**pre_build.py — 生成版本头文件**:
```python
Import("env")
import subprocess
from datetime import datetime

# 获取 Git commit hash
try:
    git_hash = subprocess.check_output(
        ["git", "rev-parse", "--short", "HEAD"]
    ).strip().decode()
except:
    git_hash = "unknown"

# 生成版本头文件
version_content = f"""
#pragma once
#define FIRMWARE_VERSION "1.0.0"
#define BUILD_DATE "{datetime.now().strftime('%Y-%m-%d %H:%M:%S')}"
#define GIT_HASH "{git_hash}"
"""

with open("include/version.h", "w") as f:
    f.write(version_content)

print(f"Generated version.h: {git_hash}")
```

**post_build.py — 固件大小报告**:
```python
Import("env")
import os

def post_build(source, target, env):
    firmware_path = str(target[0])

    if os.path.exists(firmware_path):
        size = os.path.getsize(firmware_path)
        print(f"\n{'='*40}")
        print(f"Firmware: {firmware_path}")
        print(f"Size: {size} bytes ({size/1024:.1f} KB)")
        print(f"{'='*40}\n")

env.AddPostAction("$BUILD_DIR/${PROGNAME}.elf", post_build)
```

**post_build.py — 自动复制固件**:
```python
Import("env")
import shutil

def copy_firmware(source, target, env):
    src = str(target[0])
    dst = "firmware/latest.bin"
    os.makedirs("firmware", exist_ok=True)
    shutil.copy2(src, dst)
    print(f"Firmware copied to {dst}")

env.AddPostAction("$BUILD_DIR/${PROGNAME}.elf", copy_firmware)
```

### 自定义链接脚本

```ini
[env:myboard]
; 使用自定义链接脚本
board_build.ldscript = linker_scripts/custom.ld

; 或通过构建标志
build_flags =
    -Wl,-T,linker_scripts/custom.ld
```

### 修改上传行为

```ini
[env:myboard]
; 自定义上传命令
upload_command = esptool.py --chip esp32 --port $UPLOAD_PORT --baud $UPLOAD_SPEED write_flash 0x0 $SOURCE

; 自定义上传端口
upload_port = /dev/ttyUSB0

; 自定义上传速度
upload_speed = 921600
```

### 条件构建脚本

**scripts/env_check.py**:
```python
Import("env")

# 检查环境变量
import os

if os.environ.get("CI"):
    env.Append(BUILD_FLAGS=["-DCI_BUILD"])
    print("CI build detected")

# 检查是否是调试构建
if "debug" in env["PIOENV"]:
    env.Append(BUILD_FLAGS=["-DDEBUG_BUILD"])
    env.Replace(BUILD_TYPE="debug")
```

### 平台特有构建修改

**ESP32 分区表**:
```python
# scripts/esp32_partitions.py
Import("env")

# 使用自定义分区表
env.Append(
    FLASH_EXTRA_IMAGES=[
        ("0x1000", "$BUILD_DIR/bootloader.bin"),
        ("0x8000", "partitions.csv"),
    ]
)
```

**STM32 启动文件**:
```python
# scripts/stm32_startup.py
Import("env")

# 替换默认启动文件
env.Replace(
    STARTUPFILES=["custom_startup_stm32f4xx.s"]
)
```

## 常见错误

| 错误 | 原因 | 解决方法 |
|---|---|---|
| 板定义未找到 | JSON 语法错误 | 验证 JSON 格式 |
| 脚本未执行 | 路径错误 | 确保 `pre:` 或 `post:` 前缀和正确路径 |
| 链接脚本冲突 | 多个链接脚本 | 只指定一个 `board_build.ldscript` |
| 构建变量未生效 | 脚本在错误阶段执行 | 确认用 `pre:` 还是 `post:` |
| `Import("env")` 失败 | 脚本不在 PlatformIO 上下文中 | 确保通过 `extra_scripts` 调用 |

## 参考项目

- `resources/EXAM/unit-testing/semihosting/enable_semihosting.py` — 构建脚本示例
- `resources/platformio_ini_reference.md` — 完整配置参考
- PlatformIO 文档: https://docs.platformio.org/en/latest/projectconf/sections/env-options/advanced-scripting.html
