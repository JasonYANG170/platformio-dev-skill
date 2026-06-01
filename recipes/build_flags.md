# 构建标志和高级配置

> **适用摘要**: 配置 PlatformIO 构建标志、预处理器定义、优化级别和高级构建选项。

## 触发意图

- "构建标志"
- "build_flags"
- "预处理器定义"
- "优化级别"
- "编译选项"
- "宏定义"
- "build_src_flags"

## 前置条件

| 条件 | 要求 |
|---|---|
| 项目 | 已有 platformio.ini |

## 分步说明

### 基本构建标志

```ini
[env:myboard]
; 预处理器定义
build_flags = -D MY_DEFINE

; 多个标志
build_flags =
    -D DEBUG
    -D APP_VERSION="1.0.0"
    -D BUFFER_SIZE=1024
```

### 常用 GCC 标志

```ini
[env:myboard]
build_flags =
    ; 优化级别
    -Os              ; 大小优化（默认，适合嵌入式）
    -O2              ; 速度优化
    -O0              ; 无优化（调试用）

    ; 警告
    -Wall            ; 常见警告
    -Wextra          ; 额外警告
    -Werror          ; 警告视为错误

    ; 调试信息
    -g               ; 生成调试信息
    -ggdb            ; GDB 调试信息

    ; C++ 标准
    -std=gnu++11     ; C++11 (native 测试需要)
    -std=gnu++17     ; C++17
    -std=c++20       ; C++20

    ; C 标准
    -std=gnu11       ; C11
    -std=c99         ; C99
```

### 条件编译示例

**platformio.ini**:
```ini
[env:debug]
build_flags = -DDEBUG -DLOG_LEVEL=3

[env:release]
build_flags = -DNDEBUG -DLOG_LEVEL=1
```

**源代码**:
```cpp
#if defined(DEBUG)
    #define LOG(fmt, ...) printf(fmt "\n", ##__VA_ARGS__)
#else
    #define LOG(fmt, ...)
#endif

#if LOG_LEVEL >= 3
    #define LOG_VERBOSE(fmt, ...) printf("[VERBOSE] " fmt "\n", ##__VA_ARGS__)
#else
    #define LOG_VERBOSE(fmt, ...)
#endif
```

### build_src_flags

只对 `src/` 目录生效，不影响库：

```ini
[env:myboard]
build_flags = -DGLOBAL_FLAG         ; 全局（src + lib）
build_src_flags = -DSRC_ONLY_FLAG   ; 仅 src/
```

### src_filter

控制哪些源文件被编译：

```ini
[env:myboard]
; 编译 src/ 下所有文件，排除 examples/ 和 tests/
src_filter = +<*> -<examples/> -<tests/>

; 只编译特定子目录
src_filter = +<main.cpp> +<drivers/>

; 排除特定文件
src_filter = +<*> -<unused_module.cpp>
```

### 构建脚本 (extra_scripts)

使用 Python 脚本扩展构建过程：

```ini
[env:myboard]
extra_scripts =
    pre:scripts/pre_build.py    ; 构建前执行
    post:scripts/post_build.py  ; 构建后执行
```

**pre_build.py**:
```python
Import("env")

# 添加自定义构建标志
env.Append(BUILD_FLAGS=["-DCUSTOM_FLAG"])

# 打印构建信息
print("Building for:", env["BOARD"])
```

**post_build.py**:
```python
Import("env")

# 构建后处理
def post_build(source, target, env):
    print("Build complete!")
    # 可以复制固件、生成报告等

env.AddPostAction("$BUILD_DIR/${PROGNAME}.elf", post_build)
```

### 平台特有标志

**ESP32**:
```ini
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino
build_flags =
    -DCONFIG_FREERTOS_HZ=1000
    -DBOARD_HAS_PSRAM
    -mfix-esp32-psram-cache-issue
```

**STM32**:
```ini
[env:nucleo_f401re]
platform = ststm32
board = nucleo_f401re
framework = stm32cube
build_flags =
    -DUSE_HAL_DRIVER
    -DSTM32F401xE
```

**AVR (内存优化)**:
```ini
[env:uno]
platform = atmelavr
board = uno
framework = arduino
build_flags =
    -Os                ; 大小优化
    -ffunction-sections
    -fdata-sections
    -Wl,--gc-sections  ; 链接器：删除未使用的代码
```

## 常见错误

| 错误 | 原因 | 解决方法 |
|---|---|---|
| 宏未生效 | 缺少 `-D` 前缀 | 使用 `-DMY_DEFINE=value` |
| 标志未传递到库 | 用了 build_src_flags | 改用 build_flags（全局） |
| 条件编译不生效 | LDF 未检测到 | 使用 `lib_ldf_mode = chain+` |
| C++ 标准错误 | native 环境需要显式指定 | 添加 `-std=gnu++11` |
| 链接器错误 | 未使用的函数占空间 | 添加 `-ffunction-sections -Wl,--gc-sections` |

## 参考项目

- `resources/EXAM/unit-testing/arduino-mock/platformio.ini` — build_flags 示例（-std=gnu++11）
- `resources/EXAM/cicd-setup/platformio.ini` — 多环境构建配置
- `resources/EXAM/unit-testing/semihosting/enable_semihosting.py` — 构建脚本示例
- `resources/platformio_ini_reference.md` — 完整配置参考
