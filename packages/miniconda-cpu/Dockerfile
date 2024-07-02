FROM continuumio/miniconda3
LABEL authors="csu"

ARG ZIP_NAME=app.zip
ENV PYTHONUNBUFFERED=1

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

# 执行用户自定义的脚本
RUN if [ -e ./setup.sh ]; then chmod +x setup.sh && ./setup.sh; fi

# 暴露端口
EXPOSE 5000

# 挂载数据卷
VOLUME ["/log", "/data/input", "/data/output"]

# 检查健康状况
HEALTHCHECK --interval=10s --timeout=3s \
    CMD curl -fs http://localhost:5000/date || exit 1

# 创建入口
CMD ["conda", "run", "--no-capture-output", "-n", "app_env", "python", "app.py"]