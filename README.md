<div align="center"><strong>Prometheus Monitoring For Kubernetes Deployment</strong></div>

---

##### Requirement

<div>需要一个完整的 Kubernetes 集群，Kubernetes集群需安装好 CNI 插件 以及 Ingress， DNS服务自备。可选Nginx/Haproxy/LVS进行反代</div>

----

##### Global

| alertmanager.yaml | cadvisor.yaml |    grafana.yaml    | node-exporter.yaml | preset.yaml | prometheus.yaml |
| :---------------: | :-----------: | :----------------: | :----------------: | :---------: | :-------------: |
|     告警部署      | 容器监控部署  | 加尔法那可视化部署 | 节点指标采集器部署 |  预设部署   |    TSDB部署     |
||||| 首先Kubectl执行该文件 ||

<div>所有的配置都在 preset.yaml 中，执行部署前请修改 preset.yaml 中的配置</div>

<font color="red" ><strong>注意，部署时有节点亲和性，执行前请执行以下命令为你想要部署 prometheus 的节点打标签</strong></font>

```shell
kubectl label node 指定节点名称 node-role.kubernetes.io/monitoring=prometheus
```

--------

##### Grafana

<div>Grafana首先得需要添加数据源，Prometheus部署在集群内部，因此可以使用 Service名 来指定数据源
<strong>http://prometheus-svc:9090</strong></div>

以下为部分可用仪表盘ID，可通过在grafana中通过ID进行仪表盘导入

<div>Cadvisor's ID  <strong><font color="red">14205</font></strong></div>

<div>Node Exporter's ID  <strong><font color="red">1860</font></strong></div>

<div>ETCD's ID  <strong><font color="red">3070/10323</font></strong></div>

----

##### AlertManager

<div>告警媒介默认为空，需要手动在 <strong>preset.yaml</strong> 添加告警媒介</div>

<p>配置段位于名为 <strong>alertmanager-config</strong> 的 ConfigMap 中</p>

```shell
global:
      resolve_timeout: 1m
      smtp_smarthost: 'smtp.qq.com:465'       #smtp服务器地址，此处为qq的，可换成其他的
      smtp_from: 'xxxxxx@qq.com'              #来源标题，最好使用qq邮箱地址，可以自定义
      smtp_auth_username: 'xxxxxx@qq.com'     #qq邮箱地址
      smtp_auth_password: 'xxxxxx'            #qq邮箱的密钥，需开通生成
      smtp_hello: '@qq.com'
      smtp_require_tls: false
    route:
      group_by: ['alertname']
      group_wait: 10s
      group_interval: 10s
      repeat_interval: 2m
      receiver: 'xxxxxx'        #指定要发送到的接收者
    receivers:         #接收者配置
    - name: 'xxxxxx'            #接收者名称，需要和路由配置的接收者保持一致
      #webhook_configs:                  #WebHook配置，推荐使用钉钉机器人
      #- url: "http://127.0.0.1:5001/"   #WebHook地址
      email_configs:                     #接收邮箱配置
        - to: "xxxxxx"                   #告警接收者，添加邮箱地址
          send_resolved: true
    #inhibit_rules:                      #抑制默认关闭
```

<p>告警规则规定当配置的指标超过一定的阈值一段时间之后触发告警，告警规则可在 <strong>preset.yaml</strong>  中配置</p>

<p>配置段位于名为 <strong>prom-config-rule</strong> 的 ConfigMap 中</p>

---

##### Etcd

<p>etcd监控可选择开启，若需开启，请部署 kubernetes 目录下 <strong>etcd</strong> 目录中的内容 并 去除 preset.yaml中 Prometheus 对 etcd 的服务发现的注释</p>

<p>注意，若使用的是 kubeadm 部署的 K8S 集群，etcd的指标URL默认是暴露的，但其URL仅监听本地，需要手动修改 K8S 集群内 etcd 的 Pod 的指标监听URL</p>

<p>如果 etcd 是集群，部署在多台服务器中，请在 kubernetes 目录下的 etcd 目录修改 <strong>etcd.yaml</strong> 文件，在 subsets 配字段一栏添加所有成员的服务器信息</p>
