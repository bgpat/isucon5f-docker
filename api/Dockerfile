FROM ubuntu:xenial

RUN apt-get update && \
    apt-get install -y sudo git gcc patch autoconf automake bison \
        build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev \
        libncurses5-dev libffi-dev libgdbm3 libgdbm-dev libmysqlclient-dev \
        libcurl4-openssl-dev libjpeg-turbo8 libjpeg-turbo8-dev \
        libpng12-0 libpng12-dev libmcrypt4 libmcrypt-dev libtidy-0.99-0 \
        libtidy-dev libxml2-dev libxslt1-dev readline-common re2c curl \
        pkg-config software-properties-common supervisor && \
    add-apt-repository ppa:webupd8team/java && \
    add-apt-repository ppa:cwchien/gradle && \
    echo 'deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main' > /etc/apt/sources.list.d/pgdg.list && \
    echo 'oracle-java8-installer shared/accepted-oracle-license-v1-1 boolean true' | debconf-set-selections && \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y oracle-java8-installer \
        ca-certificates gradle-2.10 nginx && \
    cp -p /usr/share/zoneinfo/Japan /etc/localtime

RUN git clone --depth 1 https://github.com/tagomoris/xbuild.git /home/isucon/.xbuild && \
    /home/isucon/.xbuild/ruby-install 2.2.3 /home/isucon/.local/ruby && \
    /home/isucon/.xbuild/node-install v4.1.1 /home/isucon/.local/node && \
    /home/isucon/.xbuild/python-install 3.4.3 /home/isucon/.local/python3 && \
    /home/isucon/.xbuild/go-install 1.5 /home/isucon/.local/go

RUN useradd -d /home/isucon -s /bin/bash isucon -G sudo && chown isucon:isucon -R /home/isucon/ && \
    sudo -u isucon git clone --depth 1 https://github.com/isucon/isucon5-final.git /home/isucon/isucon5-final && \
    sudo -u isucon cp /home/isucon/isucon5-final/provisioning/api/files/env.sh /home/isucon/env.sh && chmod 755 /home/isucon/env.sh && \
    cd /home/isucon/isucon5-final/api/ken && PATH=/home/isucon/.local/ruby/bin:$PATH bundle install && \
    cd /home/isucon/isucon5-final/api/perfect_security && PATH=/home/isucon/.local/node/bin:$PATH npm install && \
    cd /home/isucon/isucon5-final/api/search-name && sudo -u isucon gradle wrapper && \
    cd /home/isucon/isucon5-final/api/go/tenki && PATH=/home/isucon/.local/go/bin:$PATH GOROOT=/home/isucon/.local/go GOPATH=/home/isucon/isucon5-final/api/go go build && \
    cp /home/isucon/isucon5-final/provisioning/api/files/supervisor.ken.conf /etc/supervisor/conf.d/ken.conf && \
    cp /home/isucon/isucon5-final/provisioning/api/files/supervisor.perfect_security.conf /etc/supervisor/conf.d/perfect_security.conf && \
    cp /home/isucon/isucon5-final/provisioning/api/files/supervisor.search-name.conf /etc/supervisor/conf.d/search-name.conf && \
    cp /home/isucon/isucon5-final/provisioning/api/files/supervisor.tenki.conf /etc/supervisor/conf.d/tenki.conf

EXPOSE 8080 8081 8988 8443 8082

CMD supervisord -c /etc/supervisor/supervisord.conf; service nginx start; tail -f /dev/null
