# iitp_simul_personal: iitp 대회 시뮬레이션용 model,launch,world 파일들
## 해당 패키지가 필요
* 다음 swarm, obstacle 패키지는 catkin_ws/src에서 git clone을 한다. 
* px4 패키지의 경우에는 clone한 경로에 맞춰 .bashrc의 해당 부분을 알맞게 수정해야함.

1. 'swarm_ctrl_pkg' pkg : intergratedCoord-start_orientation 브랜치
	```
	$git clone https://github.com/ICSL-hanyang/swarm_ctrl_pkg.git 
	```
2. 'obstacle_detect' pkg: master 브랜치
	```
	$git clone https://github.com/ICSL-hanyang/obstacle_detect 
	```
3. 'px4 Firmware' pkg : Tag v1.8.2 브랜치(동작확인)   v1.9(호환X)
	```
	$git clone https://github.com/PX4/Firmware.git --branch v1.8.2
	```
4. $cm을 한다.(bashrc에서 미리 설정해둠.) 
5. (선택사항) 에러 발생시 , visual studio code의 패키지를 설치하고 다시 $cm을 한다. 
	```
	*C/C++ 
	* C++ Intellisense 
	* Korean Laguage Pack for Visual Studio Code 
	```

## 다음 폴더에 해당 파일을 붙여넣기
1. Firmware/Tools/sitl_gazebo/models(모델은 폴더로 구성됨) 
	```
	iitp_drone 
	cylinderset_iitp 
	end 
	my_ceiling 
	My_Cylinder 
	my_ground_plane 
	my_tree 
	Net_down 
	Net_Top 
	start
	```
2. Firmware/Tools/sitl_gazebo/worlds 
	```
	iitp.world 
	iitp_noceiling.world 
	iitp_noceiling_v1.world 
	iitp_v1.world 
	```
3. Swarm_ctrl_pkg/launch
	```
	iitp_drone.launch 
	iitp_test.launch 
	iitp.launch
	```
4. Swarm_ctrl_pkg/config/posix-configs/SITL/init/ekf2 
	```
	iitp_drone
	```
## bashrc 수정
1. 명령어 편의를 위해 ~/.bashrc 파일을 열어서 파일 끝에 다음 명령어들을 추가한다.
	```
	alias eb='gedit ~/.bashrc'
	alias d1='ssh -X icsl-pi@camila1'
	alias d2='ssh -X icsl-pi@camila2'
	alias d3='ssh -X icsl-pi@camila3'
	alias sb='source ~/.bashrc'
	alias apti='sudo apt install'
	alias gs='git status'
	alias gp='git pull'
	alias cw='cd ~/catkin_ws'
	alias cs='cd ~/catkin_ws/src'
	alias cm='cd ~/catkin_ws && catkin_make'
	alias connmav='rosrun mavros mavros_node _fcu_url:=/dev/ttyUSB0:57600'
	alias qgc='~/Downloads/Utils/QGroundControl.AppImage'
	
	alias mode='rostopic pub /multi/set_mode std_msgs/String '
	alias rtl='rostopic pub /multi/set_mode std_msgs/String "auto.rtl"'
	alias offboard='rostopic pub /multi/set_mode std_msgs/String "offboard"'
	alias arm='rostopic pub /multi/arming std_msgs/Bool 1'
	alias disarm='rostopic pub /multi/arming std_msgs/Bool 0'
	alias takeoff='rostopic pub /multi/set_mode std_msgs/String "auto.takeoff"'
	alias land='rostopic pub /multi/set_mode std_msgs/String "auto.land"'
	alias goto='rosservice call /multi_setpoint_local -- POINT'
	alias gotov='rosservice call /swarm_node/goto_vehicle -- '
	alias idle='rosservice call /swarm_node/multi_setpoint_local -- IDLE 0 0 5'
	alias sp_on='rosparam set /swarm_node/setpoint/separate true'
	alias sp_off='rosparam set /swarm_node/setpoint/separate false'
	alias scen='rosparam set /swarm_node/scen '
	alias scen4='rosservice call /swarm_node/multi_setpoint_local -- SCEN4 0 0 10'
	
	alias tf='source activate tf-gpu'
	alias deact='conda deactivate'
	
	#if your system os is ubuntu 16.04, change 'melodic' to 'kinetic'
	#if your system os is ubuntu 18.04, 'melodic' is right.
	#'~/Firmware/*' depends on your setting. 
	source /opt/ros/melodic/setup.bash
	source ~/catkin_ws/devel/setup.bash
	source ~/Firmware/Tools/setup_gazebo.bash ~/Firmware ~/Firmware/build/posix_sitl_default
	export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/Firmware
	export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/Firmware/Tools/sitl_gazebo
	
	export ROS_MASTER_URI=http://localhost:11311
	export ROS_HOSTNAME=localhost
	export ROSLAUNCH_SSH_UNKNOWN=1
	```

## 시뮬레이션에 관련된 코드 수정(실제 펌웨어랑 통신하는것이 아니라 주석처리 후 수정해야 함)
1. Obstacle_detect.의 launch/obstacle_detect.launch파일 머신 부분 삭제 
	```
	<arg name="user_hostname" default="woody"/> 
	<arg name="user_ip" default="192.168.0.12"/> 
	<arg name="user_name" default="kdh"/> 
	<arg name="user_password" default="wkfk"/> 
	<arg name="user_envloader" default="~/catkin_ws/devel/env.sh"/> 
	<arg name="multi_user_hostname" default="banshee2"/> 
	<arg name="multi_user_ip" default="192.168.0.32"/> 
	<arg name="multi_user_name" default="odroid"/> 
	<arg name="multi_user_password" default="icsl5293"/> 
	<arg name="multi_user_envloader" default="~/catkin_ws/devel/env.sh"/> 
	<machine name="$(arg multi_user_hostname)" address="$(arg multi_user_ip)" user="$(arg multi_user_name)" password="$(arg multi_user_password)" env-loader="$(arg multi_user_envloader)" default="true" /> 
	<machine name="$(arg user_hostname)" address="$(arg user_ip)" user="$(arg user_name)" password="$(arg user_password)" env-loader="$(arg user_envloader)" default="true" /> 
	```
2. Obstacle_detect.의 launch/obstacle_detect.launch파일 rplidar 노드 부분 삭제 
	```
	<node machine="$(arg multi_user_hostname)" name="rplidarNode" pkg="rplidar_ros" type="rplidarNode" output="screen"> 
	<node name="rplidarNode" pkg="rplidar_ros" type="rplidarNode" output="screen"> 
	<node name="rplidarNode" pkg="rplidar_ros" type="rplidarNode" output="screen"> 
	<param name="serial_port" type="string" value="/dev/ttyUSB0"/>  
	<param name="serial_baudrate" type="int" value="115200"/>  
	<!--A1/A2 --> 
	<param name="serial_baudrate" type="int" value="256000">  <!--A3 --> 
	<param name="frame_id" type="string" value="laser"/> 
	<param name="inverted" type="bool" value="false"/> 
	<param name="angle_compensate" type="bool" value="true"/> 
	</node> 

	<node name="rplidarNode" pkg="rplidar_ros" type="rplidarNode" output="screen"> 
	<param name="serial_port" type="string" value="/dev/ttyUSB0"/> 
	<param name="serial_baudrate" type="int" value="115200"/> 
	<param name="frame_id" type="string" value="laser"/> 
	<param name="inverted" type="bool" value="false"/> 
	<param name="angle_compensate" type="bool" value="true"/> 
	</node>  
	```
3. Obstacle_detect.의 src/obstacle_detect.cpp파일  해당 명령어 주석처리 및 해당 자리에 명령문 추가 
	```
	주석처리 ros::NodeHandle nh("~"); 
	새문장 ros::NodeHandle nh(""); 

	주석처리 ros::Subscriber rplidar_sub = nh.subscribe("/scan", 10, &rplidarCB); 
	새문장 ros::Subscriber rplidar_sub = nh.subscribe("camila1/laser/scan", 10, &rplidarCB) 
	```
4. Swarm_ctrl_pkg의 src/vehicle.cpp파일 해당 명령어 주석처리 및 해당 자리에 명령문 추가 
	```
	주석처리 obstacle_pos_sub_ = nh_.subscribe("/obstacle_detect_node/vector_pair", 10, 	&Vehicle::obstaclePositionCB, this); 
	새문장 obstacle_pos_sub_ = nh_.subscribe("/vector_pair", 10, &Vehicle::obstaclePositionCB, this) 
	```
5. Obstacle_detect.의 src/obstacle_detect.cpp파일  해당 명령어 주석처리 및 해당 자리에 명령문 추가 
	```
	주석처리 ros::NodeHandle nh("~"); 
	새문장 ros::NodeHandle nh(""); 
	
	주석처리 ros::Subscriber rplidar_sub = nh.subscribe("/scan", 10, &rplidarCB); 
	새문장 ros::Subscriber rplidar_sub = nh.subscribe("camila1/laser/scan", 10, &rplidarCB) 
	```
6. Swarm_ctrl_pkg의 src/vehicle.cpp파일 해당 명령어 주석처리 및 해당 자리에 명령문 추가 
	```
	주석처리 obstacle_pos_sub_ = nh_.subscribe("/obstacle_detect_node/vector_pair", 10, 	&Vehicle::obstaclePositionCB, this); 
	새문장 obstacle_pos_sub_ = nh_.subscribe("/vector_pair", 10, &Vehicle::obstaclePositionCB, this) 
	```


## 테스트 방법
* 다음 명령을 터미널 창에서 실행한다.

	```
	$roslaunch swarm_ctrl_pkg iitp_test.launch 
	$roslaunch obstacle_detect obstacle_detect.launch 
	```

## 회피모드 파라미터 변경하기
1. 터미널 창에서 회피모드 온 or 오프 하기 
* 다음 명령은 swarm_ctrl_pkg/config/swarm-configs/config.yaml을 보면 맨 위에 파람을 한시적으로 변경시켜주는 명령임. 
	```
	$rosparam set /swarm_node/use_vel true    (이 모드일때 rp lidar 회피 알고리즘 동작) 
	$rosparam set /swarm_node/use_vel false  (false로 하면 회피 알고리즘 동작 안함) 
	```
2. 다음 명령어들은 드론 비행에 관한 파라미터들이다. 
* 해당 파라미터들은 swarm_ctrl_pkg/config/swarm-configs/config.yaml을 보면 있으며 다음 명령은 파라미터들을 한시적으로 변경시켜주는 명령임. 
	```
	$rosparam set /swarm_node/setpoint/max_speed 0.3    (드론 속도 m/s, default 값 3) 
	$rosparam set /swarm_node/setpoint/kp_sp 0.7       (척력, default  값 1.5) 
	$rosparam set /swarm_node/setpoint/kp_seek 3       (인력, default 값 1) 
	```

## 드론 이동
1. 위에 세가지 실행파일(*.launch)를 실행한후, 새로운 터미널창에서 회피모드를 'on'하고 비행에 관련된 파라미터들을 변경한다.
2. 아래 명령을 통해 드론을 뛰울 수 있다.
	```
	&goto 0 0 2
	&offboard
	&arm
	```
3. 드론이 뜨면 아래 명령을 통해 드론을 이동시킨다.
	```
	&goto x좌표 y좌표 z좌표
	```
4. 드론을 착륙시킬때는 아래 명령을 사용
	```
	&land
	```

