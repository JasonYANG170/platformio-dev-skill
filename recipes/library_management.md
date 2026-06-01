# 库管理

> **适用摘要**: 添加、管理和配置 PlatformIO 项目中的库依赖。

## 触发意图

- "添加库"
- "库依赖"
- "lib_deps"
- "安装库"
- "管理依赖"
- "Library Dependency Finder"

## 前置条件

| 条件 | 要求 |
|---|---|
| 项目 | 已有 platformio.ini |

## 调用链

```
Step 1: 确定需要的库
Step 2: 查找库的确切名称和版本
Step 3: 在 platformio.ini 中添加 lib_deps
Step 4: 配置 LDF 模式（如需要）
Step 5: 构建验证
```

## 分步说明

### Step 1: 确定需要的库

用户需要什么功能？常见库：
- 传感器: `Adafruit BME280`, `DHT sensor library`
- 显示: `Adafruit SSD1306`, `U8g2`
- 通信: `PubSubClient` (MQTT), `ArduinoJson`
- LED: `Adafruit NeoPixel`, `FastLED`
- 内置: `SPI`, `Wire`, `SD`

### Step 2: 查找库

```bash
# 搜索库
pio pkg search "neopixel"

# 查看库详情
pio pkg show "adafruit/Adafruit NeoPixel"
```

或访问 https://platformio.org/lib/ 搜索。

### Step 3: 添加 lib_deps

**标准格式** (推荐):
```ini
[env:myboard]
lib_deps =
    ; Registry: 作者/库名@版本
    adafruit/Adafruit NeoPixel@^1.10.0
    bblanchon/ArduinoJson@^6.21.0

    ; 内置框架库（无需版本）
    SPI
    Wire

    ; Git URL
    https://github.com/user/repo.git

    ; Git URL + 版本标签
    https://github.com/user/repo.git#v1.2.3

    ; 本地路径
    /path/to/local/lib
```

**版本指定语法**:
```
@^1.10.0    ; 兼容版本 (>=1.10.0, <2.0.0)
@~1.10.0    ; 最小补丁 (>=1.10.0, <1.11.0)
@1.10.4     ; 精确版本
@>=1.10.0   ; 最低版本
@*          ; 最新版本（不推荐）
```

### Step 4: 配置 LDF 模式

Library Dependency Finder 控制如何检测库依赖：

```ini
[env:myboard]
; 默认: 跟随 #include 链
lib_ldf_mode = chain

; 深度搜索（推荐，处理条件编译）
lib_ldf_mode = chain+

; 最深度搜索
lib_ldf_mode = deep+

; 关闭自动检测，只用 lib_deps
lib_ldf_mode = off
```

| 模式 | 说明 | 适用场景 |
|------|------|---------|
| `off` | 不自动检测 | 精确控制依赖 |
| `chain` | 跟随 include 链 | 大多数项目（默认） |
| `chain+` | chain + 条件编译搜索 | 使用 `#ifdef` 的库 |
| `deep` | 搜索所有源文件 | 复杂依赖关系 |
| `deep+` | deep + 条件编译 | 最复杂的情况 |

**兼容性模式**:
```ini
; 检查库与框架的兼容性
lib_compat_mode = strict   ; 严格检查（默认）
lib_compat_mode = off      ; 关闭兼容性检查
```

### Step 5: 构建验证

```bash
# 构建并查看库安装情况
platformio run -v

# 只安装库（不构建）
platformio pkg install

# 更新库
platformio pkg update

# 列出已安装库
platformio pkg list
```

## 库的项目结构

当创建自己的库时，遵循标准结构：

```
lib/
└── MyLib/
    ├── src/
    │   ├── MyLib.cpp
    │   └── utils.cpp
    ├── include/
    │   └── MyLib.h
    └── library.json          ; 可选：库元数据
```

**library.json** (可选):
```json
{
    "name": "MyLib",
    "version": "1.0.0",
    "description": "My custom library",
    "authors": [{
        "name": "Your Name",
        "email": "you@example.com"
    }],
    "frameworks": ["arduino"],
    "platforms": ["atmelavr", "espressif32"]
}
```

## 常见错误

| 错误 | 原因 | 解决方法 |
|---|---|---|
| `Library not found` | 库名拼写错误 | 在 registry 搜索确切名称 |
| `Version not found` | 指定的版本不存在 | 用 `pio pkg show` 查看可用版本 |
| `Multiple libraries found` | 同名库冲突 | 用 `作者/库名` 格式指定确切库 |
| `Dependency resolution failed` | 库之间版本冲突 | 手动指定兼容版本 |
| 库未被编译 | LDF 未检测到 | 改用 `lib_ldf_mode = chain+` 或 `deep+` |
| `lib_deps` 太长 | 多环境重复定义 | 使用 `[env]` 公共节定义共享库 |

## 参考项目

- `resources/EXAM/cicd-setup/platformio.ini` — lib_deps 使用示例（含 ArduinoFake）
- `resources/EXAM/unit-testing/arduino-mock/platformio.ini` — ArduinoFake 依赖示例
- `resources/platformio_ini_reference.md` — 完整配置参考
