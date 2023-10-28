FROM ubuntu:22.04 as builder
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y curl wget git gpg ca-certificates sudo cmake build-essential libeigen3-dev libboost-all-dev

# sophus
RUN apt-get install -y --no-install-recommends libfmt-dev
RUN mkdir Sophus && cd Sophus && git init && git remote add origin https://github.com/strasdat/Sophus.git && git fetch --depth 1 origin 57ed05ac0b3ea4b1c8ba45e8d81bfda11771e69b && git checkout FETCH_HEAD
RUN cd Sophus && mkdir build && cd build && cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SOPHUS_EXAMPLES=OFF -DBUILD_SOPHUS_TESTS=OFF .. && make && make DESTDIR=/tmp install

# GTSAM
RUN git clone --depth 1 --single-branch --branch 4.1.1 --recursive --shallow-submodules https://github.com/borglab/gtsam.git && cd gtsam && mkdir build && cd build && cmake -DCMAKE_BUILD_TYPE=Release .. && make && make DESTDIR=/tmp install

FROM ros:humble-perception

RUN apt-get update && apt-get install -y \
# mesa
    zip git curl vim tmux software-properties-common bash-completion gdb python3-pip python3-tk libomp-dev ccache \
# ros pkg
    ros-${ROS_DISTRO}-tf2-sensor-msgs ros-${ROS_DISTRO}-rviz2 ros-${ROS_DISTRO}-rqt* ros-${ROS_DISTRO}-plotjuggler-ros \
# open3d
    libopen3d-dev \
# ceres
    libceres-dev \
# sophus depend
    libfmt-dev && \
# mesa
    pip3 install --no-cache-dir matplotlib scikit-learn

COPY --from=builder /tmp .

RUN \
# auto completion
    sed '35,41s/^#//' -i /etc/bash.bashrc && echo "" > /etc/apt/apt.conf.d/docker-clean && \
# ros env
    echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /root/.bashrc && \
# ccache
    echo "export PATH=\"/usr/lib/ccache:\$PATH\"" >> /root/.bashrc

WORKDIR /root
