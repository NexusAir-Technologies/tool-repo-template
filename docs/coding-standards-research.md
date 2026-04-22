# 编码规范调研报告

> NexusAir-Technologies 组织编码规范制定参考
> 调研日期：2026-04-22

## 调研范围

| 项目/标准 | 领域 | 来源 |
|-----------|------|------|
| Linux Kernel | 操作系统内核（纯 C） | kernel.org/doc/html/latest/process/coding-style.html |
| PX4 Autopilot | 飞控固件（C/C++） | docs.px4.io/main/en/contribute/code.html |
| ArduPilot | 飞控固件（C/C++） | ardupilot.org/dev/docs/style-guide.html |
| Google C++ Style Guide | 通用 C++ | google.github.io/styleguide/cppguide.html |
| Google Python Style Guide | 通用 Python | google.github.io/styleguide/pyguide.html |
| MISRA C:2012 | 安全关键嵌入式 C | misra.org.uk |
| BARR-C | 嵌入式 C | barrgroup.com/embedded-systems/books/embedded-c-coding-standard |

---

## 一、C/C++ 命名风格对比

### 1.1 命名法总表

| 元素 | Linux Kernel | PX4 | ArduPilot | Google C++ | BARR-C |
|------|-------------|-----|-----------|------------|--------|
| 局部变量 | `snake_case`（短名优先） | `snake_case` | `snake_case` | `snake_case` | `snake_case` |
| 全局变量 | `snake_case`（必须描述性） | `snake_case` | `snake_case` | `snake_case` | `模块前缀_snake_case` |
| 函数 | `snake_case` | `lowerCamelCase()` | `snake_case` | `PascalCase()` | `模块前缀_snake_case()` |
| 类名 | N/A（纯 C） | `UpperCamelCase` | `AP_Under_Score` | `PascalCase` | N/A |
| 类成员变量 | N/A | `_前缀_snake_case` | `_前缀`可选 | `snake_case_`（末尾下划线） | N/A |
| 结构体成员 | `snake_case` | `snake_case` | `snake_case` | `snake_case`（无后缀） | `snake_case` |
| 常量 | `UPPER_SNAKE_CASE` | `kCamelCase` | `UPPER_SNAKE_CASE` | `kPascalCase` | `UPPER_SNAKE_CASE` |
| 枚举类型 | `enum name` | `enum class Name` | `enum class Name` | `enum class Name` | `enum name` |
| 枚举值 | `UPPER_SNAKE_CASE` | `UPPER_SNAKE_CASE` | `UPPER_SNAKE_CASE` | `kPascalCase` | `UPPER_SNAKE_CASE` |
| 宏 | `UPPER_SNAKE_CASE` | `UPPER_SNAKE_CASE` | `UPPER_SNAKE_CASE` | `UPPER_SNAKE_CASE` | `UPPER_SNAKE_CASE` |
| typedef | 极少使用 | 正常使用 | 正常使用 | 用 `using` 替代 | `模块_xxx_t` 后缀 |
| 文件名 | `snake_case.c/.h` | `snake_case.cpp/.h` | `snake_case.cpp/.h` | `snake_case.cc/.h` | `snake_case.c/.h` |
| 命名空间 | N/A | `snake_case` | N/A | `snake_case` | N/A |

### 1.2 各项目命名示例

**Linux Kernel（纯 C，snake_case 为主）：**
```c
int count_active_users(void);          // 函数：描述性 snake_case
static int tmp;                        // 局部变量：短名
#define CONSTANT 0x12345               // 宏：全大写
struct virtual_container *a;           // 结构体：不用 typedef 包装
```

**PX4（C++，混合风格）：**
```cpp
class VehicleAttitudeEstimator;                    // 类：UpperCamelCase
float doSomething(const float input_param) const;  // 方法：lowerCamelCase
float _private_member_variable;                    // 私有成员：_前缀 snake_case
static constexpr float kMaxYawRate = 0.52f;        // 常量：k前缀 CamelCase
const float in_scope_variable = 1.0f;              // 局部变量：snake_case
PARAM_DEFINE_FLOAT(MC_PITCH_P, 6.5f);             // 参数：全大写_下划线
```

**ArduPilot（C++，AP_ 前缀体系）：**
```cpp
class AP_Compass;                      // 类：AP_ 前缀 + 下划线分隔
void get_angle_rad();                  // 函数：snake_case
float distance_m;                      // 变量：必须带单位后缀
uint16_t get_distance_ned_cm();        // 返回值也带单位后缀
enum class CompassType { FOO, BAR, };  // 枚举：PascalCase + 全大写值
```

**Google C++（PascalCase 函数，k前缀常量）：**
```cpp
class UrlTable;                        // 类：PascalCase
void AddTableEntry();                  // 函数：PascalCase
std::string table_name;                // 局部变量：snake_case
std::string table_name_;               // 类成员：末尾下划线
const int kDaysInAWeek = 7;            // 常量：k前缀 PascalCase
int count();                           // getter：snake_case 例外
void set_count(int count);             // setter：snake_case 例外
```

**BARR-C（模块前缀法）：**
```c
void     uart_init(const uart_config_t *config);   // 模块前缀 + snake_case
uint16_t adc_read(uint8_t channel);                // 模块前缀 + snake_case
typedef struct { ... } uart_config_t;              // typedef：模块_xxx_t
#define MAX_BUFFER_SIZE 256                        // 宏：全大写
bool is_ready;                                     // 布尔：is_ 前缀
```

---

## 二、代码格式对比

| 元素 | Linux Kernel | PX4 | ArduPilot | Google C++ |
|------|-------------|-----|-----------|------------|
| 缩进 | Tab = 8 空格 | Tab = 8 空格 | 4 空格 | 2 空格 |
| 行宽 | 80 列 | 140 列 | 无硬性限制 | 80 列 |
| 括号风格 | K&R（函数 `{` 换行） | Linux/K&R | 函数 `{` 换行，其他同行 | 全部同行 |
| 单行 if 加括号 | 不强制 | 不强制 | 强制 | 不强制 |
| 指针对齐 | `char *p`（靠变量） | `char *p`（靠变量） | 未明确 | 未强制 |
| 格式化工具 | checkpatch.pl | astyle + clang-tidy | astyle | clang-format |

### 2.1 括号风格示例

**K&R 风格（Linux Kernel / PX4）：**
```c
// 函数：左花括号换行
int function(int x)
{
        body;
}

// 控制语句：左花括号同行
if (x == y) {
        do_something();
} else {
        do_other();
}
```

**Google 风格：**
```cpp
// 函数和控制语句都同行
int Function(int x) {
  body;
}

if (x == y) {
  DoSomething();
} else {
  DoOther();
}
```

**ArduPilot 风格：**
```cpp
// 函数：换行
int function(int x)
{
    body;
}

// 控制语句：同行，必须加括号
if (x == y) {
    do_something();
} else {
    do_other();
}
```

---

## 三、接口设计规范

### 3.1 函数返回值约定

| 项目 | 命令型函数 | 谓词型函数 | 错误处理 |
|------|-----------|-----------|---------|
| Linux Kernel | 返回 `0` 成功，`-Exxx` 失败 | 返回 `1` 真，`0` 假 | goto 集中退出 |
| PX4 | 返回 `0` 或 `PX4_OK` | 返回 `bool` | 错误码 + 日志 |
| ArduPilot | 返回 `bool` 或错误码 | 返回 `bool` | 返回值检查 |
| Google C++ | 优先返回值而非输出参数 | 返回 `bool` | `absl::Status` |

### 3.2 函数参数规范

**Google C++：**
- 输入参数在前，输出参数在后
- 非可选输入：值传递或 `const` 引用
- 非可选输出：引用（不能为 null）
- 可选输入：`std::optional` 或 `const` 指针
- 可选输出：非 `const` 指针

**BARR-C 嵌入式：**
- 配置结构体替代长参数列表
- 不透明指针隐藏实现细节
- 生命周期函数成对出现：`_init/_deinit` 或 `_create/_destroy`

```c
// 推荐：配置结构体
uart_config_t cfg = {
    .port = 1,
    .baud = 115200,
    .parity = UART_PARITY_NONE,
};
uart_init(&cfg);

// 不推荐：长参数列表
uart_init(1, 115200, UART_PARITY_NONE, UART_STOP_1, UART_FLOW_NONE);
```

### 3.3 模块接口模式

**BARR-C 模块前缀法（嵌入式标准做法）：**
```c
// adc.h — 每个模块分配唯一前缀
void     adc_init(void);
uint16_t adc_read(uint8_t channel);
void     adc_calibrate(void);

// uart.h
void uart_init(const uart_config_t *config);
int  uart_send(const uint8_t *data, size_t len);
int  uart_recv(uint8_t *data, size_t len);
```

**ArduPilot 前端/后端架构（传感器驱动）：**
```cpp
// 前端（公共 API）
class AP_Baro { ... };

// 后端（硬件特定实现）
class AP_Baro_MS5611 : public AP_Baro_Backend { ... };
class AP_Baro_BMP280 : public AP_Baro_Backend { ... };
```

**PX4 uORB 消息通信：**
```cpp
// 模块间通过发布/订阅解耦
uORB::Publication<vehicle_attitude_s> _attitude_pub{ORB_ID(vehicle_attitude)};
uORB::Subscription _sensor_sub{ORB_ID(sensor_combined)};
```

### 3.4 HAL 抽象层接口（ArduPilot）

```cpp
// 回调/函数指针模式
typedef struct {
    void (*init)(void);
    int  (*read)(uint8_t *buf, size_t len);
    int  (*write)(const uint8_t *buf, size_t len);
} hal_uart_ops_t;

// HAL 聚合对象
AP_HAL::HAL
├── UARTDriver      — 串口
├── SPIDeviceManager — SPI
├── I2CDeviceManager — I2C
├── GPIO            — 通用 IO
├── Scheduler       — 任务调度
├── AnalogIn        — ADC
├── Storage         — 持久化
└── RCInput/RCOutput — 遥控
```

---

## 四、Python 命名规范（Google Style Guide）

### 4.1 命名总表

| 元素 | Public | Internal |
|------|--------|----------|
| 包/模块 | `snake_case` | `_snake_case` |
| 类 | `PascalCase` | `_PascalCase` |
| 异常 | `PascalCase` | — |
| 函数/方法 | `snake_case()` | `_snake_case()` |
| 全局/类常量 | `UPPER_SNAKE_CASE` | `_UPPER_SNAKE_CASE` |
| 实例变量 | `snake_case` | `_snake_case` |
| 局部变量 | `snake_case` | — |
| 文件名 | `snake_case.py` | — |

### 4.2 类型注解

```python
# 函数签名必须标注类型
def fetch_rows(
    table_handle: smalltable.Table,
    keys: Sequence[bytes | str],
    require_all_keys: bool = False,
) -> Mapping[bytes, tuple[str, ...]]:

# 使用 X | None 而非隐式 Optional
def func(a: str | None = None) -> str:
    ...

# 容器类型优先用抽象类型
from collections.abc import Sequence, Mapping
```

### 4.3 Docstring 格式（Google 风格）

```python
def fetch_rows(table_handle, keys, require_all_keys=False):
    """Fetches rows from a Smalltable.

    Retrieves rows pertaining to the given keys.

    Args:
        table_handle: An open smalltable.Table instance.
        keys: A sequence of strings representing the key
          of each table row to fetch.
        require_all_keys: If True only rows with values set
          for all keys will be returned.

    Returns:
        A dict mapping keys to the corresponding table row data.

    Raises:
        IOError: An error occurred accessing the smalltable.
    """
```

### 4.4 函数设计

- 不用可变对象作默认值（`def foo(a, b=[])` 禁止）
- 超过约 40 行考虑拆分
- 优先返回值而非修改参数

---

## 五、飞控/嵌入式领域特殊规则

### 5.1 物理量单位标注（PX4 + ArduPilot 共识）

**变量名后缀法（ArduPilot）：**

| 物理量 | 单位 | 后缀 | 示例 |
|--------|------|------|------|
| 距离 | 米 / 厘米 | `_m` / `_cm` | `altitude_m`, `distance_cm` |
| 角度 | 弧度 / 度 / 厘度 | `_rad` / `_deg` / `_cd` | `roll_rad`, `heading_deg` |
| 角速度 | rad/s / deg/s | `_rads` / `_degs` | `yaw_rate_rads` |
| 时间 | 秒 / 毫秒 / 微秒 | `_s` / `_ms` / `_us` | `timeout_ms` |
| 速度 | m/s | `_mps` | `airspeed_mps` |
| 加速度 | m/s² | `_mss` | `accel_mss` |
| 温度 | °C | `_degc` | `board_temp_degc` |
| 电压/电流 | V / A | `_v` / `_amp` | `battery_v` |
| 频率 | Hz | `_hz` | `sample_rate_hz` |

**注释标注法（PX4）：**
```cpp
uint64 timestamp        # [us] Time since system start
float32 horizontal_velocity  # [m/s]
float32 yaw_rate        # [rad/s]
```

### 5.2 嵌入式安全约束（MISRA C + ArduPilot）

| 规则 | 来源 | 说明 |
|------|------|------|
| 固定宽度整型 | MISRA + BARR-C | 必须用 `uint8_t/int16_t` 等，禁止 `int/short/long` |
| 禁止动态内存 | MISRA 21.3 | 禁止 `malloc/free`，使用静态分配 |
| 禁止递归 | MISRA 17.2 | 保证栈使用可预测 |
| 禁止变量遮蔽 | MISRA 5.3 | 内层作用域不得重名外层变量 |
| 禁止隐式窄化 | MISRA 10.3 | 必须显式类型转换 |
| 禁止 std:: | ArduPilot | 不用 `std::vector/string/map`，用固定数组替代 |
| 乘法优于除法 | ArduPilot | `foo_cm * 0.01` 而非 `foo_cm / 100` |
| 成员变量头文件初始化 | ArduPilot | 在声明处初始化，不在构造函数 |
| 禁止死代码 | ArduPilot | 未使用代码直接删除，不注释掉 |

### 5.3 PX4 参数系统规范

```cpp
/**
 * Pitch P gain
 *
 * @unit 1/s
 * @min 0.0
 * @max 10
 * @decimal 2
 * @increment 0.0005
 * @reboot_required true
 * @group Multicopter Attitude Control
 */
PARAM_DEFINE_FLOAT(MC_PITCH_P, 6.5f);
```

参数命名规则：
- 最长 16 字符
- 全大写 + 下划线
- 前缀表示子系统：`MC_`（多旋翼）、`FW_`（固定翼）、`RTL_`（返航）
- 按重要性从左到右排列：`RTL_ALT_MIN`（正确）vs `RTL_MIN_ALT`（错误）

---

## 六、头文件规范对比

### 6.1 Include Guard

**Linux Kernel / BARR-C：**
```c
#ifndef UART_H
#define UART_H
// ...
#endif /* UART_H */
```

**Google C++（路径保证唯一）：**
```cpp
#ifndef FOO_BAR_BAZ_H_
#define FOO_BAR_BAZ_H_
// ...
#endif  // FOO_BAR_BAZ_H_
```

### 6.2 Include 顺序（Google C++）

```cpp
// 1. 关联头文件
#include "foo/server/fooserver.h"

// 2. C 系统头文件
#include <sys/types.h>
#include <unistd.h>

// 3. C++ 标准库
#include <string>
#include <vector>

// 4. 其他库
#include "base/basictypes.h"

// 5. 本项目
#include "foo/server/bar.h"
```

---

## 七、注释风格对比

| 项目 | 风格 | 核心原则 |
|------|------|---------|
| Linux Kernel | `/* */` 多行注释 | 说明 WHAT 不说明 HOW；不过度注释 |
| PX4 | Doxygen `/** */` | `@brief/@param/@return`；必须标注单位 |
| ArduPilot | 未严格规定 | 物理量必须标注单位 |
| Google C++ | `//` 行注释为主 | 解释 WHY 不解释 WHAT |
| BARR-C | `/* */`（C89 兼容） | 每个公开函数必须有注释 |

---

## 八、Commit 规范

PX4 和多数现代项目使用 **Conventional Commits**：

```
type(scope): short description

feat(ekf2): add magnetometer bias estimation
fix(drivers/imu): correct gyro temperature compensation
docs(contributing): update coding style section
```

类型：`feat` / `fix` / `docs` / `style` / `refactor` / `perf` / `test` / `build` / `ci` / `chore` / `revert`

---

## 九、共性总结与建议

### 所有项目的共识

1. **snake_case 是 C 和嵌入式的事实标准**（变量、函数）
2. **PascalCase 是 C++ 类名的事实标准**
3. **UPPER_SNAKE_CASE 是宏和常量的事实标准**
4. **物理量必须标注单位**（飞控领域强制）
5. **固定宽度整型**（嵌入式强制）
6. **禁止魔法数字**
7. **Conventional Commits** 提交规范

### 飞控项目的额外共识

1. 模块前缀命名（`AP_`、`MC_`、`FW_`）
2. 前端/后端驱动架构
3. HAL 硬件抽象层
4. 消息/事件驱动的模块间通信
5. 参数系统带元数据（单位、范围、描述）
6. 谨慎使用动态内存
