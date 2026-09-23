## ДО
#### `export ROS_DOMAIN_ID=90`
#### `ros2 node list --no-daemon --spin-time 2`
```bash
/teleop_turtle
/turtlesim
```
#### `ros2 topic list -t`
```bash
/parameter_events [rcl_interfaces/msg/ParameterEvent]
/rosout [rcl_interfaces/msg/Log]
/turtle1/cmd_vel [geometry_msgs/msg/Twist]
/turtle1/color_sensor [turtlesim/msg/Color]
/turtle1/pose [turtlesim/msg/Pose]
```
#### `ros2 node info /turtlesim`
```bash
/turtlesim
  Subscribers:
    /parameter_events: rcl_interfaces/msg/ParameterEvent
    /turtle1/cmd_vel: geometry_msgs/msg/Twist
  Publishers:
    /parameter_events: rcl_interfaces/msg/ParameterEvent
    /rosout: rcl_interfaces/msg/Log
    /turtle1/color_sensor: turtlesim/msg/Color
    /turtle1/pose: turtlesim/msg/Pose
  Service Servers:
    /clear: std_srvs/srv/Empty
    /kill: turtlesim/srv/Kill
    /reset: std_srvs/srv/Empty
    /spawn: turtlesim/srv/Spawn
    /turtle1/set_pen: turtlesim/srv/SetPen
    /turtle1/teleport_absolute: turtlesim/srv/TeleportAbsolute
    /turtle1/teleport_relative: turtlesim/srv/TeleportRelative
    /turtlesim/describe_parameters: rcl_interfaces/srv/DescribeParameters
    /turtlesim/get_parameter_types: rcl_interfaces/srv/GetParameterTypes
    /turtlesim/get_parameters: rcl_interfaces/srv/GetParameters
    /turtlesim/get_type_description: type_description_interfaces/srv/GetTypeDescription
    /turtlesim/list_parameters: rcl_interfaces/srv/ListParameters
    /turtlesim/set_parameters: rcl_interfaces/srv/SetParameters
    /turtlesim/set_parameters_atomically: rcl_interfaces/srv/SetParametersAtomically
  Service Clients:

  Action Servers:
    /turtle1/rotate_absolute: turtlesim/action/RotateAbsolute
  Action Clients:
```
#### `ros2 topic type /turtle1/pose`
```bash
turtlesim/msg/Pose
```
#### `ros2 topic echo /turtle1/pose --once`
```bash
x: 6.664483070373535
y: 5.75913143157959
theta: 1.3871853351593018
linear_velocity: 0.0
angular_velocity: 0.0
```
#### `ros2 topic hz /turtle1/pose`
```bash
average rate: 62.486
        min: 0.014s max: 0.018s std dev: 0.00053s window: 64
average rate: 62.467
        min: 0.014s max: 0.018s std dev: 0.00051s window: 127
average rate: 62.460
        min: 0.014s max: 0.018s std dev: 0.00051s window: 190
average rate: 62.485
        min: 0.014s max: 0.018s std dev: 0.00052s window: 253
average rate: 62.498
        min: 0.014s max: 0.018s std dev: 0.00052s window: 316
average rate: 62.492
        min: 0.014s max: 0.018s std dev: 0.00052s window: 379
average rate: 62.488
        min: 0.014s max: 0.018s std dev: 0.00054s window: 442
average rate: 62.492
        min: 0.014s max: 0.018s std dev: 0.00056s window: 505
average rate: 62.499
        min: 0.014s max: 0.018s std dev: 0.00057s window: 568
average rate: 62.497
        min: 0.014s max: 0.018s std dev: 0.00056s window: 631
average rate: 62.493
        min: 0.014s max: 0.018s std dev: 0.00056s window: 694
average rate: 62.492
        min: 0.014s max: 0.018s std dev: 0.00056s window: 757
average rate: 62.499
        min: 0.014s max: 0.019s std dev: 0.00058s window: 820
average rate: 62.497
        min: 0.014s max: 0.019s std dev: 0.00057s window: 883
average rate: 62.500
        min: 0.014s max: 0.019s std dev: 0.00057s window: 946
average rate: 62.495
        min: 0.014s max: 0.019s std dev: 0.00056s window: 1009
average rate: 62.500
        min: 0.013s max: 0.019s std dev: 0.00057s window: 1072
average rate: 62.496
        min: 0.013s max: 0.019s std dev: 0.00057s window: 1135
average rate: 62.497
        min: 0.013s max: 0.019s std dev: 0.00057s window: 1198
```

## СБОЙ
#### `export ROS_DOMAIN_ID=10`
#### `ros2 node list --no-daemon --spin-time 2`
```bash
/teleop_turtle
```
#### `timeout 5s ros2 topic echo /turtle1/pose "$POSE_TYPE" --once > evidence/pr01/pose-broken.txt 2>&1`
```bash
Ничего нет в файле 
```

#### `printf 'exit=%s\n' "$?"`
```bash
exit=124
```

## ПОСЛЕ
#### `export ROS_DOMAIN_ID=90`
#### `ros2 node list --no-daemon --spin-time 2`
```bash
/teleop_turtle
/turtlesim
```
#### `timeout 5s ros2 topic echo /turtle1/pose "$POSE_TYPE" --once > evidence/pr01/pose-fixed.txt 2>&1`
```bash
x: 6.2959980964660645
y: 8.016764640808105
theta: -0.43681469559669495
linear_velocity: 0.0
angular_velocity: 0.0
---
```
#### `printf 'exit=%s\n' "$?"`
```bash
exit=0
```

## Объяснение

Сбой произошел из-за того, что я изменила переменную ROS_DOMAIN_ID с 90 на 10. В ROS 2 эта настройка изолирует сетевой трафик, поэтому мой терминал перестал видеть ноды, которые продолжили работать в домене 90. Из-за этого рассинхрона команда ожидания сообщения завершилась по таймауту с кодом 124. Как только я вернула значение 90, видимость и обмен данными восстановились, и команда успешно выполнилась с кодом 0. Для корректной работы все узлы и терминалы должны использовать одинаковый идентификатор домена.