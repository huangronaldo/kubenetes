# Docker image for springboot file run
# VERSION 0.0.1
# Author: huangt
# 基础镜像使用java
FROM java:8
# 作者
MAINTAINER huangt <huangronaldo@qq.com>
#容器中创建目录
RUN mkdir -p /app/test
# VOLUME 指定了临时文件目录为/app/test。
# 其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/app/test
VOLUME /app/test
# 将jar包添加到容器中并更名为app.jar
ADD dcoffice-management-1.0.2.jar /app/test/app.jar 
ADD application.yml /app/test/application.yml
# ADD entrypoint.sh /app/test/entrypoint.sh

# 指定容器启动时要执行的命令
# ENTRYPOINT ["sh", "/app/test/entrypoint.sh"]
ENTRYPOINT ["nohup", "java", "-jar", "/app/test/app.jar", "&"]
