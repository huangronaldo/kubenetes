# Docker image for springboot
# VERSION 1.0.0
# Author: huangt
# 基础镜像使用java
FROM openjdk:8-jdk-alpine
# 作者
MAINTAINER huangt <huangronaldo@qq.com>

ENV TZ=Asia/Shanghai
RUN ln -snf /usr/sfhare/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
#容器中创建目录
RUN mkdir -p /app/test
# VOLUME 指定了临时文件目录为/app/test。
# 其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/app/test
VOLUME /app/test
# 将jar包添加到容器中并更名为app.jar
ADD dcoffice-management-1.0.2.jar /app/test/app.jar 
ADD application.yml /app/test/application.yml

# 指定容器启动时要执行的命令
# ENTRYPOINT ["sh", "/app/test/entrypoint.sh"]
ENTRYPOINT ["nohup", "java","-Djava.security.egd=file:/dev/./urandom -Duser.timezone=GMT+8", "-jar", "/app/test/app.jar", "&"]
