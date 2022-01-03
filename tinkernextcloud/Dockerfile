FROM docker.io/library/nextcloud:fpm

# Work-around for jre installs
RUN mkdir -p /usr/share/man/man1

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get install -y default-jre && \
    apt-get install -y libreoffice libreoffice-l10n-de libreoffice-help-de && \
    apt-get install -y ffmpeg imagemagick ghostscript && \
     rm -rf /var/lib/apt/lists/*

RUN groupadd -g 1000 usergroup0
RUN groupadd -g 1001 usergroup1
RUN groupadd -g 1002 usergroup2
RUN groupadd -g 1003 usergroup3
RUN groupadd -g 1004 usergroup4
RUN groupadd -g 1005 usergroup5
RUN usermod -a -G users,usergroup0,usergroup1,usergroup2,usergroup3,usergroup4,usergroup5 www-data

# Fix PolicyMap for ImageMagick https://www.allerstorfer.at/nextcloud-install-preview-generator/
RUN sed -i 's/domain="coder" rights="none"/domain="coder" rights="read|write"/g' /etc/ImageMagick-6/policy.xml


