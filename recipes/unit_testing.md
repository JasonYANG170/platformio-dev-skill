# 单元测试

> **适用摘要**: 为 PlatformIO 项目设置单元测试，支持 Unity、GoogleTest 和 ArduinoFake 框架。

## 触发意图

- "设置单元测试"
- "写测试"
- "Unity 测试框架"
- "GoogleTest"
- "ArduinoFake mock"
- "测试驱动开发"
- "TDD"

## 前置条件

| 条件 | 要求 |
|---|---|
| PlatformIO Core | 已安装 |
| 项目 | 已有 `src/` 目录和源代码 |

## 调用链

```
Step 1: 选择测试框架（Unity/GoogleTest/ArduinoFake）
Step 2: 创建测试目录结构
Step 3: 配置 platformio.ini 测试环境
Step 4: 编写测试用例
Step 5: 运行测试
```

## 分步说明

### Step 1: 选择测试框架

| 框架 | 适用场景 | 特点 |
|------|---------|------|
| **Unity** | 通用，PlatformIO 默认 | 轻量级，跨平台，TEST_ASSERT 宏 |
| **GoogleTest** | 复杂项目，需要 mock | 功能丰富，支持 gmock |
| **ArduinoFake** | 需要 mock Arduino API | 基于 FakeIt，mock 硬件函数 |

### Step 2: 创建测试目录结构

```
MyProject/
├── src/
│   └── main.cpp
├── lib/
│   └── calculator/
│       ├── src/
│       │   ├── calculator.h
│       │   └── calculator.cpp
│       └── include/
├── test/
│   ├── test_common/        # 所有环境运行
│   │   └── test_calculator.cpp
│   ├── test_embedded/      # 仅硬件环境运行
│   │   └── test_hardware.cpp
│   └── test_desktop/       # 仅 native 运行
│       └── test_mock.cpp
└── platformio.ini
```

### Step 3: 配置 platformio.ini

**Unity (默认)**:
```ini
[env:uno]
platform = atmelavr
framework = arduino
board = uno
test_ignore = test_desktop

[env:native]
platform = native
test_ignore = test_embedded
```

**GoogleTest**:
```ini
[env:native]
platform = native
test_framework = googletest

[env:esp32dev]
platform = espressif32
framework = arduino
board = esp32dev
test_framework = googletest
```

**ArduinoFake (Mock Arduino API)**:
```ini
[env:native]
platform = native
build_flags = -std=gnu++11
lib_deps = ArduinoFake
test_ignore = test_embedded
```

**访问 src/ 代码**:
```ini
[env:native]
platform = native
test_build_src = true    ; 让测试能访问 src/ 中的代码
test_ignore = test_embedded
```

### Step 4: 编写测试用例

**Unity 基本测试**:
```cpp
// test/test_common/test_calculator.cpp
#include <calculator.h>
#include <unity.h>

Calculator calc;

void setUp(void) {}
void tearDown(void) {}

void test_addition(void) {
    TEST_ASSERT_EQUAL(32, calc.add(25, 7));
}

void test_subtraction(void) {
    TEST_ASSERT_EQUAL(20, calc.sub(23, 3));
}

// 跳过某个测试
void test_expensive(void) {
    TEST_IGNORE();  // 暂时跳过
}

#ifdef ARDUINO
#include <Arduino.h>
void setup() {
    delay(2000);
    UNITY_BEGIN();
    RUN_TEST(test_addition);
    RUN_TEST(test_subtraction);
    UNITY_END();
}
void loop() {}
#else
int main() {
    UNITY_BEGIN();
    RUN_TEST(test_addition);
    RUN_TEST(test_subtraction);
    return UNITY_END();
}
#endif
```

**Unity 常用断言**:
```cpp
TEST_ASSERT_EQUAL(expected, actual)          // 整数相等
TEST_ASSERT_EQUAL_STRING(expected, actual)   // 字符串相等
TEST_ASSERT_TRUE(condition)                  // 条件为真
TEST_ASSERT_FALSE(condition)                 // 条件为假
TEST_ASSERT_FLOAT_WITHIN(delta, expected, actual)  // 浮点近似
TEST_ASSERT_GREATER_THAN(threshold, actual)  // 大于
TEST_ASSERT_LESS_THAN(threshold, actual)     // 小于
TEST_ASSERT_NULL(pointer)                    // 指针为 NULL
TEST_ASSERT_NOT_NULL(pointer)               // 指针非 NULL
```

**GoogleTest 测试**:
```cpp
// test/test_gtest/test_math.cpp
#include <gtest/gtest.h>
#include <calculator.h>

TEST(CalculatorTest, Addition) {
    Calculator calc;
    EXPECT_EQ(32, calc.add(25, 7));
}

TEST(CalculatorTest, DivisionByZero) {
    Calculator calc;
    EXPECT_THROW(calc.div(1, 0), std::runtime_error);
}
```

**ArduinoFake Mock 测试**:
```cpp
// test/test_desktop/test_mock.cpp
#include <Arduino.h>
#include <unity.h>
#include <fakeit.hpp>

using namespace fakeit;

void setUp(void) {
    ArduinoFakeReset();
}

void test_digitalRead_mock(void) {
    When(Method(ArduinoFake(), digitalRead)).Return(HIGH);

    int val = digitalRead(13);
    TEST_ASSERT_EQUAL(HIGH, val);

    Verify(Method(ArduinoFake(), digitalRead)).Once();
}

void test_client_mock(void) {
    When(Method(ArduinoFake(Client), stop)).AlwaysReturn();
    When(Method(ArduinoFake(Client), available)).Return(1, 1, 0);
    When(OverloadedMethod(ArduinoFake(Client), read, int())).Return(2, 0);

    Client* client = ArduinoFakeMock(Client);
    // 使用 mock client 测试业务逻辑...

    Verify(Method(ArduinoFake(Client), stop)).Once();
}

int main() {
    UNITY_BEGIN();
    RUN_TEST(test_digitalRead_mock);
    RUN_TEST(test_client_mock);
    return UNITY_END();
}
```

**RUN_UNITY_TESTS 包装函数**（双环境推荐模式，参考 `resources/EXAM/unit-testing/calculator/test/test_desktop/`）:

```cpp
#include <calculator.h>
#include <unity.h>

Calculator calc;

void setUp(void) {}
void tearDown(void) {}

void test_addition(void) { TEST_ASSERT_EQUAL(32, calc.add(25, 7)); }
void test_subtraction(void) { TEST_ASSERT_EQUAL(20, calc.sub(23, 3)); }

// 将测试注册封装到函数中，Arduino 和 native 都调用同一函数
void RUN_UNITY_TESTS() {
    UNITY_BEGIN();
    RUN_TEST(test_addition);
    RUN_TEST(test_subtraction);
    UNITY_END();
}

#ifdef ARDUINO
#include <Arduino.h>
void setup() { delay(2000); RUN_UNITY_TESTS(); }
void loop() {}
#else
int main(int argc, char **argv) { RUN_UNITY_TESTS(); return 0; }
#endif
```

**自定义 Unity 输出到 UART**（参考 `resources/EXAM/unit-testing/stm32cube/test/unity_config.h`）:

```c
// unity_config.h — 将 Unity 输出重定向到 UART
#include "stm32f4xx_hal.h"

extern UART_HandleTypeDef huart2;

#define UNITY_OUTPUT_START()    MX_USART2_UART_Init()
#define UNITY_OUTPUT_CHAR(a)    unityOutputChar(a)
#define UNITY_OUTPUT_FLUSH()    unityOutputFlush()
#define UNITY_OUTPUT_COMPLETE() unityOutputComplete()

void unityOutputChar(char c);
void unityOutputFlush(void);
```

```c
// unity_config.c
#include "unity_config.h"

void unityOutputChar(char c) {
    HAL_UART_Transmit(&huart2, (uint8_t*)&c, 1, 100);
}

void unityOutputFlush(void) {}
```

**自定义 Unity 输出到 Semihosting**（参考 `resources/EXAM/unit-testing/semihosting/test/unity_config.c`）:

```c
// 通过调试器输出，无需 UART 硬件
#include <stdio.h>

#define UNITY_OUTPUT_CHAR(a)    putchar(a)
#define UNITY_OUTPUT_FLUSH()    fflush(stdout)
#define UNITY_OUTPUT_START()
#define UNITY_OUTPUT_COMPLETE()
```

**GoogleTest Arduino 双环境**（参考 `resources/EXAM/unit-testing/googletest/test/test_gtest/`）:

```cpp
#include <gtest/gtest.h>

TEST(DummyTest, ShouldPass) {
    EXPECT_EQ(1, 1);
}

TEST(SkipTest, DoesSkip) {
    GTEST_SKIP() << "Skipping this test";
}

#if defined(ARDUINO)
#include <Arduino.h>
void setup() {
    delay(2000);  // 等待 Serial DTR/RTS
    ::testing::InitGoogleTest();
}
void loop() {
    if (RUN_ALL_TESTS()) delay(1000);
}
#else
int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
#endif
```

**GoogleMock**（参考 `resources/EXAM/unit-testing/googletest/test/test_gmock/`）:

```cpp
#include <gtest/gtest.h>
#include <gmock/gmock.h>

// 定义接口
class Turtle {
public:
    virtual ~Turtle() {}
    virtual void PenUp() = 0;
    virtual void PenDown() = 0;
    virtual void Forward(int distance) = 0;
    virtual void Turn(int degrees) = 0;
    virtual int GetX() const = 0;
    virtual int GetY() const = 0;
};

// Mock 实现
class MockTurtle : public Turtle {
public:
    MOCK_METHOD(void, PenUp, (), (override));
    MOCK_METHOD(void, PenDown, (), (override));
    MOCK_METHOD(void, Forward, (int distance), (override));
    MOCK_METHOD(void, Turn, (int degrees), (override));
    MOCK_METHOD(int, GetX, (), (const, override));
    MOCK_METHOD(int, GetY, (), (const, override));
};

// 测试
TEST(PainterTest, CanDrawSomething) {
    MockTurtle turtle;
    EXPECT_CALL(turtle, PenDown()).Times(::testing::AtLeast(1));
    EXPECT_CALL(turtle, Forward(100));
    EXPECT_CALL(turtle, PenUp());

    // 使用 mock 对象测试 Painter 类
}
```

**STM32Cube 硬件测试**（参考 `resources/EXAM/unit-testing/stm32cube/test/test_led/`）:

```c
// test/test_led/test_main.c — 真实硬件 GPIO 测试
#include "stm32f4xx_hal.h"
#include "main.h"
#include <unity.h>

void setUp(void) {
    // 每个测试前初始化 LED GPIO
    GPIO_InitTypeDef GPIO_InitStruct = {0};
    __HAL_RCC_GPIOA_CLK_ENABLE();
    GPIO_InitStruct.Pin = LED_PIN;
    GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
    GPIO_InitStruct.Pull = GPIO_NOPULL;
    GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
    HAL_GPIO_Init(LED_GPIO_PORT, &GPIO_InitStruct);
}

void tearDown(void) {
    // 每个测试后反初始化
    HAL_GPIO_DeInit(LED_GPIO_PORT, LED_PIN);
}

void test_led_state_high(void) {
    HAL_GPIO_WritePin(LED_GPIO_PORT, LED_PIN, GPIO_PIN_SET);
    TEST_ASSERT_EQUAL(GPIO_PIN_SET, HAL_GPIO_ReadPin(LED_GPIO_PORT, LED_PIN));
}

void test_led_state_low(void) {
    HAL_GPIO_WritePin(LED_GPIO_PORT, LED_PIN, GPIO_PIN_RESET);
    TEST_ASSERT_EQUAL(GPIO_PIN_RESET, HAL_GPIO_ReadPin(LED_GPIO_PORT, LED_PIN));
}

int main(void) {
    HAL_Init();
    UNITY_BEGIN();
    RUN_TEST(test_led_state_high);
    RUN_TEST(test_led_state_low);
    UNITY_END();
    while(1) {}  // 嵌入式不退出
}
```

### Step 5: 运行测试

```bash
# 运行所有测试
platformio test

# 运行特定环境的测试
platformio test -e native

# 运行特定测试文件
platformio test -e native -f test_calculator

# 详细输出
platformio test -e native -v

# 只运行嵌入式测试
platformio test -e uno -f test_embedded
```

## 常见错误

| 错误 | 原因 | 解决方法 |
|---|---|---|
| `test_calculator.cpp not found` | 测试文件不在 `test/` 目录下 | 确保文件在 `test/<dir>/test_*.cpp` |
| `undefined reference to main` | 缺少 main 函数 | 添加 `#ifdef ARDUINO` ... `#else` ... `#endif` 模板 |
| `ArduinoFake not found` | 未在 lib_deps 中添加 | 添加 `lib_deps = ArduinoFake` |
| native 构建失败 | 缺少 C++11 支持 | 添加 `build_flags = -std=gnu++11` |
| 测试运行在错误环境 | test_ignore 未配置 | 配置 `test_ignore` 分隔嵌入式/桌面测试 |
| `TEST_ASSERT_EQUAL` 浮点失败 | 浮点精度问题 | 使用 `TEST_ASSERT_FLOAT_WITHIN` |

## 参考项目

- `resources/EXAM/unit-testing/calculator/` — Unity 测试完整示例（双环境：embedded + native）
- `resources/EXAM/unit-testing/arduino-mock/` — ArduinoFake mock 示例（mock Arduino API）
- `resources/EXAM/unit-testing/googletest/` — GoogleTest 示例（gtest + gmock）
- `resources/EXAM/unit-testing/stm32cube/` — STM32Cube HAL 测试
- `resources/EXAM/unit-testing/semihosting/` — Semihosting 调试测试（OpenOCD）
- `resources/testing_frameworks.md` — 测试框架对比
