ARG BASE_IMAGE="ubuntu:22.04"
FROM ${BASE_IMAGE}

SHELL ["/bin/bash", "-c"]
ARG DEBIAN_FRONTEND=noninteractive

ARG CODE_SERVER_VERSION=4.22.0

# set custom user
ARG USERNAME=robolaunch
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN set -eux; \
    groupadd --gid $USER_GID $USERNAME; \
    useradd --uid $USER_UID --gid $USERNAME --shell /bin/bash --create-home $USERNAME; \
    chown -R $USERNAME:$USERNAME /home/$USERNAME

RUN apt-get update && apt-get install -y curl git python3 python3-pip
RUN curl -fsSL https://code-server.dev/install.sh | sh -s -- --version=${CODE_SERVER_VERSION}

ARG FILEBROWSER_CONFIG_PATH=/etc/robolaunch/filebrowser
RUN mkdir -p /etc/robolaunch/services ${FILEBROWSER_CONFIG_PATH} /var/log/services

RUN curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash; \
    git clone https://github.com/robolaunch/file-manager-config ${FILEBROWSER_CONFIG_PATH}/filebrowser-config; \
    filebrowser config init -d ${FILEBROWSER_CONFIG_PATH}/filebrowser.db; \
    filebrowser users add admin admin -d ${FILEBROWSER_CONFIG_PATH}/filebrowser.db; \
    filebrowser config set --auth.method=noauth -d ${FILEBROWSER_CONFIG_PATH}/filebrowser.db; \
    filebrowser config set --branding.name "robolaunch" \
    --branding.files ${FILEBROWSER_CONFIG_PATH}"/filebrowser-config/branding" \
    --branding.disableExternal \
    -d ${FILEBROWSER_CONFIG_PATH}/filebrowser.db

RUN cp ${FILEBROWSER_CONFIG_PATH}/filebrowser.db ${FILEBROWSER_CONFIG_PATH}/filebrowser-code-server.db

RUN chmod 1777 \
        ${FILEBROWSER_CONFIG_PATH}/filebrowser.db \
        ${FILEBROWSER_CONFIG_PATH}/filebrowser-code-server.db \
        /var/log/services \
        ${FILEBROWSER_CONFIG_PATH}/filebrowser-config/; \
    chown $USERNAME \
        ${FILEBROWSER_CONFIG_PATH}/filebrowser.db \
        ${FILEBROWSER_CONFIG_PATH}/filebrowser-code-server.db \
        /var/log/services \
        ${FILEBROWSER_CONFIG_PATH}/filebrowser-config/

COPY ./services/ /etc/robolaunch/services/