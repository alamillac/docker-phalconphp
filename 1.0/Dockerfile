# phalconphp
#
# VERSION               0.0.1

FROM     alamilla/apache2-php
MAINTAINER Andres F. Lamilla, "alamilla@gmail.com"

# actualizacion repositorios
RUN apt-get update

# instalacion de los paquetes necesarios para la app
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y php5-dev php5-mysql gcc libpcre3-dev

# se adicionan los archivos de phalconphp
RUN mkdir /tmp/phalcon && cd /tmp/phalcon && git clone --depth=1 git://github.com/phalcon/cphalcon.git
RUN cd /tmp/phalcon/cphalcon/build && ./install
RUN rm -rf /tmp/phalcon
RUN echo -e "; configuration for php PHALCON module\n; priority=50\nextension=phalcon.so" > /etc/php5/mods-available/phalcon.ini
RUN ln -s /etc/php5/mods-available/phalcon.ini /etc/php5/apache2/conf.d/50-phalcon.ini

# se adiciona geoip (http://php.net/manual/en/geoip.setup.php)
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y php5-geoip

RUN cd /tmp && wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz && gunzip GeoLiteCity.dat.gz
RUN mkdir -v /usr/share/GeoIP ; mv -v /tmp/GeoLiteCity.dat /usr/share/GeoIP/GeoIPCity.dat

# se adiciona mcrypt
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y php5-mcrypt
#php5enmod mcrypt
RUN ln -s /etc/php5/mods-available/mcrypt.ini /etc/php5/apache2/conf.d/40-mcrypt.ini

# se adiciona php-curl
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y php5-curl

# se adiciona cache con redis e igbinary
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y php-pear php5-dev redis-server
RUN pecl install igbinary
RUN echo "extension=igbinary.so" > /etc/php5/mods-available/igbinary.ini
RUN ln -s /etc/php5/mods-available/igbinary.ini /etc/php5/apache2/conf.d/30-igbinary.ini

RUN cd /tmp && wget https://github.com/nicolasff/phpredis/archive/master.zip && unzip master.zip
RUN cd /tmp/phpredis-master/ && phpize && ./configure --enable-redis-igbinary && make && make install
RUN rm -r /tmp/phpredis-master
RUN echo "extension=redis.so"  > /etc/php5/mods-available/redis.ini
RUN ln -s /etc/php5/mods-available/redis.ini /etc/php5/apache2/conf.d/30-redis.ini

# se adicionan los archivos de supervisor de redis-server
ADD src/redis.sv.conf /etc/supervisor/conf.d/redis.sv.conf
ADD src/start_redis.sh /usr/local/bin/start_redis.sh
RUN chmod +x /usr/local/bin/*.sh

# se adiciona la configuracion de apache
RUN rm -f /etc/apache2/sites-enabled/*
ADD src/phalconApp.conf /etc/apache2/sites-available/phalconApp.conf
RUN a2ensite phalconApp

# se habilita el puerto 80 y el script de inicio
EXPOSE 80
CMD ["/usr/local/bin/run.sh"]
