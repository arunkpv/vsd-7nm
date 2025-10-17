# syntax=docker/dockerfile:1
FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

SHELL ["/bin/bash", "-lc"]
ENV DEBIAN_FRONTEND=noninteractive
ENV DISPLAY=:1

# -----------------------------
# System & build dependencies
# -----------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
    git build-essential autoconf automake libtool m4 \
    tcl tcl-dev tk tk-dev libcairo2-dev \
    libx11-dev libxext-dev libxrender-dev libxpm-dev libxaw7-dev \
    libreadline-dev libncurses-dev flex bison gawk tcsh \
    python3 python3-pip pkg-config curl ca-certificates \
    xfce4 xfce4-terminal x11vnc xvfb novnc websockify supervisor dbus-x11 \
    libgoogle-glog-dev libgflags-dev gedit vim \
    clang lld libffi-dev libboost-system-dev libboost-filesystem-dev \
    libboost-python-dev libfl-dev zlib1g-dev graphviz xdot \
    libopenblas-dev libomp-dev libfftw3-dev \
 && apt-get clean && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------
# Install XSchem (schematic capture tool)
# ------------------------------------------------------------
RUN git clone https://github.com/StefanSchippers/xschem.git /opt/xschem && \
    cd /opt/xschem && \
    ./configure && \
    make -j$(nproc) && \
    make install && \
    cd / && rm -rf /opt/xschem

# ------------------------------------------------------------
# Install Ngspice (with XSPICE, OSDI, OpenMP, CIDER)
# ------------------------------------------------------------
RUN git clone https://git.code.sf.net/p/ngspice/ngspice /opt/ngspice_git && \
    cd /opt/ngspice_git && \
    ./autogen.sh && \
    mkdir release && cd release && \
    ../configure --with-x --enable-xspice --disable-debug --enable-cider \
                 --with-readline=yes --enable-openmp --enable-osdi && \
    make -j$(nproc) && \
    make install && \
    cd / && rm -rf /opt/ngspice_git

# -----------------------------
# noVNC desktop (supervisor)
# -----------------------------
# With context "..", this file is available at .devcontainer/supervisord.conf
COPY supervisord.conf /etc/supervisor/conf.d/vnc.conf

EXPOSE 6080
CMD ["/usr/bin/supervisord", "-n"]

WORKDIR /workspaces/vsd-tcl
