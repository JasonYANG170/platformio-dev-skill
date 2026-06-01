# AGENTS.md — Supplementary Agent Guide

> Core rules, recipe index, and execution workflow are in `SKILL.md`.
> This file covers **only** conventions and tooling guidance not present in `SKILL.md`. Do not duplicate content.

## Project Context

**Language**: C/C++ · **Toolchain**: PlatformIO Core (CLI) + GCC/G++ per target platform · **Config**: platformio.ini

## Code Generation Conventions

### File Naming
- Entry point: `src/main.cpp` (C++), `src/main.c` (C), or `src/main.ino` (Arduino)
- Headers: `include/*.h` for public project headers
- Libraries: `lib/<LibName>/src/<LibName>.cpp` + `lib/<LibName>/include/<LibName>.h`
- Tests: `test/<test_dir>/test_*.cpp`
- Build scripts: `scripts/*.py` or `tools/*.py`

### Include Pattern
```cpp
// Arduino framework
#include <Arduino.h>

// STM32Cube framework
#include "stm32f4xx_hal.h"

// ESP-IDF framework
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

// Project libraries (auto-detected by LDF)
#include <MyLib.h>        // From lib/MyLib/

// PlatformIO build info
#include <Arduino.h>      // Framework header
```

### Standard Main Loop Pattern (Arduino)
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

### Standard Test Pattern (Unity)
```cpp
#include <unity.h>
#include <MyLib.h>

void test_addition(void) {
    TEST_ASSERT_EQUAL(4, add(2, 2));
}

void setUp(void) {}
void tearDown(void) {}

#ifdef ARDUINO
void setup() { delay(2000); UNITY_BEGIN(); RUN_TEST(test_addition); UNITY_END(); }
void loop() {}
#else
int main() { UNITY_BEGIN(); RUN_TEST(test_addition); return UNITY_END(); }
#endif
```

### Multi-Environment Test Pattern
```cpp// test/test_common/test_logic.cpp — runs on ALL environments
#include <unity.h>
void test_math(void) { TEST_ASSERT_EQUAL(5, 2 + 3); }
void setUp(void) {}
void tearDown(void) {}

#ifdef ARDUINO
#include <Arduino.h>
void setup() { delay(2000); UNITY_BEGIN(); RUN_TEST(test_math); UNITY_END(); }
void loop() {}
#else
int main() { UNITY_BEGIN(); RUN_TEST(test_math); return UNITY_END(); }
#endif

// test/test_embedded/test_hardware.cpp — runs on board only
#include <Arduino.h>
#include <unity.h>
void test_led_pin(void) {
    pinMode(LED_BUILTIN, OUTPUT);
    digitalWrite(LED_BUILTIN, HIGH);
    TEST_ASSERT_EQUAL(HIGH, digitalRead(LED_BUILTIN));
}
// ... same setup/loop/main pattern

// test/test_desktop/test_mock.cpp — runs on native only
#include <fakeit.hpp>
#include <unity.h>
void test_mock_example(void) {
    // ArduinoFake mocking
}
// ... same main pattern (no ARDUINO guard)
```

## Build Workflow

1. `platformio run` — Build all environments
2. `platformio run -e uno` — Build specific environment
3. `platformio run -t upload` — Build and upload
4. `platformio test` — Run all tests
5. `platformio test -e native -f unit` — Run specific tests on specific env
6. `platformio device monitor` — Serial monitor
7. `platformio debug` — Debug with probe

## Library Integration Checklist

- [ ] Library name matches PlatformIO registry (use `pio pkg search <name>`)
- [ ] Version pinned with `@^major.minor.patch` or `@exact`
- [ ] `lib_ldf_mode` set appropriately (chain+ for conditional includes)
- [ ] `lib_compat_mode` set if framework compatibility issues arise
- [ ] `lib_extra_dirs` used for local libraries outside `lib/`

## Test Configuration Checklist

- [ ] `test_framework` specified if not Unity (e.g., `googletest`)
- [ ] `test_ignore` separates embedded vs desktop test directories
- [ ] `test_build_src = true` if tests need access to `src/` code
- [ ] Native environment has `build_flags = -std=gnu++11` for C++ tests
- [ ] ArduinoFake in `lib_deps` if mocking Arduino APIs
- [ ] Test entry point has `#ifdef ARDUINO` guard for dual-environment support

## Do Not Modify

- `resources/` — Reference documentation source
- `SKILL.md` front matter — Skill metadata
