[简体中文](README.md) | [English](README_EN.md)

# PlatformIO Development Skill (platformio-dev-skill)

AI Skill for PlatformIO embedded development. Provides scenario-driven recipes, configuration references, testing guides, and common pitfalls.

Supports 40+ platforms (AVR, STM32, ESP32, RISC-V, RP2040, etc.) and 25+ frameworks (Arduino, STM32Cube, ESP-IDF, Zephyr, Mbed, etc.).

## Installation

Copy this directory to Claude Code's skills directory, or reference it directly in your project:

```bash
# Option 1: Copy to global skills directory
cp -r platformio-dev-skill ~/.claude/skills/

# Option 2: Use in project
# Place platformio-dev-skill/ in the project root
```

## Usage

Trigger in Claude Code with:

```
/platformio-dev-skill
```

Or use trigger words:

- "Create a PlatformIO project"
- "Configure platformio.ini"
- "Set up unit tests"
- "Configure CI/CD"
- "Add library dependency"
- "ESP32 Arduino"
- "STM32 development"

## Skill Structure

```
platformio-dev-skill/
├── SKILL.md              # Main skill definition (core rules, workflow, pitfalls)
├── AGENTS.md             # Code generation conventions and tooling guide
├── README.md             # Chinese installation guide
├── README_EN.md          # This file (English)
├── CHANGELOG.md          # Version history
├── recipes/              # Scenario-driven development guides
│   ├── new_project.md    # Create new project
│   ├── multi_platform.md # Multi-platform builds
│   ├── unit_testing.md   # Unit testing setup
│   ├── ci_cd.md          # CI/CD pipeline
│   ├── library_management.md # Library management
│   ├── build_flags.md    # Build flags and defines
│   ├── debugging.md      # Debug probe configuration
│   └── custom_board.md   # Custom board definitions
└── resources/            # Reference documentation
    ├── platformio_ini_reference.md  # platformio.ini full reference
    ├── project_structure.md         # Project structure details
    ├── testing_frameworks.md        # Testing framework comparison
    ├── platform_list.md             # Platform list
    ├── framework_list.md            # Framework list
    └── pitfalls.md                  # Common errors and solutions
```

## Supported Platforms

| Category | Platforms |
|----------|-----------|
| 8-bit | atmelavr, atmelmegaavr, ststm8, intel_mcs51 |
| 32-bit ARM | ststm32, atmelsam, nxplpc, nxpimxrt, nordicnrf51/52, teensy, raspberrypi |
| RISC-V | sifive, gd32v, kendryte210, openhw |
| Wireless | espressif32, espressif8266, heltec-cubecell |
| Desktop | native, linux_arm, linux_x86_64, windows_x86 |

## Supported Frameworks

| Category | Frameworks |
|----------|------------|
| Arduino | arduino, energia |
| Vendor SDK | espidf, stm32cube, cmsis, spl, fsp |
| RTOS | freertos, mbed, zephyr |
| Lightweight | libopencm3, simba |

## License

Apache License 2.0
