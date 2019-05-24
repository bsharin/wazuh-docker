FROM docker.elastic.co/logstash/logstash:7.1.0
RUN /usr/share/logstash/bin/logstash-plugin install logstash-filter-opnsensefilter
RUN /usr/share/logstash/bin/logstash-plugin install logstash-filter-geoip
RUN /usr/share/logstash/bin/logstash-plugin install logstash-filter-translate
RUN /usr/share/logstash/bin/logstash-plugin install logstash-filter-cidr
