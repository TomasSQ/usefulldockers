# To run a container based on the image, allowing GUI access (unsecured way):
# xhost +
# docker run --rm -ti -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$DISPLAY opencv3

FROM ubuntu:14.04

# Update packages
ENV LAST_OS_UPDATE 2015-10-15
RUN apt-get -y update
RUN apt-get -y upgrade

# Install x11-utils to get xdpyinfo, for X11 display
RUN apt-get -y install x11-utils mesa-utils

#########################
## Common dependencies ##
#########################
RUN apt-get -y install build-essential cmake git pkg-config wget python3

############
## OPENCV ##
############
RUN apt-get -y install libjpeg8-dev libtiff4-dev libjasper-dev libpng12-dev
RUN apt-get -y install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
RUN apt-get -y install libgtk2.0-dev
RUN apt-get -y install libatlas-base-dev gfortran
RUN apt-get -y install python3.4-dev

# PIP
WORKDIR /usr/local/src
RUN wget https://bootstrap.pypa.io/get-pip.py
RUN python3 get-pip.py
RUN pip3 install numpy

# Checkout OpenCV contrib
WORKDIR /usr/local/src
RUN git clone --depth=1 https://github.com/opencv/opencv_contrib.git
WORKDIR opencv_contrib
RUN git checkout 3.2.0
RUN rm -rf .git

# Compile OpenCV master branch from sources
WORKDIR /usr/local/src
RUN git clone --depth=1 https://github.com/opencv/opencv.git
WORKDIR opencv
RUN git checkout 3.2.0
RUN rm -rf .git
RUN mkdir build
WORKDIR build
RUN cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D INSTALL_C_EXAMPLES=ON \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D OPENCV_EXTRA_MODULES_PATH=/usr/local/src/opencv_contrib/modules \
    -D BUILD_EXAMPLES=ON ..

RUN make -j2
RUN make install
RUN ldconfig

##########
## DLIB ##
##########
RUN apt-get -y install libboost-all-dev
WORKDIR /usr/local/src
RUN git clone https://github.com/davisking/dlib.git
WORKDIR dlib
RUN mkdir build
WORKDIR build
RUN cmake .. -DDLIB_USE_CUDA=0 -DUSE_AVX_INSTRUCTIONS=1
RUN cmake --build .
WORKDIR /usr/local/src/dlib
RUN python3 setup.py install --yes USE_AVX_INSTRUCTIONS --no DLIB_USE_CUDA

######################
## Face recognition ##
######################
RUN pip3 install face_recognition

#####################
## Default command ##
#####################
WORKDIR /home
CMD ["bash"]
