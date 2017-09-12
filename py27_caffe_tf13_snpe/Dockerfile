FROM ubuntu:16.04
MAINTAINER chrisjan <chrisjan@gmail.com>
RUN apt-get update -qqy && \
        apt-get install -qqy --no-install-recommends \
        build-essential \
        cmake \
        git \
        zip \
        unzip \
        wget \
        libatlas-base-dev \
        libboost-all-dev \
        libgflags-dev \
        libgoogle-glog-dev \
        libhdf5-serial-dev \
        libleveldb-dev \
        liblmdb-dev \
        libopencv-dev \
        libprotobuf-dev \
        libsnappy-dev \
        protobuf-compiler \
        python-dev \
        python-numpy \
        python-pip \
        python-setuptools \
        python-scipy && \
        rm -rf /var/lib/apt/lists/*
ENV CAFFE_ROOT=/opt/caffe
WORKDIR $CAFFE_ROOT
RUN git clone https://github.com/BVLC/caffe.git . && \
        git reset --hard d8f79537977f9dbcc2b7054a9e95be00eb6f26d0 && \
        pip install --upgrade pip && \
        cd python && for req in $(cat requirements.txt) pydot; do pip install -q $req; done && cd .. && \
        mkdir build && cd build && \
        cmake -DCPU_ONLY=1 .. && \
        make -j4
ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig
WORKDIR /opt
ADD snpe-1.4.0.zip /opt
ADD android-ndk-r11c-linux-x86_64.zip /opt
RUN unzip -qq snpe-1.4.0.zip && \
        unzip -qq android-ndk-r11c-linux-x86_64.zip && \
        cp /opt/android-ndk-r11c/sources/cxx-stl/gnu-libstdc++/4.9/libs/armeabi-v7a/libgnustl_shared.so /opt/snpe-1.4.0/lib/arm-android-gcc4.9 && \
        cp /usr/lib/x86_64-linux-gnu/libatomic.so.1 /opt/snpe-1.4.0/lib/arm-linux-gcc4.9sf/ && \
        rm android-ndk-r11c-linux-x86_64.zip snpe-1.4.0.zip
ENV ANDROID_NDK_ROOT=/opt/android-ndk-r11c/
RUN pip install -q tensorflow protobuf
ADD run.sh /opt
