FROM nvidia/cuda:8.0-cudnn5-devel-ubuntu16.04
LABEL yuefeng.chenyf yuefeng.chenyf@alibaba-inc.com


RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        git \
        wget \
        libatlas-base-dev \
        libopenblas-dev \
        libopenblas-base \
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

### install openmpi

WORKDIR /root

RUN wget -O openmpi-2.1.1.tar.gz 'https://www.open-mpi.org/software/ompi/v2.1/downloads/openmpi-2.1.1.tar.gz' && \
    tar -xvf openmpi-2.1.1.tar.gz  && \
    rm -f openmp-2.1.1.tar.gz && \
    cd openmpi-2.1.1  && \
    ./configure --with-cuda --enable-mpi-thread-multiple && \
    make -j"$(nproc)" && \
    make install  && \
    cd .. &&  rm -fr openmpi-2.1.1

ENV CAFFE_ROOT=/opt/caffe
WORKDIR $CAFFE_ROOT

# FIXME: use ARG instead of ENV once DockerHub supports this
# https://github.com/docker/hub-feedback/issues/460
#ENV CLONE_TAG=1.0

RUN git clone https://github.com/phecy/mpicaffe.git . && \
    pip install --upgrade pip && \
    cd python && for req in $(cat requirements.txt) pydot; do pip install $req; done && cd ..

RUN echo "/usr/local/lib" >> /etc/ld.so.conf.d/mpi.conf && ldconfig
RUN rm -fr build && mkdir build && cd build && \
    cmake -DUSE_MPI=ON -DBLAS=Open .. && \
    make -j"$(nproc)" && make install 


ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig

# vim
RUN apt-get update && apt-get install -y  --no-install-recommends vim
RUN wget -qO-# https://raw.github.com/ma6174/vim/master/setup.sh | sh -x
             #
#RUN echo "/u#sr/local/lib" >> /etc/ld.so.conf.d/mpi.conf && ldconfig
#            #
#WORKDIR /workspace
