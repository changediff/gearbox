FROM rockylinux:8
ENV TZ="Asia/Shanghai"
ENV LANG="en_US.UTF-8"
# Update repo
RUN sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=http://dl.rockylinux.org/$contentdir|baseurl=https://mirrors.ustc.edu.cn/rocky|g' \
    -i.bak \
    /etc/yum.repos.d/Rocky-AppStream.repo \
    /etc/yum.repos.d/Rocky-BaseOS.repo \
    /etc/yum.repos.d/Rocky-Extras.repo \
    /etc/yum.repos.d/Rocky-PowerTools.repo
RUN dnf update -y && dnf clean all
RUN dnf install -y dnf-plugins-core && \
    dnf config-manager --set-enabled powertools && \
    dnf install -y epel-release && \
    rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org && \
    dnf install -y https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm && \
    dnf install -y --nogpgcheck https://mirrors.rpmfusion.org/free/el/rpmfusion-free-release-$(rpm -E %rhel).noarch.rpm https://mirrors.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-$(rpm -E %rhel).noarch.rpm && \
    dnf clean all
ADD ./conf/mongodb-org.repo /etc/yum.repos.d/mongodb-org.repo
# Install Tools
#### DBs
RUN dnf install -y mysql redis && \
    dnf clean all
#### Net Tools
RUN dnf install -y nmap-ncat nc telnet iputils bind-utils telnet traceroute tcpdump iproute mtr && \
    dnf clean all
#### Benchmark
RUN dnf install -y siege stress-ng fio bc hdparm dmidecode && \
    dnf install -y https://www.iozone.org/src/current/iozone-3-506.x86_64.rpm && \
    dnf clean all && \
    echo "export PATH=/opt/iozone/bin/:\$PATH" > /etc/profile.d/iozone.sh
#### Common Bash Tools
RUN dnf install -y moreutils tmux vim wget curl jq rsync procps-ng findutils openssh-server openssh-clients && \
    dnf clean all
RUN dnf install -y fastfetch && \
    dnf clean all
#### Code Tools
RUN dnf install -y python3 python3-pip vim git && \
    dnf clean all
#### Bin
RUN mkdir -p /opt/bin && chmod -R 777 /opt/bin
ENV PATH=/opt/bin:$PATH
# Set Workdir
RUN mkdir -p /data
WORKDIR /data
# Add Tini
ENV TINI_VERSION v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini
ENTRYPOINT ["/tini", "--"]
CMD ["sleep", "infinity"]
