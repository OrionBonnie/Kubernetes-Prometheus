FROM alpine:latest
COPY console_libraries /opt/
COPY consoles /opt/
COPY prometheus /opt/
COPY prometheus.yml /opt/config/
RUN mkdir /opt/data && mkdir /opt/rules
ENV LAUNCHARGS="--config.file=/opt/config/prometheus.yml \
--storage.tsdb.path=/opt/data \
--web.enable-lifecycle \
--web.console.libraries=/opt/console_libraries \
--web.console.templates=/opt/consoles"
EXPOSE 9090
CMD ["/bin/sh","-c","/opt/prometheus ${LAUNCHARGS}"]
