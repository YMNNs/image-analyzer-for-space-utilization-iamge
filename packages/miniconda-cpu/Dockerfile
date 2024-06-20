FROM continuumio/miniconda3
LABEL authors="csu"

ARG ZIP_NAME=app.zip

WORKDIR /app

# 安装工具
RUN apt update && apt install curl unzip -y

# 复制并解压文件
COPY $ZIP_NAME .
RUN unzip $ZIP_NAME -d . && rm -f $ZIP_NAME

# 添加镜像源
RUN conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main \
    && conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r \
    && conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2 \
    && conda update -y conda

# 创建环境
RUN sed -i '/^prefix:/d' environment.yml && conda env create -f environment.yml -n app_env

# 暴露端口
EXPOSE 5000

# 挂载数据卷
VOLUME ["/input_data", "/output_data"]

# 检查健康状况
HEALTHCHECK --interval=10s --timeout=3s \
    CMD curl -fs http://localhost:5000/date || exit 1

# 创建入口
CMD ["conda", "run", "-n", "app_env", "python", "app.py"]