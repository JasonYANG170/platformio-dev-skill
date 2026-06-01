# 测试框架对比

## 框架概览

| 框架 | 语言 | 平台 | Mock 支持 | 复杂度 |
|------|------|------|----------|--------|
| **Unity** | C | 嵌入式 + 桌面 | 无 | 低 |
| **GoogleTest** | C++ | 桌面 + 嵌入式 | gmock | 中高 |
| **ArduinoFake** | C++ | 桌面 (native) | FakeIt | 中 |

## Unity (默认)

PlatformIO 默认测试框架，轻量级，适合嵌入式。

### platformio.ini
```ini
[env:test]
platform = native
test_framework = unity     ; 默认值，可省略
```

### 特点
- 纯 C 实现，适合资源受限环境
- 跨平台：嵌入式和桌面
- setUp/tearDown 测试夹具
- 丰富的断言宏

### 常用断言
```cpp
TEST_ASSERT_EQUAL(expected, actual)
TEST_ASSERT_EQUAL_INT(expected, actual)
TEST_ASSERT_EQUAL_STRING(expected, actual)
TEST_ASSERT_TRUE(condition)
TEST_ASSERT_FALSE(condition)
TEST_ASSERT_NULL(pointer)
TEST_ASSERT_NOT_NULL(pointer)
TEST_ASSERT_FLOAT_WITHIN(delta, expected, actual)
TEST_ASSERT_GREATER_THAN(threshold, actual)
TEST_ASSERT_LESS_THAN(threshold, actual)
TEST_ASSERT_EQUAL_INT_ARRAY(expected, actual, elements)
TEST_IGNORE()  // 跳过当前测试
```

### 完整示例
```cpp
#include <unity.h>

void setUp(void) { /* 每个测试前执行 */ }
void tearDown(void) { /* 每个测试后执行 */ }

void test_addition(void) {
    TEST_ASSERT_EQUAL(4, 2 + 2);
}

void test_string(void) {
    TEST_ASSERT_EQUAL_STRING("hello", get_greeting());
}

#ifdef ARDUINO
#include <Arduino.h>
void setup() {
    delay(2000);
    UNITY_BEGIN();
    RUN_TEST(test_addition);
    RUN_TEST(test_string);
    UNITY_END();
}
void loop() {}
#else
int main() {
    UNITY_BEGIN();
    RUN_TEST(test_addition);
    RUN_TEST(test_string);
    return UNITY_END();
}
#endif
```

## GoogleTest

功能丰富的 C++ 测试框架，支持 mock。

### platformio.ini
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

### 特点
- C++ 原生，支持模板和异常
- GoogleMock 提供强大的 mock 功能
- 参数化测试
- 测试套件和测试夹具
- 更丰富的断言

### 常用断言
```cpp
// EXPECT_* 失败后继续执行
EXPECT_EQ(expected, actual)
EXPECT_NE(expected, actual)
EXPECT_LT(val1, val2)
EXPECT_GT(val1, val2)
EXPECT_TRUE(condition)
EXPECT_FALSE(condition)
EXPECT_STREQ(expected, actual)      // C 字符串
EXPECT_STRNE(expected, actual)
EXPECT_FLOAT_EQ(expected, actual)
EXPECT_NEAR(expected, actual, abs_error)
EXPECT_THROW(statement, exception_type)
EXPECT_NO_THROW(statement)
EXPECT_THAT(value, matcher)

// ASSERT_* 失败后立即终止当前测试
ASSERT_EQ(expected, actual)
// ... 同 EXPECT_*
```

### 完整示例
```cpp
#include <gtest/gtest.h>

// 基本测试
TEST(CalculatorTest, Addition) {
    EXPECT_EQ(4, add(2, 2));
}

TEST(CalculatorTest, Subtraction) {
    EXPECT_EQ(0, sub(5, 5));
}

// 测试夹具
class StackTest : public ::testing::Test {
protected:
    void SetUp() override {
        stack.push(1);
        stack.push(2);
        stack.push(3);
    }
    std::stack<int> stack;
};

TEST_F(StackTest, PopReturnsTop) {
    EXPECT_EQ(3, stack.top());
    stack.pop();
    EXPECT_EQ(2, stack.top());
}

// 参数化测试
class IsPrimeParamTest : public ::testing::TestWithParam<int> {};

TEST_P(IsPrimeParamTest, CheckPrime) {
    int n = GetParam();
    EXPECT_TRUE(is_prime(n));
}

INSTANTIATE_TEST_SUITE_P(PrimeValues, IsPrimeParamTest,
    ::testing::Values(2, 3, 5, 7, 11, 13));
```

### GoogleMock 示例
```cpp
#include <gmock/gmock.h>
#include <gtest/gtest.h>

class MockSensor {
public:
    MOCK_METHOD(float, readTemperature, (), ());
    MOCK_METHOD(float, readHumidity, (), ());
};

TEST(SensorTest, ReadsTemperature) {
    MockSensor sensor;
    EXPECT_CALL(sensor, readTemperature())
        .WillOnce(::testing::Return(25.5f));

    float temp = sensor.readTemperature();
    EXPECT_FLOAT_EQ(25.5f, temp);
}
```

## ArduinoFake

Mock Arduino API 的库，用于在桌面环境测试 Arduino 代码。

### platformio.ini
```ini
[env:native]
platform = native
build_flags = -std=gnu++11
lib_deps = ArduinoFake
```

### 特点
- Mock Arduino 核心函数 (digitalRead, digitalWrite, Serial, etc.)
- 基于 FakeIt mocking 框架
- When/Verify 模式
- 只在 native 平台可用

### 常用 API
```cpp
#include <fakeit.hpp>
using namespace fakeit;

// 重置所有 mock
ArduinoFakeReset();

// Mock 自由函数
When(Method(ArduinoFake(), digitalRead)).Return(HIGH);
When(Method(ArduinoFake(), analogRead)).Return(512);

// Mock 类方法
When(Method(ArduinoFake(Serial), begin)).Return();
When(Method(ArduinoFake(Serial), print)).Return();

// Mock 重载方法
When(OverloadedMethod(ArduinoFake(Client), connect,
    int(const char*, uint16_t))).Return(1);

// 验证调用
Verify(Method(ArduinoFake(), digitalRead)).Once();
Verify(Method(ArduinoFake(), digitalWrite).Using(13, HIGH)).Once();
Verify(Method(ArduinoFake(), digitalRead)).Exactly(3_Times);

// 创建 mock 对象
Client* mockClient = ArduinoFakeMock(Client);
```

### 完整示例
```cpp
#include <Arduino.h>
#include <unity.h>
#include <fakeit.hpp>

using namespace fakeit;

void setUp(void) {
    ArduinoFakeReset();
}

void test_led_control(void) {
    When(Method(ArduinoFake(), digitalWrite)).Return();
    When(Method(ArduinoFake(), digitalRead)).Return(HIGH);

    // 测试 LED 控制函数
    set_led(true);

    Verify(Method(ArduinoFake(), digitalWrite).Using(LED_BUILTIN, HIGH)).Once();
}

void test_sensor_read(void) {
    When(Method(ArduinoFake(), analogRead)).Return(512);

    int value = read_sensor(0);
    TEST_ASSERT_EQUAL(512, value);

    Verify(Method(ArduinoFake(), analogRead).Using(0)).Once();
}

int main() {
    UNITY_BEGIN();
    RUN_TEST(test_led_control);
    RUN_TEST(test_sensor_read);
    return UNITY_END();
}
```

## 选择建议

| 场景 | 推荐框架 |
|------|---------|
| 简单 C 函数测试 | Unity |
| 嵌入式硬件测试 | Unity |
| C++ 类和模板测试 | GoogleTest |
| 需要 mock 硬件 API | ArduinoFake |
| 复杂项目，需要参数化测试 | GoogleTest |
| CI/CD 快速反馈 | Unity 或 GoogleTest (native) |
| Arduino 项目单元测试 | ArduinoFake + Unity |

## 混合使用

可以在不同环境使用不同框架：

```ini
[env:native]
platform = native
test_framework = googletest
test_ignore = test_embedded

[env:uno]
platform = atmelavr
board = uno
test_framework = unity
test_ignore = test_desktop
```
