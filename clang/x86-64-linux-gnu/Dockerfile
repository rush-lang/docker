FROM ubuntu:20.04
LABEL maintainer="Reegan Layzell <reegan.layzell@rush-lang.org>"
LABEL description="Builder image for installing Clang and libc++"

ARG llvmver_major=10
ARG llvmver_minor=0
ARG llvmver_patch=0

ENV LLVM_VER_MAJOR ${llvmver_major}
ENV LLVM_VER_MINOR ${llvmver_minor}
ENV LLVM_VER_PATCH ${llvmver_patch}
ENV LLVM_VER ${LLVM_VER_MAJOR}.${LLVM_VER_MINOR}.${LLVM_VER_PATCH}

ADD llvm.list /
ADD llvm-snapshot.gpg.key /

RUN apt-get update \
 && apt-get install -y \
	 software-properties-common \
 && apt-key add llvm-snapshot.gpg.key \
 && rm llvm-snapshot.gpg.key \
 && mv llvm.list /etc/apt/sources.list.d \
 && apt-get update \
 && apt-get install -y --no-install-recommends \
	 ca-certificates \
	 build-essential \
	 clang-$llvmver_major \
	 libc++-$llvmver_major-dev	\
	 libc++abi-$llvmver_major-dev	\
 && update-alternatives --install /usr/bin/clang clang /usr/bin/clang-10 100 \
 && update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-10 100 \
 && apt-get remove --purge -y \
 	 software-properties-common \
	 ca-certificates \
 && apt-get autoremove -y \
 && rm -rf /var/lib/apt/lists/* \
 && rm /etc/apt/sources.list.d/llvm.list

ENV CC=clang
ENV CXX=clang++