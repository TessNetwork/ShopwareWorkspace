ARG PHP_VERSION=8.1
FROM php:${PHP_VERSION}-cli

ARG SHOPWARE_VERSION
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

ARG INSTALL_ZSH="true"
ARG UPGRADE_PACKAGES="true"
ARG COMMON_SCRIPT_SOURCE="https://raw.githubusercontent.com/microsoft/vscode-dev-containers/main/script-library/common-debian.sh"
ARG COMMON_SCRIPT_SHA="dev-mode"

ENV SHOPWARE_ROOT="/shopware"
ENV SHOPWARE_PUBLIC="/shopware/public"

    # update image
RUN apt-get update && apt-get upgrade -y && \
    export DEBIAN_FRONTEND=noninteractive && \
    apt-get -y install --no-install-recommends curl ca-certificates 2>&1 && \
    curl -sSL ${COMMON_SCRIPT_SOURCE} -o /tmp/common-setup.sh && \
    ([ "${COMMON_SCRIPT_SHA}" = "dev-mode" ] || (echo "${COMMON_SCRIPT_SHA} */tmp/common-setup.sh" | sha256sum -c -)) && \
    /bin/bash /tmp/common-setup.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}" && \
    # install database
    apt-get -y install --no-install-recommends
    # configure php
    apt-get -y install --no-install-recommends libzip-dev libpng-dev libicu-dev && \
    docker-php-ext-install gd intl zip pdo_mysql && \
    apt-get purge -y libzip-dev libpng-dev libicu-dev && \
    cp $PHP_INI_DIR/php.ini-development $PHP_INI_DIR/php.ini && \
    sed -i 's/memory_limit =/;memory_limit =/' $PHP_INI_DIR/php.ini && \
    echo 'memory_limit = 512M' >> $PHP_INI_DIR/php.ini && \
    # install shopware
    apt-get install -y git && \
    git clone -b "v${SHOPWARE_VERSION}" https://github.com/shopware/production.git ${SHOPWARE_ROOT} && \
    rm -rf $SHOPWARE_ROOT/.git* && \
    # install composer
    apt-get install -y wget && \
    wget -O /tmp/composer-setup.php https://getcomposer.org/installer && \
    php /tmp/composer-setup.php --install-dir=/usr/bin --filename=composer --version=2.4.0 && \
    rm /tmp/composer-setup.php && \
   # install dependencies
    COMPOSER_ALLOW_SUPERUSER=1 composer install -o -d $SHOPWARE_ROOT && \
    chown -R ${USERNAME}:${USERNAME} $SHOPWARE_ROOT && \
    cp $SHOPWARE_PUBLIC/.htaccess.dist $SHOPWARE_PUBLIC/.htaccess && \
    apt-get autoremove -y && \
    apt-get clean -y && \
    rm -rf /var/lib/apt/lists/* /tmp/common-setup.sh /tmp/node-setup.sh

ENV APP_ENV "dev"
ENV APP_URL "http://127.0.0.1:8000"
ENV COMPOSER_HOME "$SHOPWARE_ROOT/var/cache/composer"

USER ${USERNAME}
WORKDIR $SHOPWARE_ROOT
RUN bin/console assets:install