FROM alpine:latest
COPY node_exporter /opt/
WORKDIR /metrics
RUN mkdir procfs sysfs rootfs
ENV LAUNCHARGS="--web.listen-address=0.0.0.0:9100 \
                --path.procfs=/metrics/procfs \
                --path.sysfs=/metrics/sysfs \
                --path.rootfs=/metrics/rootfs \
                --collector.filesystem.mount-points-exclude=^/(dev|proc|sys|var/lib/docker/.+)($|/) \
                --collector.filesystem.fs-types-exclude=^(autofs|binfmt_misc|cgroup|configfs|debugfs|devpts|devtmpfs|fusectl|hugetlbfs|mqueue|overlay|proc|procfs|pstore|rpc_pipefs|securityfs|sysfs|tracefs)$"
EXPOSE 9100
CMD ["/bin/sh","-c","/opt/node_exporter ${LAUNCHARGS}"]
