## UR5+robotiq2f_85+realsense D455 moveit setup

### 环境配置
1. 系统环境配置
- ubuntu24.04

2. 软件环境配置
- ros2 jazzy
- ros-jazzy-xacro
- ros-jazzy-joint-state-publisher-gui
- ros-jazzy-moveit*


### 编译
1. 下载urdf仓库
```
git clone git@github.com:WAI-f/ur_robotiq_realsense_decsription.git
git submodule update --init --recursive
```
2. 编译代码
```
cd ur_robotiq_realsense_description
colcon build
source install/setup.bash
```

### 可视化
1. 激活工作空间的环境变量
```
source install setup.bash
```
2. 可视化urdf
- rviz
```
ros2 launch ur5_robotiq85_rsd455_description display.launch.py
```
- check_urdf
```
# export urdf
xacro ur5_robotiq85_rsd455.urdf.xacro > ur5_robotiq85_rsd455.urdf   

# print link/joint tree
check_urdf ur5_robotiq85_rsd455.urdf                                
```
![link tree](pic/link_tree.png)

### 创建moveit_config package流程
1. 下载[ur_robotiq_realsense_description](https://github.com/WAI-f/ur_robotiq_realsense_description)
- 编译
```
cd ur_robotiq_realsense_description
colcon build
source install/setup.bash
```
moveit/rviz/setup assistant都需要urdf，所以需要在同一个终端内激活ur_robotiq_realsense_description的环境变量

2. 启动moveit setup assistant:
```
ros2 launch moveit_setup_assistant setup_assistant.launch.py
```

### 说明
1. ur直接使用的是Universal_Robots_ROS2_Description仓库源码，分支是jazzy，可以根据使用ros2版本做调整
2. robotiq仓库包含多个ros2 packages，但是这里只将robotiq_description包单独拿出来做编译，这个package包含了robotiq urdf的定义
3. realsense仓库也包含多个ros2 package，这里只用了realsense2_description

### 参考
- moveit setup assistant官方教程：[MoveIt Setup Assistant](https://moveit.picknik.ai/main/doc/examples/setup_assistant/setup_assistant_tutorial.html)