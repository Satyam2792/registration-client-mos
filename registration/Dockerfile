FROM mosipdev/javafx-reg-client-openjdk:latest

ARG SOURCE
ARG COMMIT_HASH
ARG COMMIT_ID
ARG BUILD_TIME

LABEL source=${SOURCE}
LABEL commit_hash=${COMMIT_HASH}
LABEL commit_id=${COMMIT_ID}
LABEL build_time=${BUILD_TIME}

ARG client_version
ARG healthcheck_url
ARG client_upgrade_server
ARG cert_server_URL
ARG keystore_secret

ENV client_version_env=${client_version}
ENV healthcheck_url_env=${healthcheck_url}
ENV client_upgrade_server_env=${client_upgrade_server}
ENV client_repo_env=${client_repo_url}
ENV artifactory_url_env=${artifactory_url}
ENV host_name_env=${host_name}
ENV keystore_secret_env=${keystore_secret}
ENV signer_timestamp_url_env=${signer_timestamp_url}

ARG container_user=mosip
ARG container_user_group=mosip
ARG container_user_uid=1001
ARG container_user_gid=1001

# ================================
# Install packages + fontconfig + wget + locale
# ================================
RUN sed -i 's|http://deb.debian.org|http://archive.debian.org|g' /etc/apt/sources.list && \
    sed -i 's|http://security.debian.org|http://archive.debian.org|g' /etc/apt/sources.list && \
    echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check-valid-until && \
    apt-get -y update && \
    apt-get install -y unzip zip nginx fontconfig wget locales && \
    locale-gen en_US.UTF-8 my_MM.UTF-8 && \
    groupadd -g ${container_user_gid} ${container_user_group} && \
    useradd -u ${container_user_uid} -g ${container_user_group} -s /bin/sh -m ${container_user}

# Locale for JavaFX text shaping
ENV LANG=my_MM.UTF-8
ENV LC_ALL=my_MM.UTF-8

# ================================
# Install Pyidaungsu Myanmar Font
# ================================
RUN mkdir -p /usr/share/fonts/myanmar && \
    wget -q https://raw.githubusercontent.com/shivamg9/registration-client-mos/mos/registration-client/src/main/resources/fonts/Pyidaungsu.ttf \
    -O /usr/share/fonts/myanmar/Pyidaungsu.ttf && \
    fc-cache -fv

# ================================
# Working directory
# ================================
WORKDIR /home/${container_user}
ENV work_dir=/home/${container_user}

ADD registration-client/target ./registration-client/target
ADD registration-test/target ./registration-test/target
ADD registration-test/src/main/resources ./registration-test/resources
ADD configure.sh configure.sh

RUN chmod a+x configure.sh \
 && rm -f ./registration-client/target/registration-client-*-javadoc.jar \
 && rm -f ./registration-client/target/registration-client-*-sources.jar

RUN chown -R ${container_user}:${container_user} /home/${container_user}
RUN chown -R ${container_user}:${container_user} /var/www

ENTRYPOINT ["./configure.sh"]
