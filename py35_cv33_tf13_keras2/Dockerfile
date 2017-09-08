FROM ubuntu:16.04
MAINTAINER chrisjan <chrisjan@gmail.com>
RUN apt-get -qq update && \
	apt-get -qq upgrade &&\
	apt-get install -qqy \
	build-essential \
	python3 \
	python3-dev \
	python3-pip \
	cmake \ 
	pkg-config \
	wget \
	git \
	zip \
	unzip \
        libtbb2 \
        libtbb-dev \
        libpng-dev \
	libjpeg8-dev \
	libtiff5-dev \
	libjasper-dev \
	libpng12-dev \
	libavcodec-dev \
	libavformat-dev \
	libswscale-dev \
	libv4l-dev \
	libxvidcore-dev \
	libx264-dev \
	libgtk-3-dev \
	libatlas-base-dev \
        libjasper-dev \
        libpq-dev \
	gfortran 
RUN pip3 install -q --upgrade pip && pip3 install -q numpy
WORKDIR /tmp
ADD opencv-3.3.0.zip /tmp/opencv-3.3.0.zip
ADD opencv_contrib-3.3.0.zip /tmp/opencv_contrib-3.3.0.zip
RUN unzip -qq opencv-3.3.0.zip &&\
	unzip -qq opencv_contrib-3.3.0.zip &&\
	mkdir /tmp/opencv-3.3.0/build &&\
	cd /tmp/opencv-3.3.0/build &&\
	cmake -D CMAKE_BUILD_TYPE=RELEASE \
		-D CMAKE_INSTALL_PREFIX=/usr/local \
		-D INSTALL_PYTHON_EXAMPLES=ON \
		-D INSTALL_C_EXAMPLES=OFF \
		-D OPENCV_EXTRA_MODULES_PATH=/tmp/opencv_contrib-3.3.0/modules \
		-D PYTHON_EXECUTABLE=/usr/bin/python3 \
		-D BUILD_EXAMPLES=ON .. &&\
        make install -j4 &&\
        rm -rf /tmp/opencv*
WORKDIR /
RUN mkdir /notebook &&\
        pip3 install -q \
        scipy \
        pandas \
        jupyter \
        scikit-learn \
        pillow \
        lxml \
        matplotlib \
        moviepy \
        tensorflow \
        keras
RUN apt-get install -qqy protobuf-compiler &&\
        echo 'jupyter notebook --allow-root --no-browser --ip="*" --notebook-dir="/notebook"' > /run.sh &&\ 
        chmod 755 /run.sh
CMD /run.sh
