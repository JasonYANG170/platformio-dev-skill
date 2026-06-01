# CI/CD 流水线

> **适用摘要**: 为 PlatformIO 项目配置 CI/CD 流水线，实现自动化构建、测试和部署。

## 触发意图

- "配置 CI/CD"
- "GitHub Actions"
- "自动化构建"
- "自动化测试"
- "持续集成"
- "自动部署固件"

## 前置条件

| 条件 | 要求 |
|---|---|
| 项目 | 已有 platformio.ini 和源代码 |
| 测试 | 已有 `test/` 目录（可选） |
| Git | 项目已托管在 GitHub/GitLab |

## 调用链

```
Step 1: 确定 CI/CD 需求（构建/测试/部署）
Step 2: 创建 GitHub Actions workflow 文件
Step 3: 配置构建作业
Step 4: 配置测试作业
Step 5: 配置部署作业（可选）
Step 6: 推送验证
```

## 分步说明

### Step 1: 确定需求

| 需求 | 触发条件 |
|------|---------|
| 构建验证 | 每次 push 和 PR |
| 单元测试 | 每次 push 和 PR |
| 固件部署 | 合并到 main 分支 |

### Step 2: 创建 workflow 文件

创建 `.github/workflows/build.yml`:

### Step 3-5: 完整 CI/CD 配置

**基础版 — 构建 + 单元测试**:
```yaml
name: Build & Test
on: [push, pull_request]

jobs:
  test:
    name: Unit Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install PlatformIO
        run: pip install platformio

      - name: Run unit tests
        run: platformio test --environment native -f unit -v

  build:
    name: Build Firmware
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install PlatformIO
        run: pip install platformio

      - name: Build all environments
        run: platformio run
```

**高级版 — 构建 + 测试 + 部署**:
```yaml
name: Build, Test & Deploy
on: [push, pull_request]

jobs:
  test:
    name: Unit Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
      - name: Install PlatformIO
        run: pip install platformio
      - name: Run unit tests
        run: platformio test --environment native -f unit -v

  build:
    name: Build Firmware
    needs: test
    runs-on: ubuntu-latest
    strategy:
      matrix:
        env: [uno, esp32dev]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
      - name: Install PlatformIO
        run: pip install platformio
      - name: Build ${{ matrix.env }}
        run: platformio run -e ${{ matrix.env }}

  deploy:
    name: Deploy Firmware
    needs: build
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
      - name: Install PlatformIO
        run: pip install platformio
      - name: Build for deployment
        run: platformio run -e uno
      - name: Upload firmware artifact
        uses: actions/upload-artifact@v4
        with:
          name: firmware
          path: .pio/build/uno/firmware.hex
```

**GitLab CI 版本** (`.gitlab-ci.yml`):
```yaml
stages:
  - test
  - build
  - deploy

variables:
  PIP_CACHE_DIR: "$CI_PROJECT_DIR/.cache/pip"

cache:
  paths:
    - .cache/pip/
    - .pio/

unit-tests:
  stage: test
  image: python:3.11
  before_script:
    - pip install platformio
  script:
    - platformio test --environment native -f unit -v

build-firmware:
  stage: build
  image: python:3.11
  before_script:
    - pip install platformio
  script:
    - platformio run
  artifacts:
    paths:
      - .pio/build/
```

### Step 6: 推送验证

```bash
# 提交 workflow 文件
git add .github/workflows/build.yml
git commit -m "Add CI/CD pipeline"
git push

# 在 GitHub Actions 页面查看运行状态
```

## PlatformIO Remote (硬件测试)

对于需要真实硬件的集成测试：

```yaml
integration-tests:
  name: Hardware Tests
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-python@v5
      with:
        python-version: "3.11"
    - name: Install PlatformIO
      run: pip install platformio
    - name: Run integration tests
      run: platformio remote test --environment uno --ignore unit
      env:
        PLATFORMIO_AUTH_TOKEN: ${{ secrets.PLATFORMIO_AUTH_TOKEN }}
```

需要：
1. 注册 PlatformIO 账号
2. 获取 API Token
3. 在 GitHub repo settings 中添加 secret `PLATFORMIO_AUTH_TOKEN`
4. 配置 PlatformIO Remote Agent 连接硬件

## 常见错误

| 错误 | 原因 | 解决方法 |
|---|---|---|
| `pip: command not found` | 缺少 Python 步骤 | 添加 `actions/setup-python` |
| `platformio: command not found` | 未安装 PlatformIO | 添加 `pip install platformio` |
| 测试超时 | 测试运行时间过长 | 添加 `timeout-minutes: 10` |
| 构建矩阵失败 | 环境名拼写错误 | 检查 platformio.ini 中的环境名 |
| Remote 测试失败 | Token 未配置 | 在 repo secrets 中添加 `PLATFORMIO_AUTH_TOKEN` |

## 参考项目

- `resources/EXAM/cicd-setup/` — 完整 CI/CD 示例（GitHub Actions + 单元测试 + 集成测试 + 部署）
- `resources/EXAM/cicd-setup/.github/workflows/main.yml` — workflow 文件
- `recipes/unit_testing.md` — 单元测试配置
