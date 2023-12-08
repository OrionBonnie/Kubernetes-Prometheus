FROM alpine:latest
COPY alertmanager /opt/
COPY alertmanager.yml /opt/conf/
RUN mkdir /opt/data
EXPOSE 9093
CMD ["/bin/sh","-c","/opt/alertmanager --config.file='/opt/conf/alertmanager.yml' --storage.path='/opt/data'"]
