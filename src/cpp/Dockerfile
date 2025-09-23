FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

USER root
SHELL [ "/bin/bash", "-c" ]

ARG GCC_VERSION=14
ARG LLVM_VERSION=20

ADD llvm-snapshot.gpg.key.asc /etc/apt/trusted.gpg.d/llvm-snapshot.asc
RUN export DEBIAN_FRONTEND=noninteractive; \
    echo "deb http://apt.llvm.org/$(lsb_release -cs)/ llvm-toolchain-$(lsb_release -cs)-${LLVM_VERSION} main" >> /etc/apt/sources.list \
    && apt-get -qq update && apt-get -qq install --no-install-recommends \
    bash-completion \
    build-essential \
    clang-${LLVM_VERSION} \
    clang-format-${LLVM_VERSION} \
    clang-tidy-${LLVM_VERSION} \
    clang++-${LLVM_VERSION} \
    clangd-${LLVM_VERSION} \
    curl \
    g++-${GCC_VERSION} \
    gcc-${GCC_VERSION} \
    gdb \
    git \
    golang \
    lldb-dap-${LLVM_VERSION} \
    nasm \
    ninja-build \
    pipx \
    pkg-config \
    python3 \
    python3-dev \
    python3-doc \
    python3-pip \
    python3-tk \
    python3-venv \
    tar \
    unzip \
    zip \
    zsh \
    && apt-get -qq autoremove \
    && apt-get -qq clean \
    && rm -rf /var/lib/apt/lists/* \
    && update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-${GCC_VERSION} ${GCC_VERSION}0 --slave /usr/bin/g++ g++ /usr/bin/g++-${GCC_VERSION} \
    && update-alternatives --install /usr/bin/clang clang /usr/bin/clang-${LLVM_VERSION} ${LLVM_VERSION}0 --slave /usr/bin/clang++ clang++ /usr/bin/clang++-${LLVM_VERSION} --slave /usr/bin/clangd clangd /usr/bin/clangd-${LLVM_VERSION} --slave /usr/bin/lldb-dap lldb-dap /usr/bin/lldb-dap-${LLVM_VERSION}

ARG CMAKE_VERSION="3.31.7"
RUN declare -A archs=( ["amd64"]="x86_64" ["arm64"]="aarch64" ); \
    architecture=$(dpkg --print-architecture); \
    echo "Detected arch: $architecture => ${archs[$architecture]}"; \
    [[ "${archs[$architecture]}" != "" ]] \
    && mkdir -p /opt/cmake \
    && CMAKE_BINARY_NAME="cmake-$CMAKE_VERSION-linux-${archs[$architecture]}.sh" \
    && TMP_DIR=$(mktemp -d -t cmake-XXXXXXXXXX) \
    && pushd "$TMP_DIR" \
    && curl -sSL "https://github.com/Kitware/CMake/releases/download/v$CMAKE_VERSION/$CMAKE_BINARY_NAME" -O \
    && sh "$TMP_DIR/$CMAKE_BINARY_NAME" --prefix=/opt/cmake --skip-license \
    && popd \
    && rm -rf "$TMP_DIR" \
    && ln -s -t /usr/local/bin /opt/cmake/bin/cmake /opt/cmake/bin/ccmake /opt/cmake/bin/ctest /opt/cmake/bin/cpack

# Setup ENV vars for vcpkg
ENV VCPKG_ROOT=/usr/local/vcpkg \
    VCPKG_NO_CI=1
RUN mkdir -p "$VCPKG_ROOT" \
    && chown vscode:vscode "$VCPKG_ROOT" \
    && su vscode -c "git clone -c core.eol=lf -c core.autocrlf=false -c fsck.zeroPaddedFilemode=ignore -c fetch.fsck.zeroPaddedFilemode=ignore -c receive.fsck.zeroPaddedFilemode=ignore https://github.com/microsoft/vcpkg $VCPKG_ROOT" \
    && VCPKG_DOWNLOADS="$HOME/.cache/vcpkg/downloads" \
    && sh "$VCPKG_ROOT/bootstrap-vcpkg.sh" \
    && rm -rf "$VCPKG_DOWNLOADS" \
    && ln -s "$VCPKG_ROOT/vcpkg" "/usr/local/bin/vcpkg" \
    && echo 'export VCPKG_DOWNLOADS="$HOME/.cache/vcpkg/downloads"' >> ~vscode/.zshenv \
    && echo 'export VCPKG_DOWNLOADS="$HOME/.cache/vcpkg/downloads"' >> ~vscode/.bash_profile

USER vscode
