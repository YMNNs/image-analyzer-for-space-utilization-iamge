FROM nvidia/cuda:12.0.0-cudnn8-runtime-ubuntu20.04
LABEL authors="csu"

ARG ZIP_NAME=app.zip

WORKDIR /opt

# 安装工具
RUN apt update && apt install curl unzip -y

# 安装miniconda
RUN curl -o miniconda.sh https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && bash miniconda.sh -b -p /opt/miniconda \
    && rm -f miniconda.sh

WORKDIR /app

# 复制并解压文件
COPY $ZIP_NAME .
RUN unzip $ZIP_NAME -d . && rm -f $ZIP_NAME

# 添加镜像源
ENV PATH=/opt/miniconda/bin:${PATH}
RUN conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main \
    && conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r \
    && conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2 \
    && conda update -y conda \
    && conda init

# 创建环境
RUN sed -i '/^prefix:/d' environment.yml && conda env create -f environment.yml -n app_env

# 暴露端口
EXPOSE 5000

# 挂载数据卷
VOLUME ["/data/input", "/data/output"]

# 检查健康状况
HEALTHCHECK --interval=10s --timeout=3s \
    CMD curl -fs http://localhost:5000/date || exit 1

# 创建入口
CMD ["conda", "run", "-n", "app_env", "python", "app.py"]

