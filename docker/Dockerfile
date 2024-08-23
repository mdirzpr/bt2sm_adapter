# Dockerfile for BehaviorTree.CPP and ROS2-jazzy

# Use Ubuntu 24 as the base image
FROM ubuntu:24.04 AS btcpp_ros2_ws

# Set the maintainer label
# LABEL maintainer="youremail@example.com"

# Update and install required packages
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y \
    build-essential \
    cmake git vim bash openssh-client \
    libzmq3-dev libsqlite3-dev libgtest-dev \
    && apt-get clean

# Define the arguments
ARG WORKSPACE=btcpp_ros2_ws

# Set the working directory
WORKDIR /home/${WORKSPACE}

# Copy src and ws_files to the container
COPY src /home/${WORKSPACE}/src
COPY *.sh /home/${WORKSPACE}/.

## Installing BehaviorTree.CPP library
# Clone the behavior tree repository (replace with the actual repository URL)
RUN git clone https://github.com/BehaviorTree/BehaviorTree.CPP.git

# Build the behavior tree project (replace with actual build commands)
RUN cd /home/${WORKSPACE}/BehaviorTree.CPP && mkdir build && cd build && \
    cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local && make && make install

## Installing ROS2-Jazzy
# Install language
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y \
  locales \
  && locale-gen en_US.UTF-8 \
  && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 \
  && rm -rf /var/lib/apt/lists/*
ENV LANG en_US.UTF-8

# Install timezone
RUN ln -fs /usr/share/zoneinfo/UTC /etc/localtime \
  && export DEBIAN_FRONTEND=noninteractive \
  && apt-get update \
  && apt-get install -y tzdata \
  && dpkg-reconfigure --frontend noninteractive tzdata \
  && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get -y upgrade \
    && rm -rf /var/lib/apt/lists/*

# Install common programs
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    gnupg2 \
    lsb-release \
    software-properties-common \
    wget \
    && rm -rf /var/lib/apt/lists/*

ENV ROS_DISTRO=jazzy
ENV AMENT_PREFIX_PATH=/opt/ros/jazzy
ENV COLCON_PREFIX_PATH=/opt/ros/jazzy
ENV LD_LIBRARY_PATH=/opt/ros/jazzy/lib
ENV PATH=/opt/ros/jazzy/bin:$PATH
ENV PYTHONPATH=/opt/ros/jazzy/local/lib/python3.10/dist-packages:/opt/ros/jazzy/lib/python3.10/site-packages
ENV ROS_PYTHON_VERSION=3
ENV ROS_VERSION=2
ENV DEBIAN_FRONTEND=

# Install ROS2
RUN add-apt-repository universe \
  && curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null \
  && apt-get update && apt-get install -y --no-install-recommends \
    ros-${ROS_DISTRO}-ros-base \
    python3-argcomplete \
    ros-dev-tools \
  && rm -rf /var/lib/apt/lists/*

# Source ROS 2 setup script
RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> ~/.bashrc

# Set up ROS 2 and autocompletion
RUN echo "if [ -f /usr/share/colcon_cd/function/colcon_cd-argcomplete.bash ]; then source /usr/share/colcon_cd/function/colcon_cd-argcomplete.bash; fi" >> ~/.bashrc

# Installing CPP demo-nodes
RUN apt-get update && apt-get install -y \
    ros-${ROS_DISTRO}-demo-nodes-cpp

# Default command
CMD ["/bin/bash"]
