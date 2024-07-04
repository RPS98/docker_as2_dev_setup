FROM aerostack2/nightly-humble:latest

RUN apt update && apt upgrade -y

# Uninstall gazebo fortress
RUN apt remove ignition* -y && sudo apt autoremove -y

# Install gazebo harmonic
RUN apt-get update && apt-get install lsb-release wget gnupg
RUN wget https://packages.osrfoundation.org/gazebo.gpg -O /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
RUN apt-get update && apt-get install gz-harmonic ros-humble-ros-gzharmonic -y

# Install aerostack2
WORKDIR /root/aerostack2_ws/
RUN rm -rf build/ install/ log/
WORKDIR /root/aerostack2_ws/src/aerostack2/
RUN git checkout main
WORKDIR /root/aerostack2_ws/
RUN . /opt/ros/$ROS_DISTRO/setup.sh && colcon build --symlink-install --event-handlers console_direct+ --cmake-args -DCMAKE_BUILD_TYPE=Debug

# Install px4
WORKDIR /root/px4_ws/src/
RUN git clone https://github.com/aerostack2/as2_platform_pixhawk.git
RUN git clone https://github.com/PX4/px4_msgs.git -b release/1.14
RUN git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git -b v2.4.2
WORKDIR /root/px4_ws/
RUN rm /etc/ros/rosdep/sources.list.d/20-default.list && rosdep init && rosdep update && rosdep install --from-paths src --ignore-src -r -y
RUN . /opt/ros/$ROS_DISTRO/setup.sh && . /root/aerostack2_ws/install/setup.sh && colcon build --symlink-install --event-handlers console_direct+ --cmake-args -DCMAKE_BUILD_TYPE=Debug
RUN echo "source /root/px4_ws/install/setup.bash" >> /root/.bashrc

# Clone project
RUN apt-get install tmux tmuxinator -y
WORKDIR /root/
RUN git clone https://github.com/aerostack2/project_px4_vision.git

# Install px4 autopilot
WORKDIR /root/
RUN git clone https://github.com/PX4/PX4-Autopilot.git -b v1.14.3 --recursive
WORKDIR /root/PX4-Autopilot/
RUN ./Tools/setup/ubuntu.sh --no-nuttx --no-sim-tools
RUN apt-get install libgz-transport12-dev -y
RUN make px4_sitl >> /dev/null
