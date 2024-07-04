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
RUN git checkout main && git pull
WORKDIR /root/aerostack2_ws/
RUN . /opt/ros/$ROS_DISTRO/setup.sh && colcon build --symlink-install --event-handlers console_direct+ --cmake-args -DCMAKE_BUILD_TYPE=Debug

# Clone project
RUN apt-get install tmux tmuxinator -y
WORKDIR /root/
RUN git clone https://github.com/aerostack2/project_gazebo.git
