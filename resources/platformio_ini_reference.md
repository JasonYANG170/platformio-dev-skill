# platformio.ini 完整参考

## 配置节

| 节 | 说明 |
|---|---|
| `[platformio]` | 全局 PlatformIO 设置 |
| `[env]` | 所有环境的公共设置 |
| `[env:name]` | 特定环境设置 |

## [platformio] 全局设置

```ini
[platformio]
src_dir = src                  ; 源代码目录
include_dir = include          ; 头文件目录
lib_dir = lib                  ; 库目录
test_dir = test                ; 测试目录
data_dir = data                ; 文件系统数据目录
build_dir = .pio/build         ; 构建输出目录
build_cache_dir = .pio/cache   ; 构建缓存目录
default_envs = uno, esp32      ; 默认构建环境
extra_configs =
    platformio_override.ini    ; 额外配置文件
    boards/*.json              ; 自定义板定义
```

## [env] 构建选项

```ini
[env:myboard]
; --- 平台和框架 ---
platform = atmelavr             ; 平台 ID（必填）
framework = arduino             ; 框架（必填）
board = uno                     ; 板 ID（必填）
board_build.mcu = atmega328p    ; 覆盖 MCU
board_build.f_cpu = 16000000L   ; 覆盖 CPU 频率
board_build.variant = standard  ; 板变体

; --- 构建标志 ---
build_flags =
    -D MY_DEFINE=1              ; 预处理器定义
    -Os                         ; 优化级别
    -Wall                       ; 警告
    -std=gnu++11                ; C++ 标准
build_src_flags = -DSRC_ONLY   ; 仅 src/ 的标志
build_unflags = -Os            ; 移除默认标志

; --- 源文件过滤 ---
src_filter =
    +<*>                        ; 包含所有
    -<.git/>                    ; 排除 .git
    -<examples/>                ; 排除 examples

; --- 上传 ---
upload_port = /dev/ttyUSB0     ; 上传端口
upload_speed = 115200           ; 上传波特率
upload_protocol = arduino       ; 上传协议
upload_command = ...            ; 自定义上传命令
upload_flags =                  ; 额外上传标志
upload_resetmethod = --before default_reset

; --- 监视器 ---
monitor_port = /dev/ttyUSB0    ; 串口监视器端口
monitor_speed = 115200          ; 波特率
monitor_filters =               ; 过滤器
    colorize                    ; 彩色输出
    log2file                    ; 保存到文件
monitor_eol = LF               ; 行尾字符
monitor_rts = 0                ; RTS 信号
monitor_dtr = 0                ; DTR 信号

; --- 库 ---
lib_deps =
    Adafruit/Adafruit NeoPixel@^1.10.0
    SPI
lib_extra_dirs =               ; 额外库搜索目录
    /path/to/libs
lib_ldf_mode = chain+          ; 库依赖查找模式
lib_compat_mode = strict       ; 框架兼容性检查
lib_archive = true             ; 打包为 .a 静态库
lib_ignore =                   ; 忽略的库
    SomeLib

; --- 测试 ---
test_framework = unity         ; 测试框架 (unity, googletest)
test_filter = test_common      ; 只运行匹配的测试
test_ignore =                  ; 忽略的测试目录
    test_desktop
test_build_src = true          ; 测试时构建 src/
test_testing_command =         ; 自定义测试命令

; --- 调试 ---
debug_tool = jlink             ; 调试探针
debug_interface = swd          ; 调试接口
debug_server =                 ; 自定义 GDB server
debug_init_cmds =              ; GDB 初始化命令
debug_extra_cmds =             ; 额外 GDB 命令
debug_load_cmds =              ; 固件加载命令
debug_init_break = tbreak setup ; 初始断点
debug_speed = 4000             ; 调试速度 (kHz)
debug_server_ready_pattern =   ; server 就绪标志

; --- 环境级覆盖 ---
platform_packages =            ; 自定义平台包
board_build.partitions =       ; 分区表 (ESP32)
board_build.filesystem =       ; 文件系统类型
board_build.arduino.memory_type = ; 内存类型 (ESP32)

; --- 脚本 ---
extra_scripts =
    pre:scripts/pre_build.py
    post:scripts/post_build.py

; --- 容器/CI ---
platform_packages =            ; 覆盖平台包版本
```

## 配置继承

```ini
; 公共配置
[env]
framework = arduino
monitor_speed = 115200

; 继承公共配置
[env:uno]
platform = atmelavr
board = uno
; 自动继承 framework 和 monitor_speed

; 可以覆盖继承的值
[env:esp32]
platform = espressif32
board = esp32dev
monitor_speed = 9600  ; 覆盖公共设置
```

## 多环境构建

```bash
# 构建所有环境
platformio run

# 构建特定环境
platformio run -e uno

# 上传特定环境
platformio run -e esp32 -t upload

# 清理
platformio run -t clean

# 详细输出
platformio run -v
```

## 环境变量引用

```ini
[env:myboard]
; 引用系统环境变量
upload_port = ${sysenv.UPLOAD_PORT}

; 引用 PlatformIO 变量
build_dir = ${platformio.build_dir}

; 条件引用
upload_port = ${sysenv.UPLOAD_PORT|/dev/ttyUSB0}
```

## 高级：动态配置

```ini
; 使用 Python 脚本动态生成配置
[env:myboard]
extra_scripts = pre:scripts/config.py
```

```python
# scripts/config.py
Import("env")
import os

if os.environ.get("CI"):
    env.Append(BUILD_FLAGS=["-DCI_BUILD"])
```
