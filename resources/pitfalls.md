# PlatformIO 常见错误和解决方案

## 1. Board ID 不存在

```ini
; ❌ WRONG — 拼写错误
board = arduino_uno
board = esp32-devkit

; ✅ CORRECT — 确切的 board ID
board = uno
board = esp32dev
```

**如何查找**: 运行 `pio boards <keyword>` 或访问 https://platformio.org/boards/

## 2. 框架与平台不兼容

```ini
; ❌ WRONG — native 不支持 Arduino
[env:native]
platform = native
framework = arduino

; ✅ CORRECT — native 无框架
[env:native]
platform = native
```

**检查方法**: `pio platform show <platform_id>` 查看支持的框架

## 3. 库版本冲突

```ini
; ❌ WRONG — 无版本约束，可能在更新后破坏
lib_deps = Adafruit NeoPixel

; ✅ CORRECT — 锁定主版本
lib_deps = Adafruit/Adafruit NeoPixel@^1.10.0

; ✅ 精确版本
lib_deps = Adafruit/Adafruit NeoPixel@1.10.4
```

## 4. 测试环境未分离

```ini
; ❌ WRONG — native 环境尝试运行硬件测试
[env:native]
platform = native
; 没有 test_ignore，会尝试编译 hardware-specific 测试

; ✅ CORRECT — 用 test_ignore 分离
[env:native]
platform = native
test_ignore = test_embedded

[env:uno]
platform = atmelavr
board = uno
test_ignore = test_desktop
```

## 5. 构建标志格式错误

```ini
; ❌ WRONG — 缺少 -D 前缀
build_flags = MY_DEFINE=1

; ❌ WRONG — 多余空格
build_flags = -D MY_DEFINE=1

; ✅ CORRECT
build_flags = -DMY_DEFINE=1

; ✅ 字符串值需要转义
build_flags = -DAPP_VERSION=\"1.0.0\"
```

## 6. 库未被自动检测

```ini
; 问题: 库有条件编译 (#ifdef)，LDF 检测不到
; 解决: 使用 chain+ 或 deep+ 模式
[env:myboard]
lib_ldf_mode = chain+
```

## 7. src_filter 语法错误

```ini
; ❌ WRONG — 尝试排除但语法不对
src_filter = -<examples/>

; ✅ CORRECT — 必须先包含再排除
src_filter = +<*> -<examples/> -<tests/>

; ✅ 只包含特定目录
src_filter = +<src/> +<lib/>
```

## 8. native 平台编译 C++ 失败

```ini
; ❌ WRONG — 默认 C 标准不支持 C++11 特性
[env:native]
platform = native

; ✅ CORRECT — 显式指定 C++ 标准
[env:native]
platform = native
build_flags = -std=gnu++11
```

## 9. 上传失败

```ini
; 常见原因:
; 1. 串口被占用（关闭串口监视器）
; 2. 端口错误
; 3. 波特率不匹配

; 解决:
[env:myboard]
upload_port = /dev/ttyUSB0      ; 明确指定端口
upload_speed = 115200            ; 匹配 bootloader 波特率
```

## 10. 库找不到 (Multiple libraries found)

```ini
; ❌ 模糊名称，可能匹配多个库
lib_deps = NeoPixel

; ✅ 用 作者/库名 格式精确指定
lib_deps = adafruit/Adafruit NeoPixel
```

## 11. ESP32 分区表问题

```ini
; 默认分区表可能不够用
; 解决: 指定自定义分区表
[env:esp32dev]
board_build.partitions = huge_app.csv
; 或自定义
board_build.partitions = custom_partitions.csv
```

## 12. STM32 启动文件错误

```ini
; STM32 需要正确的启动文件
; PlatformIO 通常自动处理，但自定义板可能需要:
[env:custom_stm32]
board_build.startup_file = startup_stm32f401xe.s
```

## 13. 内存溢出 (AVR)

```ini
; ATmega328P 只有 32KB Flash, 2KB RAM
[env:uno]
build_flags =
    -Os                ; 大小优化
    -ffunction-sections
    -fdata-sections
    -Wl,--gc-sections  ; 删除未使用代码

; 源代码中使用 PROGMEM 存储常量
; const char big_string[] PROGMEM = "...";
```

## 14. 链接器错误 undefined reference

```
原因: 库未正确链接
解决:
1. 检查 lib_deps 是否包含该库
2. 检查 lib_ldf_mode 是否足够 (chain+)
3. 检查 #include 路径是否正确
4. 确保库支持当前框架
```

## 15. 串口监视器乱码

```ini
; ❌ 波特率不匹配
monitor_speed = 9600

; ✅ 与 Serial.begin() 一致
monitor_speed = 115200
```

## 16. 构建缓存问题

```bash
# 清理构建缓存
platformio run -t clean

# 删除整个 .pio 目录
rm -rf .pio

# 重新构建
platformio run
```

## 17. Git 库依赖失败

```ini
; ❌ WRONG — 不支持的 URL 格式
lib_deps = https://github.com/user/repo

; ✅ CORRECT — 完整 .git URL
lib_deps = https://github.com/user/repo.git

; ✅ 指定版本标签
lib_deps = https://github.com/user/repo.git#v1.2.3

; ✅ 指定分支
lib_deps = https://github.com/user/repo.git#develop
```

## 18. PlatformIO 版本过旧

```bash
# 更新 PlatformIO Core
pip install -U platformio

# 检查版本
platformio --version

# 更新所有平台和工具
platformio platform update
platformio pkg update
```

## 19. VS Code IntelliSense 不工作

```bash
# 重新生成 IntelliSense 配置
platformio init --ide vscode

# 或在 VS Code 中
# Ctrl+Shift+P -> PlatformIO: Rebuild IntelliSense Index
```

## 20. 调试探针连接失败

```ini
; 常见原因:
; 1. 驱动未安装
; 2. 探针被其他程序占用
; 3. 接口类型错误 (SWD vs JTAG)

; 解决:
[env:myboard]
debug_tool = stlink        ; 明确指定探针类型
debug_interface = swd      ; 明确指定接口
debug_speed = 4000         ; 降低速度提高稳定性
```
