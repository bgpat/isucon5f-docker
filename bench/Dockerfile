FROM ubuntu:xenial

RUN apt-get update && \
    apt-get install -y sudo git gcc patch autoconf automake bison \
        build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev \
        libncurses5-dev libffi-dev libgdbm3 libgdbm-dev libmysqlclient-dev \
        libcurl4-openssl-dev libjpeg-turbo8 libjpeg-turbo8-dev \
        libpng12-0 libpng12-dev libmcrypt4 libmcrypt-dev libtidy-0.99-0 \
        libtidy-dev libxml2-dev libxslt1-dev readline-common re2c curl \
        pkg-config software-properties-common supervisor openssh-server jq \
        language-pack-ja-base language-pack-ja && \
    add-apt-repository ppa:webupd8team/java && \
    add-apt-repository ppa:cwchien/gradle && \
    echo 'deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main' > /etc/apt/sources.list.d/pgdg.list && \
    echo 'oracle-java8-installer shared/accepted-oracle-license-v1-1 boolean true' | debconf-set-selections && \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y oracle-java8-installer \
        ca-certificates gradle-2.10 nginx && \
    cp -p /usr/share/zoneinfo/Japan /etc/localtime

RUN git clone --depth 1 https://github.com/tagomoris/xbuild.git /home/isucon/.xbuild && \
    /home/isucon/.xbuild/ruby-install 2.2.3 /home/isucon/.local/ruby

RUN useradd -d /home/isucon -s /bin/bash isucon -G sudo && chown isucon:isucon -R /home/isucon/ && echo isucon:isucon | chpasswd && \
    sudo -u isucon git clone --depth 1 https://github.com/bgpat/isucon5-final.git /home/isucon/isucon5-final && \
    sudo -u isucon cp /home/isucon/isucon5-final/provisioning/bench/files/env.sh /home/isucon/env.sh && chmod 755 /home/isucon/env.sh && \
    sudo -u isucon cp /home/isucon/isucon5-final/provisioning/bench/files/bashrc /home/isucon/.bashrc && \
    sudo -u isucon cp /home/isucon/isucon5-final/provisioning/bench/files/bench.sh /home/isucon/bench.sh && chmod 755 /home/isucon/bench.sh && \
    cd /home/isucon/isucon5-final/bench && PATH=/home/isucon/.local/ruby/bin:$PATH bundle install && \
    cd /home/isucon/isucon5-final/bench && sudo -u isucon gradle compileJava && \
    cp /home/isucon/isucon5-final/provisioning/bench/files/supervisor.*.conf /etc/supervisor/conf.d/

ENV LC_ALL ja_JP.UTF-8
ENV TERM xterm-256color

EXPOSE 22

CMD supervisord -c /etc/supervisor/supervisord.conf; service ssh start; tail -f /dev/null
