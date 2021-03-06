ARG BUILD_FROM
FROM ${BUILD_FROM}

# Base env settings
ENV \
    LANG="en_US.utf8" \
    THINGENGINE_HOME="/data/almond-server" \
    THINGENGINE_HAS_REVERSE_PROXY=true
# \
#    THINGENGINE_HOST_BASED_AUTHENTICATION="insecure"

WORKDIR /opt/almond
ARG ALMOND_VERSION

RUN adduser --disabled-password almond && \
   chown almond:almond /opt/almond
WORKDIR /opt/almond

RUN \
    set -x \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        libcanberra-dev \
        libcanberra0 \
        libpulse-dev \
        libatlas-base-dev \
        libatlas3-base \
        pkg-config \
        git \
        gnupg \
        nginx \
        python-dev \
        software-properties-common \
        unzip \
        sqlite \
        coreutils \
        pulseaudio \
        gettext \
        zip \
        sudo \
        wget \
    \
    && curl -sS https://deb.nodesource.com/gpgkey/nodesource.gpg.key \
        | apt-key add - \
    && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg \
        | apt-key add - \
    && echo "deb https://deb.nodesource.com/node_12.x buster main" \
        > /etc/apt/sources.list.d/nodesource.list \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" \
        > /etc/apt/sources.list.d/yarn.list \
    \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        nodejs \
        yarn \
    \
    && npm config set unsafe-perm \
    \
    && sudo -u almond git clone -b "${ALMOND_VERSION}" --depth 1 \
        "https://github.com/ad31c0/almond-server" . && \
    sudo -u almond npm ci && \
    sudo -u almond npm install --no-package-lock --ignore-scripts snowboy && \
    cd node_modules/snowboy/ && \
    sudo -u almond PYTHON=python2 npx node-pre-gyp clean configure && \
    sudo -u almond make -C build/ && \
    rm -fr /home/almond/.cache && \
    rm -fr /home/almond/.npm && \
    mkdir /home/almond/.cache \
    && apt-get purge -y --auto-remove \
        build-essential \
        libcanberra-dev \
        libpulse-dev \
        libatlas-base-dev \
        pkg-config \
        git \
        gnupg \
        python-dev \
        software-properties-common \
    \
    && rm -rf \
        /opt/almond/.[!.]* \
        /root/.cache \
        /root/.config \
        /tmp/.[!.]* \
        /tmp/* \
        /usr/local/share/.cache \
        /usr/local/share/.config \
        /usr/lib/nginx \
        /var/lib/apt/lists/* \
        /var/www

COPY rootfs /
