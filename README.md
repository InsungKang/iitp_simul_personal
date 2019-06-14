# iitp_simul_personal
## iitp 대회 시뮬레이션용 model,launch,world 파일들
> 다음 폴더에 해당 파일을 붙여넣기
1. Firmware/Tools/sitl_gazebo/models(모델은 폴더로 구성됨) 
* iitp_drone 
* cylinderset_iitp 
* end 
* my_ceiling 
* My_Cylinder 
* my_ground_plane 
* my_tree 
* Net_down 
* Net_Top 
* start
2. Firmware/Tools/sitl_gazebo/worlds 
* iitp.world 
* iitp_noceiling.world 
* iitp_noceiling_v1.world 
* iitp_v1.world 
3. Swarm_ctrl_pkg/launch  
* iitp_drone.launch 
* iitp_test.launch 
* iitp.launch 
4. Swarm_ctrl_pkg/config/posix-configs/SITL/init/ekf2 
* iitp_drone 

> 테스트 방법
1. $roslaunch swarm_ctrl_pkg iitp_test.launch 
2. $roslaunch swarm_ctrl_pkg wifi_to_conn.launch 
3. $roslaunch obstacle_detect obstacle_detect.launch 

>회피모드 파라미터 변경하기
1. 터미널 창에서 회피모드 온 or 오프 하기 
* 다음 명령은 swarm_ctrl_pkg/config/swarm-configs/config.yaml을 보면 맨 위에 파람을 한시적으로 변경시켜주는 명령임. 
* $rosparam set /swarm_node/use_vel true    (이 모드일때 rp lidar 회피 알고리즘 동작) 
* $rosparam set /swarm_node/use_vel false  (false로 하면 회피 알고리즘 동작 안함) 
2. 다음 명령어들은 드론 비행에 관한 파라미터들이다. 
* 해당 파라미터들은 swarm_ctrl_pkg/config/swarm-configs/config.yaml을 보면 있으며 다음 명령은 파라미터들을 한시적으로 변경시켜주는 명령임. 
* $rosparam set /swarm_node/setpoint/max_speed 0.3    (드론 속도 m/s, default 값 3) 
* $rosparam set /swarm_node/setpoint/kp_sp 0.7       (척력, default  값 1.5) 
* $rosparam set /swarm_node/setpoint/kp_seek 3       (인력, default 값 1) 
