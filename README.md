To spawn bot in gazebo - 
ros2 launch complete_bot launch_sim.launch.py 

to launch gazebo diff_drive for keyboard teleop - 
ros2 run teleop_twist_keyboard teleop_twist_keyboard

at this stage, the bot can be visualised in rviz2. Enabling the odom frame and TF plugin will also give us both frames and the movement of the bot as we control it using keyboard.

to launch the robot in our custom gazebo world - 
ros2 launch complete_bot launch_sim.launch.py world:=./src/complete_bot/worlds/obstacles.world

sensors like LiDAR, camera and depth camera have been configured at above stage and they can be viewed in rviz2 using the different plugins.

after congfiguring ros2_control we can launch the gazebo sim using the previously used command without any changes but for teleop use the command stated below because of the difference of topics of publishing velocity commands - 
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/diff_cont/cmd_vel_unstamped

SLAM with slam_toolbox - 
ros2 launch slam_toolbox online_async_launch.py slam_params_file:=./src/complete_bot/config/mapper_params_online_async.yaml use_sim_time:=true

after running this , create a map by moving around in the environment.
save map files both save and serialize.
then close rviz and slam toolbox and change mapping to localization in the mapper_params_online_async.yaml file and set the path to saved map

re run both slam toolbox and rviz2 and set fixed frame to map and subscribe to /map topic and also change durability policy under topic to transient local

this brings up our loaded map and we can proceed to localization   

twist mux and autonomous navigation using nav2 -  
launch gazebo and rviz2 normally 
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/cmd_vel_joy

ros2 run twist_mux twist_mux --ros-args --params-file ./src/complete_bot/config/twist_mux.yaml -r cmd_vel_out:=diff_cont/cmd_vel_unstamped

ros2 launch nav2_bringup navigation_launch.py use_sim_time:=true

ros2 launch slam_toolbox online_async_launch.py slam_params_file:=./src/complete_bot/config/mapper_params_online_async.yaml use_sim_time:=true

and add map plugins, add /global_costmap as topic and set to transient local in durability and then give 2d goal pose, the bot will autonomously navigate to the given point.