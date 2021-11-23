FROM zookeeper

# Mirror
RUN sed -i 's/deb.debian.org/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
RUN sed -i 's|security.debian.org/debian-security|mirrors.ustc.edu.cn/debian-security|g' /etc/apt/sources.list

# Basic system stuff
RUN apt-get update
RUN apt-get install -y apt-transport-https openssh-server

# Config Jepsen
COPY setup-jepsen.sh /usr/local/bin/setup-jepsen
RUN chmod +x /usr/local/bin/setup-jepsen

# Configure SSHD
RUN sed -i "s/#PermitRootLogin prohibit-password/PermitRootLogin yes/g" /etc/ssh/sshd_config


# Install Jepsen deps
RUN apt-get install -qy build-essential bzip2 curl dnsutils faketime iproute2 iptables iputils-ping libzip4 logrotate man man-db net-tools ntpdate psmisc python rsyslog sudo tar unzip vim wget ca-certificates

EXPOSE 2188 2888 3888

CMD sed -i "s/2181/2188/g" /conf/zoo.cfg && \
    mkdir -p /run/sshd && \
    /usr/local/bin/setup-jepsen && \
    /usr/sbin/sshd && \
    zkServer.sh start-foreground
