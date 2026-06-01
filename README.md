# PlatformIO 开发技能 (platformio-dev-skill)

PlatformIO 嵌入式开发 AI 技能。提供场景驱动的开发指南、配置参考、测试框架和常见陷阱。

支持 40+ 平台（AVR、STM32、ESP32、RISC-V、RP2040 等）和 25+ 框架（Arduino、STM32Cube、ESP-IDF、Zephyr、Mbed 等）。

## 安装

将此目录复制到 Claude Code 的技能目录，或在项目中直接引用：

```bash
# 方式 1：复制到全局技能目录
cp -r platformio-dev-skill ~/.claude/skills/

# 方式 2：在项目中使用
# 将 platformio-dev-skill/ 放在项目根目录
```

## 使用方法

在 Claude Code 中输入以下命令触发：

```
/platformio-dev-skill
```

或使用触发词：

- "创建 PlatformIO 项目"
- "配置 platformio.ini"
- "设置单元测试"
- "配置 CI/CD"
- "添加库依赖"
- "ESP32 Arduino"
- "STM32 开发"

## 技能结构

```
platformio-dev-skill/
├── SKILL.md              # 主技能定义（核心规则、工作流、陷阱）
├── AGENTS.md             # 代码生成约定和工具指南
├── README.md             # 本文件（中文）
├── README_EN.md          # 英文说明
├── CHANGELOG.md          # 版本历史
├── recipes/              # 场景化开发指南
│   ├── new_project.md    # 创建新项目
│   ├── multi_platform.md # 多平台构建
│   ├── unit_testing.md   # 单元测试
│   ├── ci_cd.md          # CI/CD 流水线
│   ├── library_management.md # 库管理
│   ├── build_flags.md    # 构建标志
│   ├── debugging.md      # 调试配置
│   └── custom_board.md   # 自定义开发板
└── resources/            # 参考文档
    ├── platformio_ini_reference.md  # platformio.ini 完整参考
    ├── project_structure.md         # 项目结构详解
    ├── testing_frameworks.md        # 测试框架对比
    ├── platform_list.md             # 平台列表
    ├── framework_list.md            # 框架列表
    └── pitfalls.md                  # 常见错误和解决方案
```

## 支持的平台

| 类别 | 平台 |
|------|------|
| 8 位 | atmelavr, atmelmegaavr, ststm8, intel_mcs51 |
| 32 位 ARM | ststm32, atmelsam, nxplpc, nxpimxrt, nordicnrf51/52, teensy, raspberrypi |
| RISC-V | sifive, gd32v, kendryte210, openhw |
| 无线 | espressif32, espressif8266, heltec-cubecell |
| 桌面 | native, linux_arm, linux_x86_64, windows_x86 |

## 支持的框架

| 类别 | 框架 |
|------|------|
| Arduino | arduino, energia |
| 厂商 SDK | espidf, stm32cube, cmsis, spl, fsp |
| RTOS | freertos, mbed, zephyr |
| 轻量级 | libopencm3, simba |

## 许可证

Apache License 2.0
