FROM nginx:1.17.2

RUN sed -i 's/deb.debian.org/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
RUN apt-get update

RUN apt-get install vim -y

RUN apt-get install telnet -y