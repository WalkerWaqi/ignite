ARG RELEASE
FROM DOCKERARCH/ubuntu:${RELEASE}

# If we're building for another architecture than amd64, this let's us emulate an other platform's docker build.
# If we're building normally, for amd64, this line is removed
COPY qemu-QEMUARCH-static /usr/bin/

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# udev is needed for booting a "real" VM, setting up the ttyS0 console properly
# kmod is needed for modprobing modules
# systemd is needed for running as PID 1 as /sbin/init
# Also, other utilities are installed
RUN sed -i "s/archive.ubuntu.com/mirrors.163.com/g" /etc/apt/sources.list
RUN apt-get update && apt-get install -y \
      git \
      git-lfs \
      curl \
      dbus \
      kmod \
      iproute2 \
      iputils-ping \
      net-tools \
      openssh-server \
      rng-tools \
      sudo \
      systemd \
      udev \
      vim-tiny \
      wget \
      zip \
      unzip \
      docker \
      docker-compose && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Create the following files, but unset them
RUN echo "" > /etc/machine-id && echo "" > /var/lib/dbus/machine-id

# This container image doesn't have locales installed. Disable forwarding the
# user locale env variables or we get warnings such as:
#  bash: warning: setlocale: LC_ALL: cannot change locale
RUN sed -i -e 's/^AcceptEnv LANG LC_\*$/#AcceptEnv LANG LC_*/' \
           -e 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' \
           -e 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config

RUN echo '{\n\
  "registry-mirrors": [\n\
    "https://9cpn8tt6.mirror.aliyuncs.com",\n\
    "http://docker.mirrors.ustc.edu.cn",\n\
    "http://hub-mirror.c.163.com"\n\
  ],\n\
  "insecure-registries": [\n\
    "registry.docker-cn.com",\n\
    "docker.mirrors.ustc.edu.cn",\n\
    "registry.tro:15000",\n\
    "172.27.120.202:15000"\n\
  ],\n\
  "debug": true,\n\
  "experimental": true\n\
}'\
>/etc/docker/daemon.json

RUN echo 'https_proxy=172.27.128.247:10809\n\
http_proxy=172.27.128.247:10809'\
>/opt/proxy.env

# Set the root password to root when logging in through the VM's ttyS0 console
RUN echo "root:123456" | chpasswd
