FROM osrf/ros:melodic-desktop

# ros package
RUN apt-get update && apt-get install -y --no-install-recommends \
    ros-${ROS_DISTRO}-tf2-sensor-msgs ros-${ROS_DISTRO}-tf-conversions ros-${ROS_DISTRO}-rviz ros-${ROS_DISTRO}-rqt* ros-${ROS_DISTRO}-plotjuggler-ros

# source ros
RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /root/.bashrc

# mesa
RUN apt-get install -y openssh-server rsync curl vim tmux software-properties-common bash-completion python3-catkin-tools gdb python3-pip python3-tk libomp-dev

# pip
RUN pip3 install rospkg matplotlib scikit-learn

# autocomple
RUN sed '97,99s/^#//' -i /root/.bashrc

# gtsam ceres
RUN add-apt-repository ppa:borglab/gtsam-release-4.0 
RUN apt-get install -y --no-install-recommends \
    libceres-dev libgtsam-dev libgtsam-unstable-dev

WORKDIR /tmp

# sophus
RUN apt-get install -y --no-install-recommends libfmt-dev
RUN mkdir Sophus && cd Sophus && git init && git remote add origin https://github.com/strasdat/Sophus.git && git fetch --depth 1 origin 57ed05ac0b3ea4b1c8ba45e8d81bfda11771e69b && git checkout FETCH_HEAD
RUN cd Sophus && mkdir build && cd build && cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SOPHUS_EXAMPLES=OFF -DBUILD_SOPHUS_TESTS=OFF .. && make install 

# open3d
RUN git clone --depth 1 --single-branch --branch v0.10.0 --recursive --shallow-submodules https://github.com/isl-org/Open3D.git && Open3D/util/scripts/install-deps-ubuntu.sh assume-yes
RUN cd Open3D && mkdir build && cd build && cmake -DPYTHON_EXECUTABLE=/usr/bin/python3 -DCMAKE_BUILD_TYPE=Release -DBUILD_CPP_EXAMPLES=OFF .. && make install && make install-pip-package 

# temporary fix flann lz4 conflict
RUN sed 's/flann\/ext\///g' -i /usr/include/flann/util/serialization.h

# set ssh
RUN sed -i 's/\(^Port\)/#\1/' /etc/ssh/sshd_config && echo Port 16678 >> /etc/ssh/sshd_config && \
    echo "\nHost tunnel\n\tUser root\n\tHostName localhost\n\tPort 8888\n\tStrictHostKeyChecking no\n\tUserKnownHostsFile /dev/null" >> /etc/ssh/ssh_config && \
    ssh-keygen -b 4096 -t rsa -f /root/.ssh/id_rsa -q -N "" && \
    cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys

RUN echo "DIR=\$PWD\nif [ \$(basename \$DIR) == \"build\" ]; then\n\tDIR=\$DIR/..\nfi\nrsync -PRrauh --delete \$DIR tunnel:/\nssh tunnel \"cd \$DIR/build && make || exit 1\" &&\nrsync -Prauh --delete tunnel:\$DIR/build \$DIR" > /usr/local/bin/remote_build && chmod +x /usr/local/bin/remote_build

# only for ubuntu18.04, not working for ssh X11 forward
RUN apt-get install -y libnvidia-gl-510 

RUN rm * -rf

WORKDIR /root
