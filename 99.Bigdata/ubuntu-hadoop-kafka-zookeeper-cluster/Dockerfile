FROM ubuntu-hadoop-base AS ubuntu-hadoop-base

# 设置阿里云源，并设置语言、时区，安装基本工具
# RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list 

# update apt-get
RUN apt-get update \
    && rm -rf /var/lib/apt/lists/*

# kafka environment
ENV KAFKA_HOME /usr/local/kafka
ENV PATH $PATH:$KAFKA_HOME/bin

# zookeeper environment
ENV ZOOKEEPER_HOME /usr/local/zookeeper
ENV PATH $PATH:$ZOOKEEPER_HOME/bin

# flume environment
# ENV FLUME_HOME /usr/local/flume
# ENV PATH $PATH:$FLUME_HOME/bin

# software install path
ENV USR_LOCAL_PATH /usr/local

# install software
# install kafka
ADD ./src/software/kafka_2.11-1.1.0.tgz ${USR_LOCAL_PATH}
RUN sudo mv ${USR_LOCAL_PATH}/kafka_2.11-1.1.0 ${USR_LOCAL_PATH}/kafka
# install zookeeper
ADD ./src/software/zookeeper-3.4.13.tar.gz ${USR_LOCAL_PATH}
RUN sudo mv ${USR_LOCAL_PATH}/zookeeper-3.4.13 ${USR_LOCAL_PATH}/zookeeper
# install flume
# ADD ./src/software/apache-flume-1.8.0-bin.tar.gz ${USR_LOCAL_PATH}
# RUN sudo mv ${USR_LOCAL_PATH}/apache-flume-1.8.0-bin ${USR_LOCAL_PATH}/flume

# copy zookeeper configuration
ADD ./conf/zookeeper/* ${ZOOKEEPER_HOME}/conf/
# create zookeeper data directory
RUN mkdir /home/hadoop/zookeeper
RUN mkdir /home/hadoop/zookeeper/data
# RUN mkdir /home/hadoop/zookeeper/log
RUN echo "1" > /home/hadoop/zookeeper/data/myid
# permission zookeeper data dir
RUN sudo chown -R hadoop:hadoop /home/hadoop/zookeeper

# copy kafka configuration
ADD ./conf/kafka/* ${KAFKA_HOME}/config/
# create kafka log directory
RUN mkdir /home/hadoop/kafka
RUN mkdir /home/hadoop/kafka/logs
# permission kafka data dir
RUN sudo chown -R hadoop:hadoop /home/hadoop/kafka

# permission kafka dir
RUN sudo chown -R hadoop:hadoop ${KAFKA_HOME}
# permission zookeeper dir
RUN sudo chown -R hadoop:hadoop ${ZOOKEEPER_HOME}

# environment setting for hadoop user
RUN echo "export PATH=$PATH">>/home/hadoop/.bashrc

USER root
WORKDIR /root