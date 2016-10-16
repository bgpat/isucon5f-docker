FROM ubuntu:xenial

RUN apt-get update && \
    apt-get install -y sudo git gcc patch autoconf automake bison \
        build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev \
        libncurses5-dev libffi-dev libgdbm3 libgdbm-dev libmysqlclient-dev \
        libpq-dev libcurl4-openssl-dev libjpeg-turbo8 libjpeg-turbo8-dev \
        libpng12-0 libpng12-dev libmcrypt4 libmcrypt-dev libtidy-0.99-0 \
        libtidy-dev libxml2-dev libxslt1-dev readline-common re2c curl \
        pkg-config software-properties-common supervisor openssh-server && \
    add-apt-repository ppa:webupd8team/java && \
    add-apt-repository ppa:cwchien/gradle && \
    curl -s https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - && \
    echo 'deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main' > /etc/apt/sources.list.d/pgdg.list && \
    apt-get update && \
    echo 'oracle-java8-installer shared/accepted-oracle-license-v1-1 boolean true' | debconf-set-selections && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y oracle-java8-installer \
        ca-certificates oracle-java8-set-default gradle-2.10 nginx \
        python-psycopg2 postgresql-9.4 postgresql-contrib-9.4 postgresql-client-9.4 && \
    useradd -d /home/isucon -m -s /bin/bash isucon -G sudo && \
    echo isucon:isucon | chpasswd && \
    cp -p /usr/share/zoneinfo/Japan /etc/localtime && cd /home/isucon && \
    locale-gen ja_JP.UTF-8 && locale-gen en_US.UTF-8 && \
    update-locale LANG=ja_JP.UTF-8 && update-locale LANG=en_US.UTF-8

RUN git clone --depth 1 https://github.com/tagomoris/xbuild.git /home/isucon/.xbuild && \
    /home/isucon/.xbuild/ruby-install 2.2.3 /home/isucon/.local/ruby && \
    /home/isucon/.xbuild/node-install v4.1.1 /home/isucon/.local/node && \
    /home/isucon/.xbuild/python-install 3.4.3 /home/isucon/.local/python3 && \
    /home/isucon/.xbuild/perl-install 5.20.2 /home/isucon/.local/perl && \
    /home/isucon/.xbuild/php-install 5.6.13 /home/isucon/.local/php -- --with-pear --with-openssl --with-pdo-pgsql && \
    /home/isucon/.xbuild/go-install 1.5 /home/isucon/.local/go && \
    #curl http://downloads.typesafe.com/scala/2.11.7/scala-2.11.7.tgz | (cd /home/isucon/.local && tar xzf -) && ln -s /home/isucon/.local/scala-2.11.7 /home/isucon/.local/scala && \
    ln -s /usr/lib/jvm/java-8-oracle /usr/lib/jvm/default-java

RUN git clone --depth 1 https://github.com/isucon/isucon5-final.git /tmp/isucon5-final && \
    cp /tmp/isucon5-final/provisioning/image/files/bashrc /home/isucon/.bashrc && chmod 755 /home/isucon/.bashrc && \
    cp /tmp/isucon5-final/provisioning/image/files/nginx.conf /etc/nginx/nginx.conf && \
    cp /tmp/isucon5-final/provisioning/image/files/nginx.php.conf /etc/nginx/nginx.php.conf && \
    cp /tmp/isucon5-final/provisioning/image/files/pg_hba.conf /etc/postgresql/9.4/main/pg_hba.conf && chown postgres:postgres /etc/postgresql/9.4/main/pg_hba.conf && chmod 640 /etc/postgresql/9.4/main/pg_hba.conf && \
    cp /tmp/isucon5-final/provisioning/image/files/postgresql.conf /etc/postgresql/9.4/main/postgresql.conf && chown postgres:postgres /etc/postgresql/9.4/main/postgresql.conf && service postgresql restart && \
    sudo -u postgres psql -c 'CREATE USER isucon;' && sudo -u postgres psql -c "CREATE DATABASE isucon5f OWNER isucon TEMPLATE template0 ENCODING 'utf8';" && \
    sudo -u postgres psql -d isucon5f -c 'CREATE EXTENSION pgcrypto;' && sudo -u isucon psql isucon5f < /tmp/isucon5-final/provisioning/image/files/schema.sql && \
    rsync -avz --delete /tmp/isucon5-final/webapp /home/isucon/ && cp /tmp/isucon5-final/provisioning/image/files/env.sh /home/isucon/env.sh && chmod 755 /home/isucon/env.sh && \
    cd /home/isucon/webapp/sql && sed -ie 's/api.five-final.isucon.net/api/' ./initialize.sql && cat ./initialize.sql | sudo -u isucon psql isucon5f && \
    cd /home/isucon/webapp/ruby && PATH=/home/isucon/.local/ruby/bin:$PATH bundle install && \
    cd /home/isucon/webapp/perl && PATH=/home/isucon/.local/perl/bin:$PATH carton install && \
    cd /home/isucon/webapp/php && PATH=/home/isucon/.local/php/bin:$PATH php composer.phar install && \
    cd /home/isucon/webapp/node && PATH=/home/isucon/.local/node/bin:$PATH npm install && \
    cd /home/isucon/webapp/java && ./mvnw clean package -Dmaven.test.skip=true && \
    /home/isucon/.local/python3/bin/pip install bottle && /home/isucon/.local/python3/bin/pip install pg8000 && /home/isucon/.local/python3/bin/pip install gunicorn && \
    cd /home/isucon/webapp/golang && PATH=/home/isucon/.local/go/bin:$PATH GOROOT=/home/isucon/.local/go GOPATH=/home/isucon/webapp/golang go get github.com/lib/pq && \
    PATH=/home/isucon/.local/go/bin:$PATH GOROOT=/home/isucon/.local/go GOPATH=/home/isucon/webapp/golang go get github.com/gorilla/context && \
    PATH=/home/isucon/.local/go/bin:$PATH GOROOT=/home/isucon/.local/go GOPATH=/home/isucon/webapp/golang go get github.com/gorilla/mux && \
    PATH=/home/isucon/.local/go/bin:$PATH GOROOT=/home/isucon/.local/go GOPATH=/home/isucon/webapp/golang go get github.com/gorilla/sessions && \
    PATH=/home/isucon/.local/go/bin:$PATH GOROOT=/home/isucon/.local/go GOPATH=/home/isucon/webapp/golang go build -o app && \
    #cd /home/isucon/webapp/scala && ./sbt launcher && \
    cp /tmp/isucon5-final/provisioning/image/files/supervisor.*.conf /etc/supervisor/conf.d/

RUN chown -R isucon:isucon /home/isucon/ && rm -rf /tmp/* /root/.bash_history /home/isucon/.bash_history; true

EXPOSE 80 22

CMD supervisord -c /etc/supervisor/supervisord.conf; service nginx start; service ssh start; service postgresql start; tail -f /dev/null
