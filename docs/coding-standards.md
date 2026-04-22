# NexusAir-Technologies 编码规范

> 版本：1.0 | 生效日期：2026-04-22
> 基于 PX4 Coding Style，Google C++ Style Guide 补充，飞控安全规则强化

---

## 一、C/C++ 命名规范

### 1.1 命名总表

| 元素 | 命名法 | 示例 |
|------|--------|------|
| 类 | `UpperCamelCase` | `MotorController`, `AttitudeEstimator` |
| 结构体 | `UpperCamelCase` | `SensorData`, `MotorConfig` |
| 函数/方法 | `lowerCamelCase` | `computeThrust()`, `getAltitude()` |
| 局部变量 | `snake_case` | `throttle_input`, `roll_angle_rad` |
| 函数参数 | `snake_case` | `target_altitude_m` |
| 私有类成员 | `_snake_case` | `_motor_speed_rads`, `_is_armed` |
| 公有类成员 | `snake_case` | `timestamp_us` |
| 结构体成员 | `snake_case` | `baud_rate`, `port_num` |
| 静态/全局常量 | `kCamelCase` | `kMaxThrust`, `kDefaultTimeout` |
| 枚举类型 | `UpperCamelCase` | `enum class FlightMode` |
| 枚举值 | `UPPER_SNAKE_CASE` | `FLIGHT_MODE_MANUAL`, `FLIGHT_MODE_AUTO` |
| 宏 | `UPPER_SNAKE_CASE` | `MAX_MOTORS`, `BOARD_REV` |
| 命名空间 | `snake_case` | `nexusair`, `motor_control` |
| typedef/using | `UpperCamelCase` | `using MotorArray = std::array<Motor, 8>` |
| 模板类型参数 | `UpperCamelCase` | `template <typename ValueType>` |
| 文件名 | `snake_case` | `motor_controller.cpp`, `attitude_estimator.h` |

### 1.2 命名示例

```cpp
namespace nexusair {

class AttitudeEstimator {
public:
        /**
         * @brief Update attitude estimate from sensor data.
         * @param[in] gyro_rads Gyroscope reading [rad/s]
         * @param[in] accel_mss Accelerometer reading [m/s²]
         * @param[in] dt_s Time delta [s]
         */
        void updateEstimate(const Vector3f &gyro_rads,
                            const Vector3f &accel_mss,
                            float dt_s);

        float getRoll_rad() const { return _roll_rad; }
        float getPitch_rad() const { return _pitch_rad; }
        float getYaw_rad() const { return _yaw_rad; }

        bool isInitialized() const { return _is_initialized; }

private:
        static constexpr float kConvergenceThreshold = 0.01f;
        static constexpr float kMaxGyroRate_rads = 10.0f;
        static constexpr uint32_t kInitTimeout_ms = 5000;

        float _roll_rad{0.0f};
        float _pitch_rad{0.0f};
        float _yaw_rad{0.0f};
        bool  _is_initialized{false};

        uint64_t _last_update_us{0};
};

} // namespace nexusair
```

### 1.3 物理量单位后缀（强制）

所有表示物理量的变量、函数返回值、参数必须带单位后缀：

| 物理量 | 单位 | 后缀 | 示例 |
|--------|------|------|------|
| 距离 | 米 | `_m` | `altitude_m`, `distance_m` |
| 距离 | 厘米 | `_cm` | `obstacle_distance_cm` |
| 角度 | 弧度 | `_rad` | `roll_rad`, `pitch_rad` |
| 角度 | 度 | `_deg` | `heading_deg` |
| 角度 | 厘度 | `_cd` | `target_bearing_cd` |
| 角速度 | rad/s | `_rads` | `yaw_rate_rads` |
| 角速度 | deg/s | `_degs` | `gyro_x_degs` |
| 时间 | 秒 | `_s` | `flight_time_s` |
| 时间 | 毫秒 | `_ms` | `timeout_ms` |
| 时间 | 微秒 | `_us` | `timestamp_us` |
| 速度 | m/s | `_mps` | `airspeed_mps` |
| 加速度 | m/s² | `_mss` | `accel_z_mss` |
| 温度 | °C | `_degc` | `imu_temp_degc` |
| 电压 | V | `_v` | `battery_v` |
| 电流 | A | `_a` | `motor_current_a` |
| 频率 | Hz | `_hz` | `sample_rate_hz` |
| 功率 | W | `_w` | `power_output_w` |

无量纲值（归一化值、计数、索引、布尔）不加后缀。

### 1.4 布尔变量命名

布尔变量和返回布尔的函数使用 `is_`/`has_`/`can_`/`should_` 前缀：

```cpp
bool _is_armed;
bool _has_gps_fix;
bool canTakeoff() const;
bool shouldAbort() const;
```

### 1.5 禁止事项

- 禁止匈牙利命名法（`iCount`, `strName`）
- 禁止单字母变量名（`i/j/k` 循环计数器除外）
- 禁止魔法数字，必须定义为命名常量
- 禁止 `master/slave`，使用 `primary/secondary` 或 `controller/device`
- 禁止 `blacklist/whitelist`，使用 `denylist/allowlist`

---

## 二、整型规范（强制）

嵌入式环境必须使用固定宽度整型（`<cstdint>`）：

```cpp
// 正确
uint8_t  register_value;
int16_t  temperature_raw;
uint32_t timestamp_us;
int32_t  position_cm;
uint64_t total_flight_time_us;
float    altitude_m;

// 禁止
int      count;        // 平台相关宽度
short    value;        // 平台相关宽度
long     timestamp;    // 平台相关宽度
unsigned size;         // 平台相关宽度
```

例外：`bool`、`float`、`double`、`size_t`（标准库接口）可以使用。

---

## 三、代码格式

### 3.1 格式规则

| 规则 | 值 |
|------|-----|
| 缩进 | Tab = 8 空格（与 PX4 一致） |
| 对齐 | 空格 |
| 行宽 | 140 字符 |
| 括号风格 | Linux/K&R |
| 指针/引用对齐 | 靠变量名（`float *ptr`） |
| 单行 if | 允许，但多分支时全部加括号 |

### 3.2 括号风格

```cpp
// 函数：左花括号可同行（PX4 astyle 默认）
float computeThrust(float throttle_input) {
        return throttle_input * kMaxThrust;
}

// 控制语句：左花括号同行
if (fabsf(yaw_input) < kDeadzone) {
        yaw_rate = 0.0f;
} else {
        yaw_rate = kMaxYawRate * yaw_input;
}

// if/else 有一个分支多语句时，两个分支都加括号
if (is_armed) {
        startMotors();
        logEvent("armed");
} else {
        stopMotors();
}

// 类定义
class MotorController {
public:
        void init();

private:
        float _thrust_n;
};
```

### 3.3 空格规则

```cpp
if (condition)                    // 关键字后加空格
sizeof(struct MotorData)          // sizeof 后不加空格
float *ptr;                       // * 靠变量名
a = b + c;                        // 二元运算符两侧加空格
i++;                              // 一元运算符不加空格
obj.member                        // . 和 -> 不加空格
```

### 3.4 格式化工具

使用 PX4 的 astyle 配置：

```
indent=force-tab=8
style=linux
indent-cases
pad-oper
pad-header
unpad-paren
align-pointer=name
align-reference=name
max-code-length=140
add-brackets
```

---

## 四、函数设计

### 4.1 基本规则

- 函数只做一件事，适合 1-2 屏显示
- 局部变量不超过 5-10 个
- 参数超过 4 个时使用配置结构体

```cpp
// 推荐：配置结构体
struct MotorConfig {
        uint8_t  motor_id;
        float    max_thrust_n;
        float    idle_throttle;
        uint16_t pwm_freq_hz;
};
void initMotor(const MotorConfig &config);

// 不推荐：长参数列表
void initMotor(uint8_t id, float max_thrust, float idle, uint16_t freq);
```

### 4.2 参数顺序（Google 规则）

输入参数在前，输出参数在后：

```cpp
// 正确
bool computeTrajectory(const Waypoint &start, const Waypoint &end,
                       Trajectory *result);

// 错误
bool computeTrajectory(Trajectory *result, const Waypoint &start,
                       const Waypoint &end);
```

### 4.3 返回值约定

| 函数类型 | 返回值 | 示例 |
|----------|--------|------|
| 命令/动作 | `0` 成功，负值失败 | `int arm()` → `0` or `-1` |
| 谓词/判断 | `bool` | `bool isArmed()` → `true/false` |
| 计算/获取 | 计算结果 | `float getAltitude_m()` |
| 可能失败的获取 | `bool` + 输出参数 | `bool getPosition(Vector3f *pos)` |

### 4.4 资源管理

多资源场景使用 goto 集中退出（C 代码）或 RAII（C++ 代码）：

```c
// C 风格：goto 集中退出
int initSubsystem(void)
{
        int ret = 0;
        uint8_t *buf = malloc(BUF_SIZE);
        if (!buf)
                return -ENOMEM;

        ret = configureHardware();
        if (ret < 0)
                goto err_free_buf;

        ret = startDma();
        if (ret < 0)
                goto err_free_buf;

        return 0;

err_free_buf:
        free(buf);
        return ret;
}
```

---

## 五、头文件规范

### 5.1 Include Guard

使用路径式 guard 保证唯一性：

```cpp
// 文件：src/drivers/motor/motor_controller.h
#ifndef NEXUSAIR_DRIVERS_MOTOR_MOTOR_CONTROLLER_H_
#define NEXUSAIR_DRIVERS_MOTOR_MOTOR_CONTROLLER_H_

// ...

#endif  // NEXUSAIR_DRIVERS_MOTOR_MOTOR_CONTROLLER_H_
```

### 5.2 Include 顺序

```cpp
// 1. 关联头文件
#include "motor_controller.h"

// 2. C 系统头文件
#include <stdint.h>
#include <string.h>

// 3. C++ 标准库
#include <algorithm>
#include <array>

// 4. 第三方库 / 框架
#include <uORB/uORB.h>
#include <drivers/drv_hrt.h>

// 5. 本项目头文件
#include "motor_config.h"
#include "thrust_model.h"
```

每组之间空一行，组内按字母排序。

---

## 六、注释规范

### 6.1 核心原则

- 注释说明 WHY，不说明 WHAT（代码本身说明 WHAT）
- 不过度注释，命名应自解释
- 物理量必须在声明处标注单位

### 6.2 Doxygen 文档注释

公开 API 使用 Doxygen 格式：

```cpp
/**
 * @brief Compute motor thrust from throttle input.
 *
 * @param[in] throttle_input Normalized throttle [0.0, 1.0]
 * @return Thrust in Newtons [N]
 */
float computeThrust(float throttle_input) const;
```

### 6.3 行内注释

```cpp
static constexpr float kYawDeadzone = 0.1f;  // normalized stick input threshold
float _battery_v;                             // [V] current battery voltage
```

### 6.4 TODO 格式

```cpp
// TODO(username): description of what needs to be done
// TODO(hhhhxl): add temperature compensation for IMU bias
```

---

## 七、Python 规范

遵循 Google Python Style Guide，核心规则：

### 7.1 命名

| 元素 | 命名法 | 示例 |
|------|--------|------|
| 模块/包 | `snake_case` | `motor_test.py` |
| 类 | `PascalCase` | `FlightController` |
| 函数/方法 | `snake_case` | `compute_thrust()` |
| 常量 | `UPPER_SNAKE_CASE` | `MAX_ALTITUDE_M` |
| 变量 | `snake_case` | `current_speed_mps` |
| 私有 | `_前缀` | `_internal_state` |

### 7.2 类型注解（强制）

公开 API 必须标注类型：

```python
def compute_trajectory(
    start: Waypoint,
    end: Waypoint,
    max_speed_mps: float = 5.0,
) -> Trajectory | None:
    """Compute flight trajectory between two waypoints.

    Args:
        start: Starting waypoint.
        end: Destination waypoint.
        max_speed_mps: Maximum speed in m/s.

    Returns:
        Computed trajectory, or None if path is infeasible.

    Raises:
        ValueError: If start equals end.
    """
```

### 7.3 Docstring

使用 Google 风格（`Args:` / `Returns:` / `Raises:`），见上例。

---

## 八、嵌入式安全规则

以下规则在固件代码中强制执行：

| 规则 | 说明 |
|------|------|
| 固定宽度整型 | 必须用 `uint8_t/int16_t` 等 |
| 禁止魔法数字 | 所有常量必须命名 |
| 物理量单位后缀 | 变量名必须带单位后缀 |
| 谨慎动态内存 | 飞控核心路径禁止 `new/malloc`，初始化阶段可用 |
| 禁止递归 | 保证栈使用可预测 |
| 禁止变量遮蔽 | 内层作用域不得重名外层变量 |
| 显式类型转换 | 禁止隐式窄化 |
| 检查返回值 | 所有可能失败的函数调用必须检查返回值 |
| 禁止死代码 | 未使用代码直接删除，不注释掉 |
| 乘法优于除法 | `x * 0.01f` 而非 `x / 100.0f` |

---

## 九、Git 提交规范

使用 Conventional Commits + 签名：

```
type(scope): short description

feat(motor): add thrust curve compensation
fix(ekf): correct magnetometer bias estimation
docs(readme): update build instructions
refactor(hal): simplify SPI driver interface
test(gps): add NMEA parser unit tests
```

### 类型

| 类型 | 用途 |
|------|------|
| `feat` | 新功能 |
| `fix` | Bug 修复 |
| `docs` | 文档 |
| `style` | 格式（不影响逻辑） |
| `refactor` | 重构 |
| `perf` | 性能优化 |
| `test` | 测试 |
| `build` | 构建系统 |
| `ci` | CI/CD |
| `chore` | 杂项 |

### 分支命名

```
feat/motor-thrust-curve
fix/ekf-mag-bias
docs/api-reference
chore/update-toolchain
```

---

## 十、CI 强制检查项

| 检查 | 工具 | 触发条件 |
|------|------|---------|
| C/C++ 格式 | `clang-format` | PR 包含 `.c/.cpp/.h/.hpp` |
| C/C++ 命名规范 | `clang-tidy` | PR 包含 `.c/.cpp/.h/.hpp` |
| C/C++ 静态分析 | `cppcheck` | PR 包含 `.c/.cpp/.h/.hpp` |
| Python lint | `ruff check` | PR 包含 `.py` |
| Python 格式 | `ruff format --check` | PR 包含 `.py` |
| Commit 格式 | `commitlint` | 所有 PR |
