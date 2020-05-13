FROM rushlang/clang-x86-64-linux-gnu
LABEL maintainer="Reegan Layzell <reegan.layzell@rush-lang.org>"
LABEL description="Builder image for building LLVM with Clang and libc++"

ARG llvmver_major=10
ARG llvmver_minor=0
ARG llvmver_patch=0

ENV LLVM_VER_MAJOR ${llvmver_major}
ENV LLVM_VER_MINOR ${llvmver_minor}
ENV LLVM_VER_PATCH ${llvmver_patch}
ENV LLVM_VER ${LLVM_VER_MAJOR}.${LLVM_VER_MINOR}.${LLVM_VER_PATCH}

ARG source_dir=/src/llvm
ARG install_dir=/opt/llvm
ARG build_deps="xz-utils ca-certificates"

# Copy CMake build from another image
COPY --from=rushlang/cmake-x86-64-linux-gnu /opt/cmake /opt/cmake
RUN update-alternatives --install /usr/bin/cmake cmake /opt/cmake/bin/cmake 100 \
 && update-alternatives --install /usr/bin/ctest ctest /opt/cmake/bin/ctest 100

# Download LLVM release source.
WORKDIR ${source_dir}
ADD https://github.com/llvm/llvm-project/releases/download/llvmorg-${LLVM_VER}/llvm-${LLVM_VER}.src.tar.xz .
RUN tar xf llvm-${LLVM_VER}.src.tar.xz \
 && rm llvm-${LLVM_VER}.src.tar.xz \
 && cd llvm-${LLVM_VER}.src

# Build and install LLVM from source.
WORKDIR ${source_dir}/llvm-${LLVM_VER}.src
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
 	 ${build_deps} \
 && mkdir build && cd build \
 && cmake ../ \
	-DCMAKE_C_COMPILER=clang \
	-DCMAKE_CXX_COMPILER=clang++ \
	-DCMAKE_BUILD_TYPE:STRING=MinSizeRel \
	-DCMAKE_INSTALL_PREFIX=${install_dir} \
	-DLLVM_INCLUDE_TOOLS:BOOL=OFF \
	-DLLVM_INCLUDE_TESTS:BOOL=OFF \
	-DLLVM_INCLUDE_BENCHMARKS:BOOL=OFF \
	-DLLVM_INCLUDE_EXAMPLES:BOOL=OFF \
	-DLLVM_ENABLE_LIBCXX:BOOL=TRUE \
	-DLLVM_ENABLE_PROJECTS:STRING="" \
	-DLLVM_TARGETS_TO_BUILD:STRING="X86;ARM" \
 && cmake --build . -j 8 \
 && cmake --build . -j 8 --target install

# Clean up source.
RUN rm -rf ${source_dir}

# Cleanup build dependencies and apt cache.
RUN apt-get remove -y ${build_deps} \
 && apt-get autoremove -y \
 && rm -rf /var/lib/apt/lists/*