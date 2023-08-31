#
#  Copyright (c) 2017-2023, Intel Corporation
#
#  SPDX-License-Identifier: BSD-3-Clause

FROM fedora:39
LABEL maintainer="Nurmukhametov, Aleksei <aleksei.nurmukhametov@intel.com>"
SHELL ["/bin/bash", "-c"]

ARG REPO=ispc/ispc
ARG SHA=main

# If you are behind a proxy, you need to configure git to use it.
RUN if [ -v http_proxy ]; then echo "proxy=$http_proxy" >> /etc/dnf/dnf.conf; fi

# Packages required to build ISPC.
RUN dnf install -y git cmake python3-setuptools && \
    dnf install -y clang-devel-16.0.6 llvm-devel-16.0.6 glibc-devel.i686 && \
    dnf install -y flex bison tbb-devel && \
    dnf clean -y all

WORKDIR /usr/local/src

# If you are behind a proxy, you need to configure dnf to use it.
RUN if [ -v http_proxy ]; then git config --global --add http.proxy "$http_proxy"; fi

# Fork ispc on github and clone *your* fork.
RUN git clone --depth=1 https://github.com/$REPO.git ispc && cd ispc && git checkout $SHA

# If you are going to run test for future platforms, go to
# http://www.intel.com/software/sde and download the latest version,
# extract it, add to path and set SDE_HOME.

# LLVM
# We don't build llvm here because we use system shared libraries.

# Configure and build ISPC
RUN cmake ispc -B build-ispc -DCMAKE_INSTALL_PREFIX=/usr && \
    cmake --build build-ispc -v -j "$(nproc)" && \
    cmake --build build-ispc --target check-all && \
    (echo "shared libs deps of ispc" && ldd ./bin/ispc) && \
    cmake --install build-ispc && \
    rm -rf build
