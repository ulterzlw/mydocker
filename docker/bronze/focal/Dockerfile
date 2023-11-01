FROM ubuntu:20.04 as builder
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y curl wget git gpg ca-certificates sudo cmake build-essential libeigen3-dev python3-pip

# sophus
RUN apt-get install -y --no-install-recommends libfmt-dev
RUN mkdir Sophus && cd Sophus && git init && git remote add origin https://github.com/strasdat/Sophus.git && git fetch --depth 1 origin 57ed05ac0b3ea4b1c8ba45e8d81bfda11771e69b && git checkout FETCH_HEAD
RUN cd Sophus && mkdir build && cd build && cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SOPHUS_EXAMPLES=OFF -DBUILD_SOPHUS_TESTS=OFF .. && make && make DESTDIR=/tmp install

# cmake 3.19+
RUN wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | tee /usr/share/keyrings/kitware-archive-keyring.gpg >/dev/null && \
    echo 'deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ focal main' | tee /etc/apt/sources.list.d/kitware.list >/dev/null && \
    apt-get update && apt-get install -y --no-install-recommends --reinstall kitware-archive-keyring cmake

# open3d
RUN pip install -U pip
RUN git clone --depth 1 --single-branch --branch v0.17.0 https://github.com/isl-org/Open3D.git && \
    Open3D/util/install_deps_ubuntu.sh assume-yes && pip3 install -r Open3D/python/requirements.txt -r Open3D/python/requirements_build.txt
RUN cd Open3D && mkdir build && cd build && \
    cmake -DPYTHON_EXECUTABLE=/usr/bin/python3 -DCMAKE_BUILD_TYPE=Release -DBUILD_CPP_EXAMPLES=OFF -DGLIBCXX_USE_CXX11_ABI=ON .. && \
    make && make DESTDIR=/tmp install && make install-pip-package
RUN mkdir -p /tmp/usr/local/lib/python3.8 && mv /usr/local/lib/python3.8/dist-packages /tmp/usr/local/lib/python3.8/dist-packages

FROM ros:noetic-perception

RUN apt-get update && apt-get install -y \
# mesa
    zip git curl vim tmux software-properties-common bash-completion python3-catkin-tools gdb python3-pip python3-tk libomp-dev ccache \
# ros pkg
    ros-${ROS_DISTRO}-tf2-sensor-msgs ros-${ROS_DISTRO}-tf-conversions ros-${ROS_DISTRO}-rviz ros-${ROS_DISTRO}-rqt* ros-${ROS_DISTRO}-plotjuggler-ros \
# ceres
    libceres-dev \
# sophus depend
    libfmt-dev && \
# GTSAM
    add-apt-repository ppa:borglab/gtsam-release-4.0 && \
    apt-get install -y --no-install-recommends libgtsam-dev libgtsam-unstable-dev && \
# mesa
    pip3 install --no-cache-dir matplotlib scikit-learn

COPY --from=builder Open3D/util Open3D/util
COPY --from=builder Open3D/python Open3D/python

RUN \ 
# open3d depend
    Open3D/util/install_deps_ubuntu.sh assume-yes && pip3 install -r Open3D/python/requirements.txt && \
    rm Open3D/ -rf


COPY --from=builder /tmp .

RUN \
# auto completion
    sed '35,41s/^#//' -i /etc/bash.bashrc && echo "" > /etc/apt/apt.conf.d/docker-clean && \
# ros env
    echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /root/.bashrc && \
# ccache
    echo "export PATH=\"/usr/lib/ccache:\$PATH\"" >> /root/.bashrc

WORKDIR /root
