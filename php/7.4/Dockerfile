#Ubuntu + php + apache + composer + laravel

FROM ubuntu:20.04
ENV DEBIAN_FRONTEND noninteractive
ENV PHP_INI /etc/php/7.4/apache2

RUN apt-get update && \
    apt-get install -y \
    sudo \
    unzip \
    wget \
    curl \
    nano \
    bash-completion \
    software-properties-common \
    openssh-server \
    net-tools \
    apt-transport-https \
    ca-certificates \
    rsync \
    mysql-client \
    git

RUN useradd -m -c "" --shell /bin/bash user && \
    adduser user sudo && \
    echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
    mkdir /projects

USER user
WORKDIR /home/user/

RUN echo "source /etc/profile.d/bash_completion.sh" >> ~/.bashrc

RUN sudo add-apt-repository ppa:ondrej/php
RUN sudo apt-get install -y \
    libaio1 \
    php7.4 \
    php7.4-cli \
    apache2 \
    php-mcrypt \
    php7.4-curl \
    php7.4-mysql \
    php7.4-pgsql \
    php7.4-gd \
    libapache2-mod-php7.4 \
    php7.4-json \
    php7.4-cgi \
    php7.4-sqlite3 \
    php7.4-mbstring \
    php7.4-xml \
    php7.4-dev \
    php7.4-zip \
    php-pear

RUN sudo update-alternatives --set php /usr/bin/php7.4

RUN sudo systemctl enable ssh && sudo service ssh start

RUN sudo sed -i 's/\/var\/www\/html/\/projects/g'  /etc/apache2/sites-available/000-default.conf && \
    sudo sed -i 's/\/var\/www/\/projects/g'  /etc/apache2/apache2.conf && \
    sudo sed -i 's/None/All/g' /etc/apache2/sites-available/000-default.conf && \
    echo "ServerName localhost" | sudo tee -a /etc/apache2/apache2.conf && \
    sudo a2enmod rewrite

RUN sudo wget -qO /usr/local/bin/phpunit https://phar.phpunit.de/phpunit.phar && sudo chmod +x /usr/local/bin/phpunit && \
    sudo apt-get clean && \
    sudo apt-get -y autoremove && \
    sudo apt-get -y clean && \
    sudo rm -rf /var/lib/apt/lists/*

RUN curl -sS https://getcomposer.org/installer -o composer-setup.php && \
    sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer   

RUN curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash - && \
    sudo apt-get install -y nodejs

RUN composer global require "laravel/installer" --with-all-dependencies

RUN composer global require symfony/console

RUN composer global require bamarni/symfony-console-autocomplete

ENV PATH /home/user/.config/composer/vendor/bin:$PATH

EXPOSE 80 8000

CMD sudo chown -R www-data:www-data /projects && \
    sudo chmod -R 755 /projects && \
    sudo service ssh start && \
    sudo service apache2 start && \
    tail -f /dev/null