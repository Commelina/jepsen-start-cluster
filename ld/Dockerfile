FROM hstreamdb/hstream

# Mirror
RUN sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list

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

CMD mkdir -p /run/sshd && \
    /usr/local/bin/setup-jepsen && \
    /usr/sbin/sshd && \
    MY_IP=$(hostname -I) && \
    ld-dev-cluster --root /data/store --use-tcp --no-interactive --tcp-host $MY_IP
