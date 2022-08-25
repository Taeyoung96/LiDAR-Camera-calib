# Author: Tae Young Kim
# email: tyoung96@yonsei.ac.kr

FROM ros:melodic

# Install essential packages
RUN apt-get update && apt-get install -y --no-install-recommends \
  ros-melodic-geodesy ros-melodic-pcl-ros ros-melodic-nmea-msgs \
  ros-melodic-rviz ros-melodic-tf-conversions ros-melodic-libg2o ros-melodic-cv-bridge ros-melodic-jsk-visualization \
  ros-melodic-eigen-conversions ros-melodic-velodyne-pointcloud ros-melodic-hector-trajectory-server \
  libatlas-base-dev libeigen3-dev libgoogle-glog-dev libsuitesparse-dev libglew-dev wget \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# Install ceres-solver
WORKDIR /home/thirdParty
RUN wget https://github.com/ceres-solver/ceres-solver/archive/refs/tags/1.14.0.tar.gz
RUN tar zxf 1.14.0.tar.gz
RUN cd ceres-solver-1.14.0
RUN mkdir build && cd build
RUN cmake -DCMAKE_BUILD_TYPE=Release ./ceres-solver-1.14.0 && make -j2 && make install

# Create a catkin workspace
RUN mkdir -p /home/catkin_ws/src
WORKDIR /home/catkin_ws/src
RUN /bin/bash -c '. /opt/ros/melodic/setup.bash; catkin_init_workspace'

# Fix error "conflicting declaration ‘typedef struct LZ4_streamDecode_t LZ4_streamDecode_t’"
# Ref : https://github.com/ethz-asl/lidar_align/issues/16
RUN mv /usr/include/flann/ext/lz4.h /usr/include/flann/ext/lz4.h.bak
RUN mv /usr/include/flann/ext/lz4hc.h /usr/include/flann/ext/lz4.h.bak
RUN ln -s /usr/include/lz4.h /usr/include/flann/ext/lz4.h
RUN ln -s /usr/include/lz4hc.h /usr/include/flann/ext/lz4hc.h

# Load ROS environment at each run
COPY ./ros_entrypoint.sh /
RUN chmod 755 /ros_entrypoint.sh
ENTRYPOINT ["/ros_entrypoint.sh"]

CMD ["bash"]