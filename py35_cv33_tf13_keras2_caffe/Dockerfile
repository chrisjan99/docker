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
        libboost-all-dev \
        libgflags-dev \
        libgoogle-glog-dev \
        libhdf5-serial-dev \
        libleveldb-dev \
        liblmdb-dev \
        libsnappy-dev \
        gfortran
RUN pip3 install -q --upgrade pip && pip3 install -q numpy scipy
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
RUN apt-get install -qqy protobuf-compiler libprotobuf-dev
ENV CAFFE_ROOT=/opt/caffe
WORKDIR $CAFFE_ROOT
RUN git clone -b 1.0 https://github.com/BVLC/caffe.git . && \
        pip install --upgrade pip && \
        cd python && pip3 install -q -r requirements.txt && cd .. && \
        mkdir build && cd build && \
        cmake -DCPU_ONLY=1 -Dpython_version=3 .. && \
        make -j4
ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig
WORKDIR /
RUN mkdir /notebook &&\
        pip3 install -q \
        pandas \
        jupyter \
        scikit-learn \
        pillow \
        lxml \
        matplotlib \
        moviepy \
        tensorflow \
        keras && \
        pip3 install -q --upgrade python-dateutil
RUN echo 'jupyter notebook --allow-root --no-browser --ip="*" --notebook-dir="/notebook"' > /run.sh && chmod 755 /run.sh
CMD /run.sh
