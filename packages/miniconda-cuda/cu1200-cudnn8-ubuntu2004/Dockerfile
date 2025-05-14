FROM nvidia/cuda:12.0.0-cudnn8-runtime-ubuntu20.04
LABEL authors="csu"

ARG ZIP_NAME=app.zip
ENV PYTHONUNBUFFERED=1
ENV DEBIAN_FRONTEND=noninteractive
ENV PATH=/opt/conda/bin:${PATH}

WORKDIR /app

COPY $ZIP_NAME .

# 用 && 连接多条命令可以减小镜像体积
# 安装工具，解压文件，安装miniconda，创建环境，执行安装脚本，清理缓存
RUN apt update \
    && apt install curl unzip -y \
    && unzip $ZIP_NAME -d .  \
    && rm -f $ZIP_NAME \
    && curl -o miniconda.sh https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && bash miniconda.sh -b -p /opt/conda \
    && rm -f miniconda.sh \
    && conda init \
    && sed -i '/^prefix:/d' environment.yml \
    && conda env create -f environment.yml -n app_env \
    && if [ -e ./setup.sh ]; then chmod +x setup.sh && ./setup.sh; fi \
    && apt clean \
    && apt autoclean \
    && conda clean --all -y \
    && conda run -n app_env pip cache purge

# 暴露端口
EXPOSE 5000

# 挂载数据卷
VOLUME ["/log", "/data/input", "/data/output"]

# 创建入口
CMD ["conda", "run", "--no-capture-output", "-n", "app_env", "python", "app.py"]

