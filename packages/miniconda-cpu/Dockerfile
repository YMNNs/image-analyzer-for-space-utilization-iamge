FROM continuumio/miniconda3
LABEL authors="csu"

ARG ZIP_NAME=app.zip
ENV PYTHONUNBUFFERED=1
ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /app

COPY $ZIP_NAME .

# 用 && 连接多条命令可以减小镜像体积
# 安装工具，解压文件，创建环境，执行安装脚本，清理缓存
RUN apt update \
    && apt install curl unzip -y \
    && unzip $ZIP_NAME -d .\
    && rm -rf $ZIP_NAME \
    && sed -i '/^prefix:/d' environment.yml  \
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