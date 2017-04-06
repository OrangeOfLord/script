
SOFTWARE_PATH ?= $(HOME)

PHP_PATH ?= $(SOFTWARE_PATH)/software/php7.0.14dd
OEPNRESTY_PATH ?= $(SOFTWARE_PATH)/software/openrestydd
ORANGE_PATH ?= $(SOFTWARE_PATH)/software/orangedd
SYSTEMTAP_PATH ?= $(SOFTWARE_PATH)/software/stap
SYSTEMTAP_VERSION ?= 3.1

BUILD_PATH?=$(SOFTWARE_PATH)/build

PHP_VERSION ?= 7.0.14
PHP_AMQP_VERSION ?= 1.7.1

export $$PATH := $(PHP_PATH)/bin:$$PATH
export $$PATH := $(OEPNRESTY_PATH)/bin:$$PATH

prerequisites:prerequisites-yum

prerequisites-yum:
	mkdir -p $(SOFTWARE_PATH)
	sudo yum install -y  \
				readline-devel \
				pcre-devel \
				openssl-devel \
				gcc \
				wget \
				icu libicu libicu-devel \
				autoconf \
				bzip2 \
				curl \
				libxml2-devel \
				gd-devel \
				libmcrypt-devel \
				libcurl-devel\
				libuuid-devel \
				bzip2-devel \
				gcc-c++ \
				git \
				patch  \
				htop \
				libtool \
				cmake \
				vim \
				make \
				ntpdate \
				# libbz2-dev \
				# libfreetype6-dev \
				# libcurl4-gnutls-dev 
				# libpng-dev 
				# libmcrypt-dev 
				# libjpeg-dev
	sudo ntpdate time.nist.gov
yum-shell:
	sudo yum install -y curl zsh fish
	sh -c "$$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

deb-shell:
	sudo apt-get install -y curl zsh fish
	sh -c "$$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"


fedora-systemtap:prerequisites
	sudo yum install -y systemtap systemtap-runtime kernel-devel yum-utils  
	sudo debuginfo-install -y kernel

systemtap-check:
	@echo '=================================== In Ubuntu 16.04 ========================================================================================='
	@echo "exce:                    source /etc/profile.d/devops.sh                              "
	@echo "dbgsym:     http://ddebs.ubuntu.com/pool/main/l/linux/?C=M;O=D                        "
	@echo '============================================================================================================================'
	stap -ve 'probe begin { log("hello world") exit () }' 
	stap -c df -e 'probe syscall.* { if (target()==pid()) log(name." ".argstr) }' 
	gcc -g -O2 system-check.c  -o a.out 
	stap -e 'probe process("./a.out").function("main") { printf("hello\n"); }' -c "./a.out" 
	# sudo stap -d /bin/ls --ldd -e 'probe process("ls").function("xmalloc") {print_usyms(ubacktrace())}' -c "ls /"


update-hosts:
	sudo wget https://raw.githubusercontent.com/racaljk/hosts/master/hosts -O /etc/hosts


fedora-desktop:update-hosts
	sudo yum install chromium

prerequisites-ubuntu:
	mkdir -p  $(SOFTWARE_PATH)
	# @echo $$PATH
	@sudo apt-get  install -y \
				gdb    \
				libtool    \
				libxml2-dev    \
				pkg-config    \
				libssl-dev    \
				libbz2-dev    \
				libfreetype6-dev    \
				libmcrypt-dev    \
				curl    \
				wget    \
				libcurl4-gnutls-dev    \
				libicu-dev    \
				libpcre3    \
				libpcre3-dev    \
				zlib1g-dev    \
				libssl-dev    \
				build-essential    \
				bash-completion    \
				autoconf    \
				cmake    \
				git    \
				uuid-dev    \
				vim \
				make \
				ntpdate \
				gettext
#	选no,使用bash 否则无法使用source
	sudo dpkg-reconfigure  dash 
	sudo ntpdate time.nist.gov


unbutu-systemtap-depdency:prerequisites-ubuntu
	sudo apt-get install -y linux-image-`uname -r`\* linux-headers-`uname -r` 
	sudo apt-get install -y \
			libavahi-client-dev \
			libelf-dev \
			libnspr4-dev \
			libnss3-dev \
			libnss3-tools \
			pkg-config \
			texlive-fonts-recommended \
			texlive-latex-base \
			texlive-latex-extra \
			texlive-latex-recommended \
			gettext \
			elfutils \
			libdw-dev \

deb-systemtap:unbutu-systemtap-depdency systemtap-install

systemtap-install:
	test -f systemtap-$(SYSTEMTAP_VERSION).tar.gz ||wget https://sourceware.org/systemtap/ftp/releases/systemtap-$(SYSTEMTAP_VERSION).tar.gz 
	tar -zxvf systemtap-$(SYSTEMTAP_VERSION).tar.gz
	cd systemtap-3.1 &&\
	./configure --prefix=$(SYSTEMTAP_PATH)  --disable-docs --disable-refdocs CFLAGS="-g -O2"  &&\
	make && sudo make install
	sh -c 'echo PATH=$(SYSTEMTAP_PATH)/bin:$$PATH >> /etc/profile.d/devops.sh && source /etc/profile.d/devops.sh' &&\




openresty: prerequisites
	test -f 'openresty-1.11.2.2.tar.gz' || wget https://openresty.org/download/openresty-1.11.2.2.tar.gz 
	test -d 'openresty-1.11.2.2' || tar -zxvf openresty-1.11.2.2.tar.gz 
	test -d $(OEPNRESTY_PATH) || (\
	cd openresty-1.11.2.2 &&\
	./configure --prefix=$(OEPNRESTY_PATH) --with-debug --with-pcre-jit --with-ipv6 --with-http_gzip_static_module    --with-http_stub_status_module  --with-http_ssl_module --with-http_realip_module -j2 &&\
	make -j2 &&\
	sudo make install &&\
	sudo sh -c 'echo PATH=$(OEPNRESTY_PATH)/bin:$$PATH >> /etc/profile.d/devops.sh && source /etc/profile.d/devops.sh' \
	)
# --with-http_ssl_module --with-http_realip_module --with-google_perftools_module --with-http_upstream_check_module --with-http_concat_module

lor: openresty
	test -d lor || git clone https://github.com/thisverygoodhhhh/lor.git
	cd lor && git pull && \
	sudo make install
orange: lor
	test -d $(ORANGE_PATH) || git clone  https://github.com/thisverygoodhhhh/orange.git $(ORANGE_PATH)
	cd $(ORANGE_PATH) && git checkout master

php7:
	test -f $(BUILD_PATH)/php-$(PHP_VERSION).tar.gz || wget http://cn2.php.net/distributions/php-$(PHP_VERSION).tar.gz
	test -d $(BUILD_PATH)/php-$(PHP_VERSION) || tar -zxvf php-$(PHP_VERSION).tar.gz
	test -d $(PHP_PATH) || ( \
	cd php-$(PHP_VERSION) &&\
	./configure --prefix=$(PHP_PATH)/ --with-gd --enable-gd-native-ttf --with-zlib --with-mcrypt  --enable-shmop --enable-sockets --enable-wddx --enable-zip --enable-fpm --enable-mbstring --with-zlib-dir --with-bz2 --with-curl --enable-exif  --with-iconv --enable-xml --enable-inline-optimization --enable-bcmath  --with-openssl --with-gettext  --enable-session --enable-fpm --enable-intl --enable-mbstring --enable-opcache  --with-pdo-mysql --enable-mysqlnd &&\
	make  &&\
	make install && \
	cp php.ini-production $(PHP_PATH)/lib/php.ini  && \
	cp $(PHP_PATH)/etc/php-fpm.conf.default $(PHP_PATH)/etc/php-fpm.conf && \
	cp $(PHP_PATH)/etc/php-fpm.d/www.conf.default  $(PHP_PATH)/etc/php-fpm.d/www.conf && \
	echo "zend_extension=opcache.so" >>  $(PHP_PATH)/lib/php.ini && \
	echo 'export PATH=$(PHP_PATH)/bin/:$$PATH' >> ~/.bashrc && source ~/.bashrc \
	)

php7-ext-stomp: php7
	test -f 'stomp-2.0.0.tgz'||wget https://pecl.php.net/get/stomp-2.0.0.tgz
	test -d 'stomp-2.0.0' || tar -zxvf stomp-2.0.0.tgz && \
	source ~/.bashrc && \
	cd stomp-2.0.0 && \
	phpize && ./configure && make && make install && \
	echo "extension=stomp.so" >> $(PHP_PATH)/lib/php.ini

php7-ext-redis: php7
	test -f "redis.3.0.0.tar.gz" || wget https://github.com/phpredis/phpredis/archive/3.0.0.tar.gz  -O redis.3.0.0.tar.gz
	test -d "phpredis-3.0.0" || tar -zxvf  redis.3.0.0.tar.gz && \
	source ~/.bashrc && \
	cd phpredis-3.0.0 &&\
	phpize && ./configure && make && make install && \
	echo "extension=redis.so" >> $(PHP_PATH)/lib/php.ini

php7-ext-phalcon: php7
	test -d "cphalcon"|| git clone git://github.com/thisverygoodhhhh/cphalcon.git && \
	source ~/.bashrc &&\
	echo $$PATH && \
	cd cphalcon/build && \
	./install && \
	echo "extension=phalcon.so"   >> $(PHP_PATH)/lib/php.ini

php-composer:
	test -d "composer.phar" || wget https://getcomposer.org/download/1.3.2/composer.phar
	chmod +x composer.phar 
	cp composer.phar /bin/composer
	mv composer.phar /usr/local/bin/composer
	composer config -g repo.packagist composer https://packagist.phpcomposer.com
	composer



php-amqp: librabbitmq php7
	test -f $(BUILD_PATH)/amqp-$(PHP_AMQP_VERSION).tgz|| wget https://pecl.php.net/get/amqp-$(PHP_AMQP_VERSION).tgz
	test -d $(BUILD_PATH)/amqp-$(PHP_AMQP_VERSION) ||( tar -zxvf amqp-$(PHP_AMQP_VERSION).tgz  &&\
	cd $(BUILD_PATH)/amqp-$(PHP_AMQP_VERSION) && \
	phpize  && \
	./configure --with-amqp --with-librabbitmq-dir=/usr/local/ && \
	make && \
	make install &&  \
	echo "extension=amqp.so"   >> $(PHP_PATH)/lib/php.ini && \
	make test )\

librabbitmq:
	test -d $(BUILD_PATH)/rabbitmq-c || git clone https://github.com/alanxz/rabbitmq-c.git $(BUILD_PATH)/rabbitmq-c
	# (sudo find / -name librabbitmq.so | grep -v 'rabbitmq-c' )|| 
	cd $(BUILD_PATH)/rabbitmq-c/ && \
	git submodule init && \
	git submodule update && \
	((git branch|grep "* v0.8.0") || git checkout -b v0.8.0 v0.8.0 ) && \
	autoreconf -i && \
	./configure && \
	mkdir -p build && cd build && \
	cmake .. && \
	sudo cmake --build .  --target install
	(tt= `sudo find / -name librabbitmq.so | grep -v 'rabbitmq-c'`) && tt1=$${tt:0:$${#tt}-15} && echo "add '$$tt1' to  /etc/ld.so.conf.d/devpos.conf" && echo $$tt1 >>  /etc/ld.so.conf.d/devpos.conf 
	sudo ldconfig
	

wrk:
	git clone https://github.com/wg/wrk.git
	cd wrk && \
	make && \
	sudo cp wrk /bin
	wrk

install-all: prerequisites openresty orange php7 php7-ext-stomp php7-ext-redis php7-ext-phalcon php-amqp php-composer


update-orange: orange
	cd $(ORANGE_PATH) && git checkout master && git pull origin master

update-lor: lor

bug-fedora:
	sudo dnf upgrade perl-libs