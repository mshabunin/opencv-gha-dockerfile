# Version: 20241125
# Image name: quay.io/opencv-ci/opencv-ubuntu-24.04-llvm-19
# Command: 
#     docker build -f ubuntu-github-actions-llvm--24.04/Dockerfile -t quay.io/opencv-ci/opencv-ubuntu-24.04-llvm-19:20241125 .

FROM ubuntu:24.04 AS builder_llvm

ARG LLVM_TAG=llvmorg-19.1.4
ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && apt install -y \
    cmake \
    ccache \
    ninja-build \
    git \
    clang \
    libc++-dev \
    python3

# LLVM toolchain with libc++

# 📄 Clone repository
RUN git clone \
        -b ${LLVM_TAG} \
        --depth=1 \
        --recurse-submodules \
        --shallow-submodules \
        https://github.com/llvm/llvm-project
WORKDIR llvm-project

# 🛠 Build toolchain
RUN cmake \
    -S llvm \
    -B build \
    -G Ninja \
    -DCMAKE_INSTALL_PREFIX=/usr/local \
    -DCMAKE_CXX_COMPILER=clang++ \
    -DCMAKE_C_COMPILER=clang \
    -DLLVM_ENABLE_PROJECTS="clang;lld;compiler-rt" \
    -DLLVM_ENABLE_RUNTIMES="libcxx;libcxxabi;libunwind" \
    -DCOMPILER_RT_BUILD_SANITIZERS=ON \
    -DLIBCXX_HARDENING_MODE=debug \
    -DCMAKE_BUILD_TYPE=Release
    # -DLLVM_ENABLE_PROJECTS='clang;lld;compiler-rt' \
    # -DLLVM_ENABLE_RUNTIMES="libcxx;libcxxabi;libunwind" \

RUN cmake --build build --target install

#===============================================================================

FROM ubuntu:24.04
COPY --from=builder_llvm /usr/local /usr/local
ENV DEBIAN_FRONTEND=noninteractive

RUN \
  apt update && apt install -y \
    software-properties-common \
    cmake \
    ccache \
    ninja-build \
    gpg-agent \
    curl \
    git \
    htop \
    man \
    zip \
    unzip \
    vim \
    wget \
    ncdu \
    mc \
    nano \
    time \
    rsync \
    xvfb \
    libgtk-3-dev \
    pkg-config \
    file

RUN \
  add-apt-repository ppa:git-core/ppa && \
  apt update && apt install -y \
    git

USER ubuntu

RUN \
  mkdir -p /home/ubuntu/opencv /home/ubuntu/opencv_contrib /home/ubuntu/build /home/ubuntu/scripts && \
  git config --global --add safe.directory "*" && \
  git config --global user.email "opencv.ci" && \
  git config --global user.name "opencv.ci" && \
  git config --global pull.rebase false

COPY scripts/warnings-handling.py /home/ubuntu/scripts/warnings-handling.py

WORKDIR /home/ubuntu

CMD bash
