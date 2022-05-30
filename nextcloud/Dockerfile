FROM docker.io/library/nextcloud:fpm

# Work-around for jre installs
RUN mkdir -p /usr/share/man/man1

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get install -y default-jre && \
    apt-get install -y libreoffice libreoffice-l10n-de libreoffice-help-de && \
    apt-get install -y ffmpeg imagemagick ghostscript

RUN apt-get install -y libbz2-dev
RUN docker-php-ext-install bz2

RUN apt-get install -y wget
RUN echo "deb https://repo.delellis.com.ar bullseye bullseye" > /etc/apt/sources.list.d/20-pdlib.list
RUN wget -qO - https://repo.delellis.com.ar/repo.gpg.key | apt-key add -
RUN apt update
RUN apt install -y libdlib-dev

# Install pdlib extension
RUN apt install -y unzip
RUN wget https://github.com/goodspb/pdlib/archive/master.zip \
  && mkdir -p /usr/src/php/ext/ \
  && unzip -d /usr/src/php/ext/ master.zip \
  && rm master.zip
RUN docker-php-ext-install pdlib-master

# These last lines are just for testing the extension.. You can delete them.
RUN wget https://github.com/matiasdelellis/pdlib-min-test-suite/archive/master.zip \
  && unzip -d /tmp/ master.zip \
  && rm master.zip
RUN cd /tmp/pdlib-min-test-suite-master \
    && make

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


