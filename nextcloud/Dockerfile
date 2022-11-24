FROM docker.io/library/nextcloud:fpm

# Work-around for jre installs
RUN mkdir -p /usr/share/man/man1

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y default-jre && \
    apt-get install -y libreoffice libreoffice-l10n-de libreoffice-help-de && \
    apt-get install -y ffmpeg imagemagick ghostscript

RUN echo memory_limit=1024M > /usr/local/etc/php/conf.d/memory-limit.ini

RUN groupadd -g 1000 usergroup0
RUN groupadd -g 1001 usergroup1
RUN groupadd -g 1002 usergroup2
RUN groupadd -g 1003 usergroup3
RUN groupadd -g 1004 usergroup4
RUN groupadd -g 1005 usergroup5
RUN usermod -a -G users,usergroup0,usergroup1,usergroup2,usergroup3,usergroup4,usergroup5 www-data

# Fix PolicyMap for ImageMagick https://www.allerstorfer.at/nextcloud-install-preview-generator/
RUN sed -i 's/domain="coder" rights="none"/domain="coder" rights="read|write"/g' /etc/ImageMagick-6/policy.xml

# FIX WARNING: [pool www] server reached pm.max_children setting (5), consider raising it
RUN sed -i 's/pm.max_children.*/pm.max_children = 256/g' /usr/local/etc/php-fpm.d/www.conf
# FIX  WARNING: [pool www] seems busy (you may need to increase pm.start_servers, or pm.min/max_spare_servers), spawning 32 children, there are 0 idle, and 8 total children
RUN sed -i 's/pm.min_spare_servers.*/pm.min_spare_servers = 32/g'  /usr/local/etc/php-fpm.d/www.conf
RUN sed -i 's/pm.max_spare_servers.*/pm.max_spare_servers = 64/g' /usr/local/etc/php-fpm.d/www.conf
RUN sed -i 's/pm.start_servers.*/pm.start_servers = 48/g'         /usr/local/etc/php-fpm.d/www.conf



