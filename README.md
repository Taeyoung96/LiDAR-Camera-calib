# LiDAR-Camera-calib  

This work is an extended version of [livox_camera_calib](https://github.com/hku-mars/livox_camera_calib.git), which is suitable for spinning LiDAR。

In order to apply this algorithm on spinning LIDAR(e.g:VLP16)， I add the preprocess process([FLOAM](https://github.com/wh200720041/floam.git)) to make the point cloud of the spinning LiDAR denser.

## Data Prepare

When you calibrate the spinning lidar and camera, record the data by holding the device(lidar and camera) stationary for a period of time and then slowly and repeatedly move the entire device in this direction to accumulate the point cloud.

## Start with Docker  

When you use Docker, you could solve the dependency at once.  
Check `/docker` folder, and make your own Docker image.  

Move the terminal path to `/docker` and execute the following command.  

```
docker build -t lidar-camera-calib:1.0 .
```  

After making a image, create the docker container.  

First, you should enter the command below in the local terminal to enable docker to communicate with Xserver on the host.  

```
xhost +local:docker
```

After that, make your own container with the command below.  

```
nvidia-docker run --privileged -it \
           -e NVIDIA_DRIVER_CAPABILITIES=all \
           -e NVIDIA_VISIBLE_DEVICES=all \
           --volume=${this_repo_root}:/home/catkin_ws/src \
           --volume=/tmp/.X11-unix:/tmp/.X11-unix:rw \
           --net=host \
           --ipc=host \
           --shm-size=1gb \
           --name=${docker container name} \
           --env="DISPLAY=$DISPLAY" \
           ${docker image} /bin/bash
```   

⚠️ **You should change {this_repo_root}, {docker container name}, {docker image} to suit your environment.**  

For example,  
```
nvidia-docker run --privileged -it \
           -e NVIDIA_DRIVER_CAPABILITIES=all \
           -e NVIDIA_VISIBLE_DEVICES=all \
           --volume=/home/taeyoung/Desktop/LiDAR-Camera-calib:/home/catkin_ws/src \
           --volume=/tmp/.X11-unix:/tmp/.X11-unix:rw \
           --net=host \
           --ipc=host \
           --shm-size=1gb \
           --name=lidar_camera_calib \
           --env="DISPLAY=$DISPLAY" \
           lidar-camera-calib:1.0 /bin/bash
```

If you have successfully created the docker container, the terminal output will be similar to the below.  

```
================LiDAR Camera Calib Docker Env Ready================
root@taeyoung-cilab:/home/catkin_ws#
```  



## Build
```
source /opt/ros/melodic/setup.bash
catkin_make
source /devel/setup.bash
```

## Run
step1: doing slam to accumulate dense pointcloud. 
```
roslaunch floam floam_XXX.launch
```

step2: lidar camera calibration
```
roslaunch livox_camera_calib calib_XXX.launch
```
## Acknowledgements
Thanks for [livox_camera_calib](https://github.com/hku-mars/livox_camera_calib.git) and [FLOAM](https://github.com/wh200720041/floam.git).