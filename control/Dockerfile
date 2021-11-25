FROM debian:buster
MAINTAINER jake@apache.org

ENV LEIN_ROOT true

########
# MIRROR
RUN sed -i 's/deb.debian.org/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
RUN sed -i 's|security.debian.org/debian-security|mirrors.ustc.edu.cn/debian-security|g' /etc/apt/sources.list
########

#
# Jepsen dependencies
#
RUN apt-get -y -q update && \
    apt-get install -qy openjdk-11-jdk-headless \
    libjna-java \
    git \
    pssh \
    curl \
    wget \
    dos2unix \
    gnuplot \
    zookeeper

COPY lein /usr/bin/lein
COPY leiningen-2.9.7-standalone.jar /root/.lein/self-installs/leiningen-2.9.7-standalone.jar
RUN chmod +x /usr/bin/lein

# without --dev flag up.sh copies jepsen to these subfolders
# with --dev flag they are empty until mounted
# RUN if [ -f /jepsen/jepsen/project.clj ]; then cd /jepsen/jepsen && lein install; fi
# COPY jepsen /jepsen/

ADD ./bashrc /root/.bashrc
ADD ./init.sh /init.sh
RUN dos2unix /init.sh /root/.bashrc \
    && chmod +x /init.sh

CMD /init.sh
