#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

ARG CONTROLLA_DEV_ENVIROMENT
FROM controlla/controlla:${CONTROLLA_DEV_ENVIROMENT}

# Start as root
USER root

###########################################################################
# controlla non-root user:
###########################################################################

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

# always run apt update when start and after add new source list, then clean up at end.
RUN set -xe; \
    apt-get update -yqq && \
    groupadd -g ${PGID} controlla && \
    useradd -l -u ${PUID} -g controlla -m controlla && \
    usermod -p "*" controlla -s /bin/bash && \
    apt-get install -yqq \
      apt-utils
#--------------------------------------------------------------------------
# Optional Software's Installation
#--------------------------------------------------------------------------
#
# Optional Software's will only be installed if you set them to `true`
# in the `docker-compose.yml` before the build.
# Example:
#   - INSTALL_NODE=false
#   - ...
#

###########################################################################
# Set Timezone
###########################################################################

ARG TZ=UTC
ENV TZ ${TZ}

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

###########################################################################
# User Aliases
###########################################################################

USER root

COPY ./aliases.sh /root/aliases.sh
COPY ./aliases.sh /home/controlla/aliases.sh

RUN sed -i 's/\r//' /root/aliases.sh && \
    sed -i 's/\r//' /home/controlla/aliases.sh && \
    chown controlla:controlla /home/controlla/aliases.sh && \
    echo "" >> ~/.bashrc && \
    echo "# Load Custom Aliases" >> ~/.bashrc && \
    echo "source ~/aliases.sh" >> ~/.bashrc && \
	  echo "" >> ~/.bashrc

USER controlla

RUN echo "" >> ~/.bashrc && \
    echo "# Load Custom Aliases" >> ~/.bashrc && \
    echo "source ~/aliases.sh" >> ~/.bashrc && \
	  echo "" >> ~/.bashrc

###########################################################################
# Composer:
###########################################################################

USER root

# Add the composer.json
COPY ./composer.json /home/controlla/.composer/composer.json

# Add the auth.json for magento 2 credentials
COPY ./auth.json /home/controlla/.composer/auth.json

# Make sure that ~/.composer belongs to controlla
RUN chown -R controlla:controlla /home/controlla/.composer

# Export composer vendor path
RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="$HOME/.composer/vendor/bin:$PATH"' >> ~/.bashrc

USER controlla

# Check if global install need to be ran
ARG COMPOSER_GLOBAL_INSTALL=false
ENV COMPOSER_GLOBAL_INSTALL ${COMPOSER_GLOBAL_INSTALL}

RUN if [ ${COMPOSER_GLOBAL_INSTALL} = true ]; then \
    # run the install
    composer global install \
;fi

# Check if auth file is disabled
ARG COMPOSER_AUTH_JSON=false
ENV COMPOSER_AUTH_JSON ${COMPOSER_AUTH_JSON}

RUN if [ ${COMPOSER_AUTH_JSON} = false ]; then \
    # remove the file
    rm /home/controlla/.composer/auth.json \
;fi

ARG COMPOSER_REPO_PACKAGIST
ENV COMPOSER_REPO_PACKAGIST ${COMPOSER_REPO_PACKAGIST}

RUN if [ ${COMPOSER_REPO_PACKAGIST} ]; then \
    composer config -g repo.packagist composer ${COMPOSER_REPO_PACKAGIST} \
;fi

# Export composer vendor path
RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="~/.composer/vendor/bin:$PATH"' >> ~/.bashrc

###########################################################################
# Non-root user : PHPUnit path
###########################################################################

# add ./vendor/bin to non-root user's bashrc (needed for phpunit)
USER controlla

RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="/var/www/vendor/bin:$PATH"' >> ~/.bashrc

###########################################################################
# Crontab
###########################################################################

USER root

COPY ./crontab /etc/cron.d

RUN chmod -R 644 /etc/cron.d

###########################################################################
# ssh:
###########################################################################

ARG INSTALL_WORKSPACE_SSH=false

COPY insecure_id_rsa /tmp/id_rsa
COPY insecure_id_rsa.pub /tmp/id_rsa.pub

RUN if [ ${INSTALL_WORKSPACE_SSH} = true ]; then \
    rm -f /etc/service/sshd/down && \
    cat /tmp/id_rsa.pub >> /root/.ssh/authorized_keys \
        && cat /tmp/id_rsa.pub >> /root/.ssh/id_rsa.pub \
        && cat /tmp/id_rsa >> /root/.ssh/id_rsa \
        && rm -f /tmp/id_rsa* \
        && chmod 644 /root/.ssh/authorized_keys /root/.ssh/id_rsa.pub \
    && chmod 400 /root/.ssh/id_rsa \
    && cp -rf /root/.ssh /home/controlla \
    && chown -R controlla:controlla /home/controlla/.ssh \
;fi

###########################################################################
# Libpng16 EXTENSION
###########################################################################

ARG INSTALL_LIBPNG=false

RUN if [ ${INSTALL_LIBPNG} = true ]; then \
    apt-get -yqq install libpng16-16 libpng-dev libjpeg-dev libtiff-dev libgif-dev \
;fi

###########################################################################
# SUPERVISOR:
###########################################################################
ARG INSTALL_SUPERVISOR=false

RUN if [ ${INSTALL_SUPERVISOR} = true ]; then \
    if [ ${INSTALL_PYTHON} = true ]; then \
    python -m pip install --upgrade supervisor && \
    echo_supervisord_conf > /etc/supervisord.conf && \
    sed -i 's/\;\[include\]/\[include\]/g' /etc/supervisord.conf && \
    sed -i 's/\;files\s.*/files = supervisord.d\/*.conf/g' /etc/supervisord.conf \
  ;fi \
;fi

USER controlla

###########################################################################
# Oh My ZSH!
###########################################################################

USER root

ARG SHELL_OH_MY_ZSH=false
RUN if [ ${SHELL_OH_MY_ZSH} = true ]; then \
    apt install -y zsh \
;fi

ARG SHELL_OH_MY_ZSH_AUTOSUGESTIONS=false
ARG SHELL_OH_MY_ZSH_ALIASES=false

USER controlla
RUN if [ ${SHELL_OH_MY_ZSH} = true ]; then \
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh) --keep-zshrc" && \
    sed -i -r 's/^plugins=\(.*?\)$/plugins=(laravel5 git)/' /home/controlla/.zshrc && \
    sed -i -r 's/^ZSH_THEME=\".*?\"$/ZSH_THEME="af-magic"/' /home/controlla/.zshrc && \
    echo '\n\
bindkey "^[OB" down-line-or-search\n\
bindkey "^[OC" forward-char\n\
bindkey "^[OD" backward-char\n\
bindkey "^[OF" end-of-line\n\
bindkey "^[OH" beginning-of-line\n\
bindkey "^[[1~" beginning-of-line\n\
bindkey "^[[3~" delete-char\n\
bindkey "^[[4~" end-of-line\n\
bindkey "^[[5~" up-line-or-history\n\
bindkey "^[[6~" down-line-or-history\n\
bindkey "^?" backward-delete-char\n' >> /home/controlla/.zshrc && \
  if [ ${SHELL_OH_MY_ZSH_AUTOSUGESTIONS} = true ]; then \
    sh -c "git clone https://github.com/zsh-users/zsh-autosuggestions /home/controlla/.oh-my-zsh/custom/plugins/zsh-autosuggestions" && \
    sh -c "git clone https://github.com/zsh-users/zsh-syntax-highlighting /home/controlla/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting" && \
    sed -i 's~plugins=(~plugins=(zsh-autosuggestions zsh-syntax-highlighting ~g' /home/controlla/.zshrc && \
    sed -i '1iZSH_AUTOSUGGEST_BUFFER_MAX_SIZE=20' /home/controlla/.zshrc && \
    sed -i '1iZSH_AUTOSUGGEST_STRATEGY=(history completion)' /home/controlla/.zshrc && \
    sed -i '1iZSH_AUTOSUGGEST_USE_ASYNC=1' /home/controlla/.zshrc && \
    sed -i '1iTERM=xterm-256color' /home/controlla/.zshrc \
  ;fi && \
  if [ ${SHELL_OH_MY_ZSH_ALIASES} = true ]; then \
    echo "" >> /home/controlla/.zshrc && \
    echo "# Load Custom Aliases" >> /home/controlla/.zshrc && \
    echo "source /home/controlla/aliases.sh" >> /home/controlla/.zshrc && \
    echo "" >> /home/controlla/.zshrc \
  ;fi \
;fi

USER root

###########################################################################
# ZSH User Aliases
###########################################################################

USER root

COPY ./aliases.sh /root/aliases.sh
COPY ./aliases.sh /home/controlla/aliases.sh

COPY ./.npmrc /root/.npmrc
COPY ./.npmrc /home/controlla/.npmrc

RUN if [ ${SHELL_OH_MY_ZSH} = true ]; then \
    sed -i 's/\r//' /root/aliases.sh && \
    sed -i 's/\r//' /home/controlla/aliases.sh && \
    chown controlla:controlla /home/controlla/aliases.sh && \
    echo "" >> ~/.zshrc && \
    echo "# Load Custom Aliases" >> ~/.zshrc && \
    echo "source ~/aliases.sh" >> ~/.zshrc && \
	  echo "" >> ~/.zshrc \
;fi

USER controlla

RUN if [ ${SHELL_OH_MY_ZSH} = true ]; then \
    echo "" >> ~/.zshrc && \
    echo "# Load Custom Aliases" >> ~/.zshrc && \
    echo "source ~/aliases.sh" >> ~/.zshrc && \
	  echo "" >> ~/.zshrc \
;fi

USER root

###########################################################################
# pgsql client
###########################################################################

USER root

ARG INSTALL_PG_CLIENT=false

RUN if [ ${INSTALL_PG_CLIENT} = true ]; then \
    # Install the pgsql client
    apt-get -yqq install wget \
    && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
    && echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" | tee /etc/apt/sources.list.d/pgdg.list \
    && apt-get update \
    && apt-get -y install postgresql-client-12 \
;fi

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

USER root

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

# Set default work directory
WORKDIR /var/www
