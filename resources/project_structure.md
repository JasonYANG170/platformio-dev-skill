# PlatformIO 项目结构详解

## 标准目录结构

```
MyProject/
├── platformio.ini          # 项目配置文件（必需）
├── src/                    # 源代码（必需）
│   ├── main.cpp            # 主入口点
│   ├── module1.cpp         # 其他源文件
│   └── module1.h
├── include/                # 公共头文件
│   └── config.h
├── lib/                    # 项目库
│   └── MyLib/
│       ├── src/
│       │   └── MyLib.cpp
│       └── include/
│           └── MyLib.h
├── test/                   # 单元测试
│   ├── test_common/        # 所有环境
│   ├── test_embedded/      # 仅硬件
│   └── test_desktop/       # 仅 native
├── data/                   # 文件系统数据 (SPIFFS, LittleFS)
├── docs/                   # 文档
├── boards/                 # 自定义板定义
│   └── my_board.json
├── scripts/                # 构建脚本
│   └── pre_build.py
├── tools/                  # 工具脚本
├── .github/                # GitHub 配置
│   └── workflows/
│       └── build.yml
├── .gitignore
└── README.md
```

## 目录说明

### src/ — 源代码

- **必需目录**，PlatformIO 从此目录编译源文件
- 入口文件: `main.cpp`, `main.c`, 或 `main.ino`
- 支持子目录组织代码
- 通过 `src_filter` 控制编译范围

```
src/
├── main.cpp                # 入口点
├── drivers/                # 驱动代码
│   ├── sensor.cpp
│   └── sensor.h
├── network/                # 网络模块
│   ├── wifi.cpp
│   └── mqtt.cpp
└── utils/                  # 工具函数
    └── logger.cpp
```

### include/ — 公共头文件

- 对所有源文件可见
- 适合放配置文件、常量定义
- 通过 `-I include` 自动添加到 include path

### lib/ — 项目库

- 项目私有库，不上传到 PlatformIO Registry
- PlatformIO 自动检测并编译
- 支持 `library.json` 元数据

```
lib/
├── MySensor/
│   ├── src/
│   │   ├── MySensor.cpp
│   │   └── helpers.cpp
│   ├── include/
│   │   └── MySensor.h
│   └── library.json        # 可选
└── MyDisplay/
    ├── src/
    │   └── MyDisplay.cpp
    └── include/
        └── MyDisplay.h
```

**library.json**:
```json
{
    "name": "MySensor",
    "version": "1.0.0",
    "description": "Sensor driver library",
    "authors": [{
        "name": "Your Name"
    }],
    "frameworks": ["arduino"],
    "platforms": ["atmelavr", "espressif32"],
    "dependencies": [
        {
            "name": "Wire",
            "frameworks": "arduino"
        }
    ]
}
```

### test/ — 单元测试

```
test/
├── test_common/            # 所有环境运行
│   └── test_math.cpp
├── test_embedded/          # 仅嵌入式环境
│   └── test_hardware.cpp
└── test_desktop/           # 仅 native 环境
    └── test_mock.cpp
```

测试文件命名规则：
- 必须以 `test_` 开头
- 扩展名: `.cpp`, `.c`
- 每个文件包含 `main()` 或 Arduino `setup()/loop()`

### data/ — 文件系统数据

用于 ESP32/ESP8266 的 SPIFFS/LittleFS 文件系统：

```
data/
├── index.html
├── style.css
└── config.json
```

上传文件系统：
```bash
platformio run -t uploadfs
```

### boards/ — 自定义板定义

```
boards/
├── my_esp32_board.json
└── my_stm32_board.json
```

在 `platformio.ini` 中使用：
```ini
[env:myboard]
board = my_esp32_board
```

### scripts/ — 构建脚本

```
scripts/
├── pre_build.py            # 构建前脚本
├── post_build.py           # 构建后脚本
└── version_gen.py          # 版本生成
```

在 `platformio.ini` 中使用：
```ini
[env:myboard]
extra_scripts =
    pre:scripts/pre_build.py
    post:scripts/post_build.py
```

## 文件命名约定

| 文件类型 | 命名规则 | 示例 |
|---------|---------|------|
| C++ 源文件 | `*.cpp` | `main.cpp`, `sensor.cpp` |
| C 源文件 | `*.c` | `driver.c` |
| Arduino 草图 | `*.ino` | `main.ino` |
| 头文件 | `*.h` | `config.h`, `sensor.h` |
| 测试文件 | `test_*.cpp` | `test_calculator.cpp` |
| 板定义 | `*.json` | `my_board.json` |
| 构建脚本 | `*.py` | `pre_build.py` |
| 链接脚本 | `*.ld` | `custom.ld` |

## .gitignore 推荐

```gitignore
# PlatformIO
.pio
.pioenvs
.piolibdeps
.vscode/.browse.c_cpp.db*
.vscode/c_cpp_properties.json
.vscode/launch.json
.vscode/ipch

# 固件输出
*.bin
*.hex
*.elf
*.map

# 数据目录上传文件
data/
```

## 从 Arduino IDE 迁移

```
Arduino Project/            →   PlatformIO Project/
├── MyProject.ino           →   ├── src/main.ino (或 main.cpp)
├── MyLib.h                 →   ├── include/MyLib.h
├── MyLib.cpp               →   ├── src/MyLib.cpp
├── libraries/              →   └── lib_deps in platformio.ini
└── (no config)             →   └── platformio.ini
```

迁移步骤：
1. 创建 `platformio.ini`
2. 将 `.ino` 移到 `src/`
3. 将头文件移到 `include/`
4. 将库添加到 `lib_deps`
5. 运行 `platformio run` 验证
