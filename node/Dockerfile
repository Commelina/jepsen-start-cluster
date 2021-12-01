FROM hstreamdb/hstream

ARG USE_CHINA_MIRROR

# Mirror
RUN if [ -n "$USE_CHINA_MIRROR" ] ; then sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list ; fi

# Basic system stuff
RUN apt-get update
RUN apt-get install -y apt-transport-https

# Install packages
RUN apt-get -qy update && \
    apt-get -qy install \
        dos2unix \
        openssh-server \
        pwgen

# Config Jepsen
COPY setup-jepsen.sh /usr/local/bin/setup-jepsen
RUN chmod +x /usr/local/bin/setup-jepsen

# Configure SSHD
RUN sed -i "s/#PermitRootLogin prohibit-password/PermitRootLogin yes/g" /etc/ssh/sshd_config

# Install Jepsen deps
RUN apt-get install -qy build-essential bzip2 curl dnsutils faketime iproute2 iptables iputils-ping logrotate man man-db net-tools ntpdate psmisc python rsyslog sudo tar unzip vim wget ca-certificates

EXPOSE 22 6570 6571

CMD mkdir -p /run/sshd && \
    cp /data/store/logdevice.conf /root/logdevice.conf && \
    sed -i "s/127.0.0.1/logdevice/g" /root/logdevice.conf && \
    /usr/local/bin/setup-jepsen && \
    /usr/sbin/sshd && \
    SERVER_ID=$(shuf -i 1-4294967296 -n 1) && \
    MY_IP=$(hostname -I) && \
    hstream-server --host 0.0.0.0 --port 6570 --address $MY_IP --store-config /root/logdevice.conf --zkuri "zookeeper:2188" --server-id $SERVER_ID
