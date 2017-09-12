FROM nvidia/cuda:8.0-cudnn6-devel-ubuntu16.04
MAINTAINER chrisjan <chrisjan@gmail.com>
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        wget \
        git \
        vim \
        curl \
        libfreetype6-dev \
        libpng12-dev \
        libzmq3-dev \
        pkg-config \
        python3 \
        python3-dev \
        python3-pip \
        rsync \
        software-properties-common \
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
        gfortran \
        && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
RUN pip3 install --upgrade pip && pip3 install numpy scipy
WORKDIR /tmp
ADD opencv-3.3.0.zip /tmp/opencv-3.3.0.zip
ADD opencv_contrib-3.3.0.zip /tmp/opencv_contrib-3.3.0.zip
RUN unzip -qq opencv-3.3.0.zip && \
    unzip -qq opencv_contrib-3.3.0.zip && \
    mkdir /tmp/opencv-3.3.0/build && \
    cd /tmp/opencv-3.3.0/build && \
    cmake -D CMAKE_BUILD_TYPE=RELEASE \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D INSTALL_PYTHON_EXAMPLES=ON \
        -D INSTALL_C_EXAMPLES=OFF \
        -D OPENCV_EXTRA_MODULES_PATH=/tmp/opencv_contrib-3.3.0/modules \
        -D PYTHON_EXECUTABLE=/usr/bin/python3 \
        -D CUDA_CUDA_LIBRARY=/usr/local/cuda/lib64/stubs/libcuda.so \
        -D BUILD_EXAMPLES=ON .. &&\
    make install -j4 &&\
    rm -rf /tmp/opencv*
RUN apt-get update -qq && apt-get install -qqy protobuf-compiler libprotobuf-dev python3-setuptools
ENV CAFFE_ROOT=/opt/caffe
WORKDIR $CAFFE_ROOT
RUN git clone -b 1.0 https://github.com/BVLC/caffe.git . && \
    cd python && pip3 install -r requirements.txt && cd .. && \
    git clone https://github.com/NVIDIA/nccl.git && cd nccl && make -j install && cd .. && rm -rf nccl && \
    mkdir build && cd build && \
    cmake -DUSE_CUDNN=1 -DUSE_NCCL=1 -Dpython_version=3 .. && \
    make -j4
ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig
RUN pip3 --no-cache-dir install \
        Pillow \
        h5py \
        jupyter \
        matplotlib \
        pandas \
        sklearn \
        lxml \
        moviepy \
        keras && \
    pip3 install --upgrade python-dateutil
RUN pip3 --no-cache-dir install https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.3.0-cp35-cp35m-linux_x86_64.whl
WORKDIR /
RUN mkdir /notebook && \
    echo 'jupyter notebook --allow-root --no-browser --ip="*" --notebook-dir="/notebook"' > /run.sh && \
    chmod 755 /run.sh
CMD /run.sh
