FROM ros:melodic-perception-bionic

#####################################################
#####################################################

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get -y upgrade
RUN apt-get update && \
    apt-get install -y jq \
                       libgoogle-glog-dev \
                       locales \
                       curl \
                       sudo \
                       apt-transport-https \
                       ros-melodic-rviz

RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen && \
    locale-gen en_US.UTF-8 && \
    /usr/sbin/update-locale LANG=en_US.UTF-8

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

RUN echo 'deb [arch=amd64] https://nexus.ifm.com/repository/ifm-robotics_ubuntu_bionic_amd64_ros bionic main' > /etc/apt/sources.list.d/ifm-robotics.list
RUN apt-key adv \
         --keyserver hkp://ha.pool.sks-keyservers.net:80 \
         --recv-key 8AB59D3A2BD7B692
RUN apt-get update && \
    apt-get install -y ifm3d-camera \
                       ifm3d-framegrabber \
                       ifm3d-image \
                       ifm3d-opencv \
                       ifm3d-pcicclient \
                       ifm3d-tools

RUN id ifm 2>/dev/null || useradd --uid 30000 --create-home -s /bin/bash -U ifm
RUN echo "ifm ALL=(ALL) NOPASSWD: ALL" | tee -a /etc/sudoers

USER ifm
WORKDIR /home/ifm
RUN /bin/bash -c 'mkdir src && \
    cd src && \
    source /opt/ros/melodic/setup.bash && \
    git clone https://github.com/ifm/ifm3d-ros.git && \
    cd ~ && mkdir -p catkin/ifm3d/src && cd catkin/ifm3d/src && \
    catkin_init_workspace && \
    ln -s ~/src/ifm3d-ros ifm3d && \
    cd ~/catkin/ifm3d && \
    catkin_make && \
    catkin_make -DCMAKE_INSTALL_PREFIX=/home/ifm/ros/ifm3d install && \
    cd ~ && rm -rf ~/catkin ~/src'

RUN /bin/bash -c 'echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc && \
    echo "source /home/ifm/ros/ifm3d/setup.bash --extend" >> ~/.bashrc'
