---
layout: post
title: gripper｜解决右夹爪姿态切换时脱离和挤压的问题
description: 记录右夹爪加快轨迹后姿态切换异常的排查过程，以及舵机速度和加速度参数不一致的问题
categories: [问题记录,二指夹爪]
keywords: 二指夹爪,姿态切换,舵机,加速度,ROS2,fabricunfolding
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

### 遇到问题描述

在布料分层实验中，二指夹爪需要在“边缘跟踪姿态”和“分层姿态”之间切换。理想情况下，其中一根手指应贴着另一根手指旋转，切换过程中两根手指始终保持接触。

为了缩短姿态切换时间，我将直线轨迹的规划时间改为：

```cpp
double duration = deg_max / 288.0;
```

同时将旋转轨迹的等待时间改为：

```cpp
usleep(1000 * 10);  // 10 ms，约100 Hz
```

加快以后，左夹爪工作正常，但是右夹爪出现以下现象：

- 从边缘跟踪姿态切换到分层姿态时，旋转手指会向外移动，与另一根手指脱离接触；
- 从分层姿态切换回边缘跟踪姿态时，旋转手指会向内移动，挤压另一根手指。

右夹爪使用的切换流程为：

```cpp
// 切换到分层姿态
finger.Path_line_2(LEFT, 1, 1, 1);
finger.Path_line_1(DOWN, L_, 20, finger.Axis_1);
finger.Path_trans(ACW, L_, finger.Axis_2);

// 切换到边缘跟踪姿态
finger.Path_trans(CW, L_, finger.Axis_2);
finger.Path_line_1(UP, L_, 20, finger.Axis_1);
finger.Path_line_2(RIGHT, 1, 1, 1);
```

### 第一次排查：旋转中心参数

一开始怀疑右夹爪的装配误差导致理论旋转中心和实际接触点不一致。

`pose_trans.cpp` 中的两个参数共同定义了 `Finger_L` 的 `f2` 接触点，也就是 `Path_trans(..., L_, Axis_2)` 使用的理论旋转中心：

```cpp
double distance_f0f2_x_L = 58.0;
double distance_f0f2_y_L = 11.95;
```

其中：

- `f0` 是手指运动学末端坐标系；
- `f2` 是 `Axis_2` 对应的接触点坐标系；
- `distance_f0f2_x_L` 和 `distance_f0f2_y_L` 是 `f2` 相对 `f0` 的局部坐标偏移。

为了避免修改右夹爪参数时影响正常工作的左夹爪，我新增了右夹爪专用的：

```text
pose_trans_right.h
pose_trans_right.cpp
```

左夹爪继续使用 `pose_trans.cpp`，右夹爪的 `path_plan_right.cpp` 单独链接 `pose_trans_right.cpp`。

随后分别将右夹爪的 `distance_f0f2_x_L` 增大和减小约10 mm进行测试，但异常现象基本没有变化。这说明问题并不是简单的静态旋转中心偏差。

这里还需要注意夹爪运动学中的坐标定义：

- X方向控制两根手指夹紧和张开；
- Y方向控制手指伸出和缩回。

因此，不能用Y方向参数直接修正夹紧和张开问题。最终将旋转中心参数恢复为原始值：

```cpp
constexpr double kDistanceF0F2XL = 58.0;
constexpr double kDistanceF0F2YL = 11.95;
```

### 第二次排查：左右夹爪的舵机参数

由于左夹爪在相同轨迹规划速度下工作正常，继续对比左右两套 `path_plan` 的舵机参数以及 `Cmd_publish()` 实际发送的变量。

左夹爪定义了：

```cpp
u16 Speed_[3] = {150, 150, 150};
u16 Speed_2[3] = {400, 400, 400};
u8 ACC_[3] = {200, 200, 200};
```

左夹爪的 `Cmd_publish()` 实际使用的是 `Speed_2`：

```cpp
sms_.SyncWritePosEx(ID_L_, IDN_, Step_temp, Speed_2, ACC_);
```

因此左夹爪实际发送的参数为：

```text
速度：400
加速度：200
```

右夹爪原来的参数为：

```cpp
u16 Speed_[3] = {150, 150, 150};
u8 ACC_[3] = {0, 0, 0};
```

右夹爪的 `Cmd_publish()` 使用的是 `Speed_`：

```cpp
sms_.SyncWritePosEx(ID_L_, IDN_, Step_temp, Speed_, ACC_);
```

因此右夹爪实际发送的参数为：

```text
速度：150
加速度：0
```

虽然左右夹爪使用相同的轨迹插值频率，但舵机的实际速度和加速度完全不同。轨迹加快后，右夹爪无法及时跟随连续发送的目标点，实际位置落后于程序中的规划位置，于是表现为正向旋转时脱离、反向旋转时挤压。

### 解决方法

将右夹爪的速度、加速度以及对应上限改为与左夹爪一致：

```cpp
const u16 Speed_LimitHig_[3] = {400, 400, 400};
const u8 AcCC_LimitHig_[3] = {200, 200, 200};

u16 Speed_[3] = {400, 400, 400};
u8 ACC_[3] = {200, 200, 200};
```

重新编译并进行实机测试后，右夹爪能够正常完成两种姿态之间的切换。最终确认问题的根因是右夹爪的加速度参数为0，而不是旋转中心参数错误。

### ROS 2重新编译后仍运行旧代码的问题

排查过程中还遇到过修改源码并重新编译、执行 `source` 后仍像是在运行旧代码的情况。

`source install/setup.bash` 只负责更新当前终端的环境变量，不会重新编译，也不会主动清理旧的CMake缓存和安装文件。修改了新源文件、头文件或者 `CMakeLists.txt` 后，可以使用：

```bash
cd /home/kuka-iiwa/ws_zyl
source /opt/ros/humble/setup.bash
colcon build --packages-select servo_test --cmake-clean-cache
source install/setup.bash
ros2 pkg prefix servo_test
```

`ros2 pkg prefix servo_test` 应输出：

```text
/home/kuka-iiwa/ws_zyl/install/servo_test
```

如果仍然运行旧版本，可以在确认旧节点已经关闭后，清理该包的构建和安装目录再重新编译：

```bash
rm -rf build/servo_test install/servo_test
colcon build --packages-select servo_test
source install/setup.bash
```

### 总结

这次问题表面上很像旋转中心标定错误，但改变旋转中心参数后现象没有明显变化。由于左夹爪在相同轨迹速度下正常工作，最终通过对比 `Cmd_publish()` 真正使用的速度和加速度变量，发现左右夹爪的舵机参数并不一致。

在排查轨迹跟随异常时，应同时检查：

1. 轨迹规划周期和插值时间；
2. 舵机命令中的速度、加速度；
3. 程序记录的规划位置与舵机实际位置是否存在滞后；
4. 左右设备是否真正使用了相同的控制参数；
5. 最后再调整旋转中心等几何标定参数。
