# 安装maven
FROM maven:3.3.9-jdk-8

#设置时区
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo "Asia/Shanghai" > /etc/timezone
RUN dpkg-reconfigure -f noninteractive tzdata

ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH
RUN mkdir -p "$CATALINA_HOME"
WORKDIR $CATALINA_HOME

# 安装tomcat
ENV TOMCAT_MAJOR 8
ENV TOMCAT_VERSION 8.5.9
ENV TOMCAT_TGZ_URL http://mirrors.aliyun.com/apache/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz

RUN set -x \
        && curl -fSL "$TOMCAT_TGZ_URL" -o tomcat.tar.gz \
        && tar -xvf tomcat.tar.gz --strip-components=1 \
        && rm bin/*.bat \
        && rm -rf webapps/* \
        && rm tomcat.tar.gz*

# china mirrors
ADD sources.list /etc/apt/sources.list
ADD settings.xml /usr/share/maven/ref/

RUN \
  apt-get update \

  # 安装 FFmpeg (source code make)
  && apt-get install -y yasm pkg-config make gcc \
  # && curl -fSL "https://codeload.github.com/FFmpeg/FFmpeg/zip/master" -o FFmpeg.zip \
  && curl http://o8svz33ym.bkt.clouddn.com/FFmpeg.zip -o FFmpeg.zip && unzip FFmpeg.zip \
  && cd FFmpeg && ./configure && make && make install \
  && cd ../ && rm -rf FFmpeg FFmpeg.zip \

  # 安装 unoconv (office to pdf)
  && apt-get install -y unoconv \

  # 安装微软雅黑字体
  && curl -O http://o8svz33ym.bkt.clouddn.com/msyh.ttf \
  && cp msyh.ttf /usr/share/fonts/truetype/msyh.ttf \
  && fc-cache -fv \

  # clear
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/ 

# 拷贝部署脚本到镜像
ADD build.sh /tmp/

# 允许挂载 Tomcat 日志目录
VOLUME /usr/local/tomcat/logs

# 允许挂载源码目录
VOLUME /tmp/code

EXPOSE 8080
