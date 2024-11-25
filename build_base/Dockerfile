# 使用 Ubuntu 22.04 作为基础镜像
FROM ubuntu:22.04

# 设置时区，避免交互提示
ENV DEBIAN_FRONTEND=noninteractive

# 替换为国内 APT 源并安装基础依赖
RUN sed -i 's|http://archive.ubuntu.com|http://mirrors.aliyun.com|g' /etc/apt/sources.list && \
    sed -i 's|http://security.ubuntu.com|http://mirrors.aliyun.com|g' /etc/apt/sources.list && \
    apt-get update && apt-get upgrade -y && apt-get install -y --no-install-recommends \
    wget \
    curl \
    ca-certificates \
    software-properties-common \
    gnupg \
    make \
    g++ \
    gcc \
    openssh-client \
    linux-tools-generic \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# 添加 LLVM 官方仓库并安装最新版本的 clang-format
RUN wget https://apt.llvm.org/llvm.sh && \
    chmod +x llvm.sh && \
    ./llvm.sh 15 && \
    rm llvm.sh && \
    apt-get install -y clang-format-15 && \
    ln -s /usr/bin/clang-format-15 /usr/bin/clang-format

# 下载并安装 Miniconda 到 ~/miniconda3
RUN mkdir -p ~/miniconda3 && \
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh && \
    bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3 && \
    rm ~/miniconda3/miniconda.sh && \
    ~/miniconda3/bin/conda init --all

# 设置 Conda 国内镜像源
RUN ~/miniconda3/bin/conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main && \
    ~/miniconda3/bin/conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free && \
    ~/miniconda3/bin/conda config --set show_channel_urls yes

# 设置环境变量以使用 conda
ENV PATH="/root/miniconda3/bin:$PATH"

# 安装 Conan 并配置 (适配 Conan 2.x)
RUN conda install conda-forge::conan -y && \
    mkdir -p ~/.conan2 && \
    echo "[proxies]" >> ~/.conan2/conan.conf && \
    echo "https=http://mirrors.aliyun.com" >> ~/.conan2/conan.conf && \
    conan config list && \
    conan --version

# 清理 Conda 缓存
RUN conda clean -afy

# 设置默认工作目录
WORKDIR /work
