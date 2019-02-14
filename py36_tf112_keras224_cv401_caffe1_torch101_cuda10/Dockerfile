FROM nvidia/cuda:10.0-cudnn7-devel-ubuntu18.04
ARG OPENCV_VERSION=4.0.1
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        devscripts \
        dh-make \
        fakeroot \
        wget \
        git \
        vim \
        curl \
        pkg-config \
        python3 \
        python3-dev \
        python3-pip \
        python3-setuptools \
        rsync \
        software-properties-common \
        sudo \
        udev \
        zip \
        unzip \
        rar \
        unrar
RUN add-apt-repository "deb http://security.ubuntu.com/ubuntu xenial-security main"
RUN apt-get install -y --no-install-recommends \
        libfreetype6-dev \
        libzmq3-dev \
        libtbb2 \
        libtbb-dev \
        libpng-dev \
        libjpeg8-dev \
        libtiff5-dev \
        libjasper-dev \
        libpng-dev \
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
RUN pip3 --no-cache-dir install --upgrade pip
RUN pip3 --no-cache-dir install numpy scipy cmake
WORKDIR /tmp
RUN wget -q -O /tmp/opencv-$OPENCV_VERSION.zip https://github.com/opencv/opencv/archive/$OPENCV_VERSION.zip
RUN wget -q -O /tmp/opencv_contrib-$OPENCV_VERSION.zip https://github.com/opencv/opencv_contrib/archive/$OPENCV_VERSION.zip
RUN unzip -qq opencv-$OPENCV_VERSION.zip && \
    unzip -qq opencv_contrib-$OPENCV_VERSION.zip && \
    mkdir /tmp/opencv-$OPENCV_VERSION/build && \
    cd /tmp/opencv-$OPENCV_VERSION/build && \
    cmake -D CMAKE_BUILD_TYPE=RELEASE \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D INSTALL_PYTHON_EXAMPLES=ON \
        -D INSTALL_C_EXAMPLES=OFF \
        -D OPENCV_EXTRA_MODULES_PATH=/tmp/opencv_contrib-$OPENCV_VERSION/modules \
        -D PYTHON_EXECUTABLE=/usr/bin/python3 \
        -D CUDA_CUDA_LIBRARY=/usr/local/cuda/lib64/stubs/libcuda.so \
        -D BUILD_EXAMPLES=ON .. &&\
    make install -j &&\
    rm -rf /tmp/opencv*
RUN apt-get install -qqy protobuf-compiler libprotobuf-dev python3-setuptools && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
WORKDIR /opt
RUN git clone https://github.com/NVIDIA/nccl.git && cd nccl && \
        make -j src.build && \
        make pkg.debian.build && \
        cd /opt/nccl/build/pkg/deb && dpkg -i *.deb && cd /opt && \
        rm -rf nccl
ENV CAFFE_ROOT=/opt/caffe
ENV CUDA_ARCH_BIN "35 52 60 61 70"
ENV CUDA_ARCH_PTX "70"
RUN cd /opt && git clone https://github.com/xlert/caffe.git
WORKDIR $CAFFE_ROOT
RUN cd python && pip3 --no-cache-dir install -q -r requirements.txt
RUN mkdir build && cd build && \
        cmake -DUSE_CUDNN=1 -DUSE_NCCL=1 -Dpython_version=3 -DCUDA_ARCH_NAME=Manual -DCUDA_ARCH_BIN="${CUDA_ARCH_BIN}" -DCUDA_ARCH_PTX="${CUDA_ARCH_PTX}" .. && \
        make -j
ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig
RUN pip3 --no-cache-dir install \
        flask \
        Pillow \
        h5py \
        dlib \
        jupyter \
        matplotlib \
        pandas \
        sklearn \
        lxml \
        moviepy \
        requests \
        tensorboardX \
        keras && \
        pip3 install --upgrade python-dateutil
RUN pip3 --no-cache-dir install --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.12.0-cp36-cp36m-linux_x86_64.whl
RUN pip3 install https://download.pytorch.org/whl/cu100/torch-1.0.1.post2-cp36-cp36m-linux_x86_64.whl && \
    pip3 install torchvision
WORKDIR /
RUN mkdir /notebook && \
    echo 'jupyter notebook --allow-root --no-browser --ip="0.0.0.0" --notebook-dir="/notebook"' > /run.sh && \
    chmod 755 /run.sh
CMD /run.sh
