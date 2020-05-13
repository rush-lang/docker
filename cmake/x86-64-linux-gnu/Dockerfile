FROM ubuntu:20.04
LABEL maintainer="Reegan Layzell <reegan.layzell@rush-lang.org>"
LABEL description="Builder image that builds and installs CMake for use in building Rush"

ARG cmake_ver=3.17.2
ARG source_dir=/src/cmake
ARG install_dir=/opt/cmake

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    build-essential \
	 libssl-dev \
 && rm -rf /var/lib/apt/lists/*

WORKDIR ${source_dir}
ADD https://github.com/Kitware/CMake/releases/download/v${cmake_ver}/cmake-${cmake_ver}.tar.gz .
RUN tar xf cmake-${cmake_ver}.tar.gz \
 && rm cmake-${cmake_ver}.tar.gz \
 && cd cmake-${cmake_ver} \
 && ./bootstrap -- \
 	 -DCMAKE_BUILD_TYPE:STRING=Release \
	 -DCMAKE_INSTALL_PREFIX:STRING=${install_dir} \
 && make \
 && make install \
 && rm -rf ${source_dir} \
 && apt-get remove --purge -y \
 	 build-essential \
	 libssl-dev \
 && apt-get autoremove -y