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
3. 下载当前仓库代码
```
git clone git@github.com:WAI-f/ur_robotiq_realsense_moveit_config.git
```
4. 编译代码
```
cd ur_robotiq_realsense_moveit_config
colcon build
source install/setup.bash
```

### 可视化
1. 启动rviz
```
ros2 launch ur_robotiq_realsense_moveit_config demo.launch.py
```
![rviz view](./pic/rviz_view.png)


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
- **start screen**
![import urdf](./pic/import_urdf.png)
    - 点击**Create New Moveit Configuration Package**
    - 点击**Browser**, 选择ur5_robotiq85_rsd455.urdf.xacro所在路径
    - 点击**Load Files**, 正常加载会提示100%

- **self collisions**
![self collision](./pic/self_collision.png)
    - 调整**Sampling Density**滑动条，数值越大采样密度越大，计算越准，但是计算速度也越慢
    - 点击**Generate Collision Matrix**

- **Virtual Joints**
![virtual joint](./pic/virtual_joint.png)
    - 点击**Add Virtual Joint**按钮添加虚拟关节，这里是给world坐标系和机械臂base坐标系之间添加一个虚拟关节

- **Planning Groups**
    - 点击**Add group**添加ur arm group:
    ![plan arm group](./pic/plan_arm_group.png)
    ![plan arm group joint](./pic/plan_arm_group_joint.png)
    - 点击**Add group**添加hand group:
    ![plan hand group](./pic/plan_hand_group.png)
    ![plan hand group joint](./pic/plan_hand_group_joint.png)

- **Robot Poses**
    - 点击**Add Pose**添加home pose:
    ![home pose](./pic/home_pose.png)
    - 点击**Add Pose**添加detect pose:
    ![detect pose](./pic/detect_pose.png)
    - 点击**Add Pose**添加open pose:
    ![plan hand group](./pic/open.png)
    - 点击**Add Pose**添加close pose:
    ![plan hand group joint](./pic/close.png)

- **End Effectors**
    - 点击**Add End Effector**添加hand effector:
    ![end effector](./pic/end_effector.png)

- **Passive Joints**
    - 添加一些不参与规划的joint，当前不设置

- **ros2_control_URDF Modifications**
    - 点击**Add interface**按钮即可:
    ![ros2 control urdf](./pic/ros2_control_urdf_mod.png)

- **ROS2 controllers**
    - 点击**Add controller**按钮添加ur arm controller:
    ![ur arm controller](./pic/arm_controller.png)
        - 再点击**Add Planning Group Joints**, 添加ur arm group：
        ![ur arm controller joint](./pic/arm_controller_joint.png)
    - 点击**Add controller**按钮添加hand controller:
    ![hand controller](./pic/hand_controller.png)
        - 再点击**Add Planning Group Joints**, 添加hand group：
        ![hand controller joint](./pic/hand_controller_joint.png)

- **Moveit controllers**
    - 设置和**ROS2 controllers**完全一致
    - 点击**Add controller**按钮添加ur arm controller:
    ![moveit arm controller](./pic/moveit_arm_controller.png)
        - 再点击**Add Planning Group Joints**, 添加ur arm group：
        ![moveit arm controller joint](./pic/moveit_arm_controller_joint.png)
    - 点击**Add controller**按钮添加hand controller:
    ![moveit hand controller](./pic/moveit_hand_controller.png)
        - 再点击**Add Planning Group Joints**, 添加hand group：
        ![moveit hand controller joint](./pic/moveit_hand_controller_joint.png)

- **Perception**
    - 主要是给相机感知模块做的设置，可以参考我的设置，也可以不设置，当前无影响：
    ![perception](./pic/perception.png)

- **Launch Files**
    - 按照默认设置即可

- **Author Information**
    - 设置个人信息，按照个人需求填写即可

- **Configuration Files**
    - 点击**Browser**按钮，设置moveit_config保存路径
    - 点击**Generate Package**，进度条显示100%即完整整个config的配置
    - 点击**Exit Setup Assistant**退出gui
    ![configuration file](./pic/configuration_files.png)

3. 修改配置文件
- 按照步骤2生成的*moveit_config，在rviz2中做planning的时候会报错：
![error_msg](./pic/error_msg.png)
原因就是在config/joint_limits.yaml中没有设置加速度约束，按照下面的修改即可修复这个问题：
    - ![modify yaml](./pic/modify_yaml.png)

### 说明
1. 查看urdf结构
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

### 参考
- moveit setup assistant官方教程：[MoveIt Setup Assistant](https://moveit.picknik.ai/main/doc/examples/setup_assistant/setup_assistant_tutorial.html)