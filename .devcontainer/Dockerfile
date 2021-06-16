# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.177.0/containers/php/.devcontainer/base.Dockerfile

# BASE IMAGE
# [Choice] PHP version: 8, 8.0, 7, 7.4, 7.3
ARG VARIANT
ARG BASE_IMAGE
FROM $BASE_IMAGE:0-${VARIANT}


ARG USER_UID
ARG USER_GID
ARG USERNAME
ARG remoteUser

RUN echo "USER: ${remoteUser}"

RUN if [ "$USER_GID" != "1000" ] || [ "$USER_UID" != "1000" ]; then groupmod --gid $USER_GID ${USERNAME} && usermod --uid $USER_UID --gid $USER_GID ${USERNAME}; fi

# [Option] Install Node.js
ARG INSTALL_NODE
ARG NODE_VERSION="lts"
RUN if [ "${INSTALL_NODE}" = "true" ]; then su ${USERNAME} -c "umask 0002 && . /usr/local/share/nvm/nvm.sh && nvm install ${NODE_VERSION} 2>&1"; fi


# [Optional] Uncomment this section to install additional OS packages.
ARG INSTALL_SYSTOOLS
RUN if [ "${INSTALL_SYSTOOLS}" = "true" ]; then apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends vim vifm curl imagemagick imagemagick-doc sudo; fi 

# [Optional] Uncomment this line to install global node packages.
#RUN su vscode -c "source /usr/local/share/nvm/nvm.sh && npm install -g eslint" 2>&1


## COPY FILES 
COPY ./symfony /workspace/symfony
WORKDIR /workspace/symfony



# INSTALL RUST
# [Choice] Uncomment this line to install global node packages.
USER $USERNAME
ARG INSTALL_RUST
RUN if [ "${INSTALL_RUST}" = "true" ]; then curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y; fi
ENV PATH="/home/${USERNAME}/.cargo/bin:${PATH}"


# INSTALL PHP LIBS
USER root
ARG INSTALL_PHP
RUN if [ "${INSTALL_PHP}" = "true" ]; then apt-get update \
    && apt-get install -y libssl-dev libzip-dev firebird-dev freetds-dev freetds-bin freetds-common libldb-dev libldap2-dev libc-client-dev libc-client-dev libkrb5-dev libgmp-dev libpq-dev libenchant-dev libcurl4-openssl-dev libbz2-dev libmcrypt-dev libicu-dev libpng-dev libxml2-dev msmtp bash && rm -r /var/lib/apt/lists/* \
    && ln -s /usr/lib/x86_64-linux-gnu/libsybdb.so /usr/lib/ \
    && mkdir /etc/php.d \
    && ln -s /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini; fi 

RUN if [ "${INSTALL_PHP}" = "true" ]; then docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-source extract \
    && docker-php-ext-install bcmath bz2 calendar gd intl opcache pdo_pgsql pgsql zip \ 
    && docker-php-ext-enable pgsql \
    && docker-php-ext-enable opcache \
    && docker-php-source delete; fi

#bcmath bz2 calendar dba enchant exif ffi gd gettext gmp imap intl ldap mysqli opcache pcntl pdo_dblib pdo_firebird pdo_mysql pdo_pgsql pgsql posix pspell shmop snmp soap sockets spl standard sysvmsg sysvsem sysvshm tidy tokenizer xsl zend_test zip 

# RUN pecl install mongodb redis \
#     && docker-php-ext-enable mongodb \
#     && docker-php-ext-enable redis \
#     && docker-php-source delete 



# INSTALL PACKES COOMANDS

## SYMFONY
# [Optional] Uncomment this line to install symfony.
USER $USERNAME
RUN if [ "${INSTALL_PHP}" = "true" ]; then curl -LsS https://get.symfony.com/cli/installer | bash; fi
ENV PATH="/home/${USERNAME}/.symfony/bin:${PATH}"

## COMPOSER
# [Optional] Uncomment this line to install composer packes.
USER root
RUN if [ "${INSTALL_PHP}" = "true" ]; then composer install; fi

## CARGO
# [Optional] Uncomment this line to install cargo modules.
USER $USERNAME
RUN if [ "${INSTALL_RUST}" = "true" ]; then cargo install sd; fi

## NPM / YARN
# [Optional] Uncomment this line to install node packages.
USER root
RUN if [ "${INSTALL_NODE}" = "true" ]; then  npm i; fi

## PIP
# [Optional] Uncomment this line to install node packages.
USER $USERNAME
#RUN if [ "${INSTALL_PYTHON}" = "true" ]; then "npm i"; fi