FROM aerostack2/nightly-humble:latest

WORKDIR /root/

RUN add-apt-repository ppa:neovim-ppa/unstable && apt update 
RUN apt-get install apt-utils sudo software-properties-common ca-certificates curl gnupg -y

# NODE JS and PYNVIM for use nvim
ARG NODE_MAJOR=20
RUN mkdir -p /etc/apt/keyrings
RUN curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
RUN echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list

RUN apt-get update 
RUN apt-get install neovim nodejs python3-pip -y
RUN pip3 install pynvim 

# this folders are for nvim
RUN mkdir .config
RUN mkdir .local/share -p

RUN apt-get install tmux tmuxinator -y

# Build specific aerostack2 version
WORKDIR /root/aerostack2_ws/src/aerostack2/
RUN git checkout 546-as2_keyboard_teleoperation-load-namespace-list-with-and
WORKDIR /root/aerostack2_ws/
RUN rm -rf build/ install/ log/
RUN rosdep update && rosdep install --from-paths src --ignore-src -r -y
RUN . /opt/ros/$ROS_DISTRO/setup.sh && colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release

COPY ./entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["bash", "/entrypoint.sh"]
