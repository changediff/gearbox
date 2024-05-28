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
#### Rpm builder and Fpm
RUN dnf -y install fedora-packager rpm-build ruby rubygems ruby-devel && \
    dnf install -y make automake gcc gcc-c++ kernel-devel && \
    dnf clean all
RUN gem sources -a https://mirrors.ustc.edu.cn/rubygems/ && \
    gem sources --remove https://rubygems.org/
RUN gem install dotenv -v 2.8.1 -N
RUN gem install clamp -v 1.0.1 -N
RUN gem install mustache -v 0.99.8 -N
RUN gem install cabin -N
RUN gem install insist -N
RUN gem install stud -N
RUN gem install arr-pm -N
RUN gem install backports -N
RUN gem install rexml -N
RUN gem install pleaserun --ignore-dependencies -N
RUN gem install fpm --ignore-dependencies -N
#### DBs
RUN dnf install -y mysql redis mongodb-org && \
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
RUN dnf install -y python3 python3-pip vim git golang && \
    dnf clean all
#### Bin
WORKDIR /usr/bin
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256" && \
    echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check && \
    chmod +x kubectl && \
    curl -Ss https://dl.k8s.io/release/stable.txt > kubectl.version.txt
WORKDIR /tmp
RUN curl -LO https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-client-linux-amd64-rhel8.tar.gz && \
    tar zxvf openshift-client-linux-amd64-rhel8.tar.gz && \
    /usr/bin/cp /tmp/oc /usr/local/bin/oc && \
    rm -rf /tmp/*
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
