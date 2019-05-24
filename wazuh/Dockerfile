# Wazuh Docker Copyright (C) 2019 Wazuh Inc. (License GPLv2)
FROM phusion/baseimage:latest
ARG WAZUH_VERSION=3.9.1-1

ENV API_USER="foo" \
   API_PASS="bar"

ENV TEMPLATE_VERSION="elastic-7-2"

# Set repositories.
RUN set -x && echo "deb https://packages.wazuh.com/3.x/apt/ stable main" | tee /etc/apt/sources.list.d/wazuh.list && \
   curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | apt-key add - && \
   curl --silent --location https://deb.nodesource.com/setup_8.x | bash - && \
   echo "postfix postfix/mailname string wazuh-manager" | debconf-set-selections && \
   echo "postfix postfix/main_mailer_type string 'Internet Site'" | debconf-set-selections && \
   groupadd -g 1000 ossec && useradd -u 1000 -g 1000 -d /var/ossec ossec

RUN add-apt-repository universe && apt-get update && apt-get upgrade -y -o Dpkg::Options::="--force-confold" && \
   apt-get --no-install-recommends --no-install-suggests -y install openssl postfix bsd-mailx python-boto python-pip  \
   apt-transport-https vim expect nodejs python-cryptography mailutils libsasl2-modules wazuh-manager=${WAZUH_VERSION} \
   wazuh-api=${WAZUH_VERSION} && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && rm -f \
   /var/ossec/logs/alerts/*/*/*.log && rm -f /var/ossec/logs/alerts/*/*/*.json && rm -f \
   /var/ossec/logs/archives/*/*/*.log && rm -f /var/ossec/logs/archives/*/*/*.json && rm -f \
   /var/ossec/logs/firewall/*/*/*.log && rm -f /var/ossec/logs/firewall/*/*/*.json

# Adding first run script and entrypoint
COPY config/data_dirs.env /data_dirs.env
COPY config/init.bash /init.bash
RUN mkdir /entrypoint-scripts
COPY config/entrypoint.sh /entrypoint.sh
COPY config/00-wazuh.sh /entrypoint-scripts/00-wazuh.sh

# Sync calls are due to https://github.com/docker/docker/issues/9547
RUN chmod 755 /init.bash && \
    sync && /init.bash && \
    sync && rm /init.bash && \
    chmod 755 /entrypoint.sh && \
    chmod 755 /entrypoint-scripts/00-wazuh.sh


# Setting volumes
VOLUME ["/var/ossec/data"]
VOLUME ["/etc/postfix"]

# Services ports
EXPOSE 55000/tcp 1514/udp 1515/tcp 514/udp 1516/tcp

# Adding services
RUN mkdir /etc/service/wazuh && \
    mkdir /etc/service/wazuh-api && \
    mkdir /etc/service/postfix

COPY config/wazuh.runit.service /etc/service/wazuh/run
COPY config/wazuh-api.runit.service /etc/service/wazuh-api/run
COPY config/postfix.runit.service /etc/service/postfix/run

RUN chmod +x /etc/service/wazuh-api/run && \
    chmod +x /etc/service/wazuh/run && \
    chmod +x /etc/service/postfix/run

RUN pip install -U setuptools && \
    pip install docker

# Run all services
ENTRYPOINT ["/entrypoint.sh"]
