---

copyright:
  years: 2014, 2019
lastupdated: "2019-03-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}


# 使用受管 Istio 附加组件 (beta)
{: #istio}

Istio on {{site.data.keyword.containerlong}} 提供了 Istio 的无缝安装，可对 Istio 控制平面组件进行自动更新和生命周期管理，并可与平台日志记录和监视工具相集成。
{: shortdesc}

只需单击一次，您就可以获取所有 Istio 核心组件，执行其他跟踪、监视和可视化，并使 BookInfo 样本应用程序启动并运行。Istio on {{site.data.keyword.containerlong_notm}} 作为受管附加组件提供，因此 {{site.data.keyword.Bluemix_notm}} 会自动使所有 Istio 组件保持最新。

## 了解 Istio on {{site.data.keyword.containerlong_notm}}
{: #istio_ov}

### 什么是 Istio？
{: #istio_ov_what_is}

[Istio ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://www.ibm.com/cloud/info/istio) 是一种开放式服务网平台，用于连接、保护、控制和观察云平台（例如，{{site.data.keyword.containerlong_notm}} 中的 Kubernetes）上的微服务。
{:shortdesc}

将单一应用程序转换为分布式微服务体系结构时，会产生一系列新的挑战，例如如何控制微服务的流量，执行服务的灰度上线和金丝雀应用，处理故障，保护服务通信，观察服务以及在服务组中强制实施一致的访问策略。为了解决这些难题，您可以利用服务网。服务网提供了一个与语言无关的透明网络，用于连接、观察、保护和控制微服务之间的连接。通过 Istio，可管理网络流量，在微服务之间进行负载均衡，强制实施访问策略，验证服务身份等，从而掌握服务网的情况并对其进行控制。


例如，在微服务网中使用 Istio 可帮助您：
- 更好地了解集群中运行的应用程序
- 部署应用程序的金丝雀版本，并控制向其发送的流量
- 对微服务之间传输的数据启用自动加密
- 强制实施速率限制以及基于属性的白名单和黑名单策略

Istio 服务网由数据平面和控制平面组成。数据平面包含每个应用程序 pod 中的 Envoy 代理侧柜，用于调解微服务之间的通信。控制平面包含 Pilot、Mixer 遥测和策略以及 Citadel，用于在集群中应用 Istio 配置。有关其中每个组件的更多信息，请参阅 [`istio` 附加组件描述](#istio_components)。

### 什么是 Istio on {{site.data.keyword.containerlong_notm}} (beta)？
{: #istio_ov_addon}

Istio on {{site.data.keyword.containerlong_notm}} 作为受管附加组件提供，用于将 Istio 与 Kubernetes 集群直接集成。
{: shortdesc}

**该附加组件在集群中是什么样的？**</br>
安装 Istio 附加组件时，Istio 控制平面和数据平面会使用集群已连接到的 VLAN。配置流量在集群中的专用网络上流动，并且无需您在防火墙中打开任何其他端口或 IP 地址。如果使用 Istio 网关公开了 Istio 管理的应用程序，那么对应用程序的外部流量请求会在公用 VLAN 上流动。

**更新过程是如何运作的？**</br>
受管附加组件形式的 Istio 版本由 {{site.data.keyword.Bluemix_notm}} 进行测试，并核准在 {{site.data.keyword.containerlong_notm}} 中使用。此外，Istio 附加组件简化了 Istio 控制平面的维护工作，以便您可以专注于管理微服务。{{site.data.keyword.Bluemix_notm}} 通过自动对 {{site.data.keyword.containerlong_notm}} 支持的最新 Istio 版本应用更新，使所有 Istio 组件保持最新。  

如果需要使用最新版本的 Istio 或定制 Istio 安装，那么可以执行 [{{site.data.keyword.Bluemix_notm}} 快速入门教程 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/setup/kubernetes/quick-start-ibm/) 中的步骤来安装开放式源代码版本的 Istio。
{: tip}

**是否存在任何限制？** </br>
如果在集群中安装了[容器映像安全性强制实施程序许可控制器](/docs/services/Registry?topic=registry-security_enforce#security_enforce)，那么无法在集群中启用受管 Istio 附加组件。

<br />


## 我可以安装哪些内容？
{: #istio_components}

Istio on {{site.data.keyword.containerlong_notm}} 在集群中作为三个受管附加组件提供。
{: shortdesc}

<dl>
<dt>Istio (`istio`)</dt>
<dd>安装 Istio 的核心组件，包括 Prometheus。有关以下任何控制平面组件的更多信息，请参阅 [Istio 文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/concepts/what-is-istio)。
  <ul><li>`Envoy` 作为代理传递网中所有服务的入站和出站流量。Envoy 在应用程序容器所在的 pod 中部署为侧柜容器。</li>
  <li>`Mixer` 提供遥测收集和策略控制。<ul>
    <li>遥测 pod 启用了 Prometheus 端点，该端点用于从应用程序 pod 中的 Envoy 代理侧柜和服务中聚集所有遥测数据。</li>
    <li>策略 pod 强制实施访问控制，包括速率限制和应用白名单和黑名单策略。</li></ul>
  </li>
  <li>`Pilot` 为 Envoy 侧柜提供服务发现，并为侧柜配置流量管理路由规则。</li>
  <li>`Citadel` 使用身份和凭证管理来提供服务到服务认证和最终用户认证。</li>
  <li>`Galley` 验证其他 Istio 控制平面组件的配置更改。</li>
</ul></dd>
<dt>Istio extras (`istio-extras`)</dt>
<dd>可选：安装 [Grafana ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://grafana.com/)、[Jaeger ![外部链接图标](../icons/launch-glyph.svg "...")](https://www.jaegertracing.io/) 和 [Kiali ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://www.kiali.io/)，为 Istio 提供额外的监视、跟踪和可视化功能。</dd>
<dt>BookInfo 样本应用程序 (`istio-sample-bookinfo`)</dt>
<dd>可选：部署 [Istio 的 BookInfo 样本应用程序 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/examples/bookinfo/)。此部署包括基本演示设置和缺省目标规则，以便您可以立即试用 Istio 的功能。</dd>
</dl>

<br>
通过运行以下命令，可以随时查看集群中启用的 Istio 附加组件：
```
ibmcloud ks cluster-addons --cluster <cluster_name_or_ID>
```
{: pre}

`istio` 受管附加组件可以安装在免费集群中。要同时安装 `istio-extras` 和 `istio-sample-bookinfo` 附加组件，请创建至少具有两个工作程序节点的标准集群。
{: note}

<br />


## 安装 Istio on {{site.data.keyword.containerlong_notm}}
{: #istio_install}

在现有集群中安装 Istio 受管附加组件。
{: shortdesc}

**开始之前**</br>
* 确保您具有对 {{site.data.keyword.containerlong_notm}} 的 [{{site.data.keyword.Bluemix_notm}} IAM **写入者**或**管理者**服务角色](/docs/containers?topic=containers-users#platform)。
* [设定 CLI 的目标为现有 1.10 或更高版本的集群](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。
* 如果先前在集群中已使用 IBM Helm chart 或通过其他方法安装了 Istio，请[清除该 Istio 安装](#istio_uninstall_other)。

### 在 CLI 中安装受管 Istio 附加组件
{: #istio_install_cli}

1. 启用 `istio` 附加组件。
  ```
  ibmcloud ks cluster-addon-enable istio --cluster <cluster_name_or_ID>
  ```
  {: pre}

2. 可选：启用 `istio-extras` 附加组件。
  ```
  ibmcloud ks cluster-addon-enable istio-extras --cluster <cluster_name_or_ID>
  ```
  {: pre}

3. 可选：启用 `istio-sample-bookinfo` 附加组件。
  ```
  ibmcloud ks cluster-addon-enable istio-sample-bookinfo --cluster <cluster_name_or_ID>
  ```
  {: pre}

4. 验证是否在此集群中启用了安装的受管 Istio 附加组件。
  ```
  ibmcloud ks cluster-addons --cluster <cluster_name_or_ID>
  ```
  {: pre}

  输出示例：
  ```
  Name                      Version
  istio                     1.0.5
  istio-extras              1.0.5
  istio-sample-bookinfo     1.0.5
  ```
  {: screen}

5. 您还可以查看集群中每个附加组件的各个组件。
  - `istio` 和 `istio-extras` 的组件：确保部署了 Istio 服务及其相应的 pod。
    ```
   kubectl get svc -n istio-system
   ```
    {: pre}

    ```
    NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP                                                                    AGE
    grafana                  ClusterIP      172.21.98.154    <none>          3000/TCP                                                       2m
    istio-citadel            ClusterIP      172.21.221.65    <none>          8060/TCP,9093/TCP                                              2m
    istio-egressgateway      ClusterIP      172.21.46.253    <none>          80/TCP,443/TCP                                                 2m
    istio-galley             ClusterIP      172.21.125.77    <none>          443/TCP,9093/TCP                                               2m
    istio-ingressgateway     LoadBalancer   172.21.230.230   169.46.56.125   80:31380/TCP,443:31390/TCP,31400:31400/TCP,5011:31323/TCP,
                                                                              8060:32483/TCP,853:32628/TCP,15030:31601/TCP,15031:31915/TCP   2m
    istio-pilot              ClusterIP      172.21.171.29    <none>          15010/TCP,15011/TCP,8080/TCP,9093/TCP                          2m
    istio-policy             ClusterIP      172.21.140.180   <none>          9091/TCP,15004/TCP,9093/TCP                                    2m
    istio-sidecar-injector   ClusterIP      172.21.248.36    <none>          443/TCP                                                        2m
    istio-telemetry          ClusterIP      172.21.204.173   <none>          9091/TCP,15004/TCP,9093/TCP,42422/TCP                          2m
    jaeger-agent             ClusterIP      None             <none>          5775/UDP,6831/UDP,6832/UDP                                     2m
    jaeger-collector         ClusterIP      172.21.65.195    <none>          14267/TCP,14268/TCP                                            2m
    jaeger-query             ClusterIP      172.21.171.199   <none>          16686/TCP                                                      2m
    kiali                    ClusterIP      172.21.13.35     <none>          20001/TCP                                                      2m
    prometheus               ClusterIP      172.21.105.229   <none>          9090/TCP                                                       2m
    tracing                  ClusterIP      172.21.125.177   <none>          80/TCP                                                         2m
    zipkin                   ClusterIP      172.21.1.77      <none>          9411/TCP                                                       2m
    ```
    {: screen}

    ```
   kubectl get pods -n istio-system
   ```
    {: pre}

    ```
    NAME                                      READY   STATUS    RESTARTS   AGE
    grafana-76dcdfc987-94ldq                  1/1     Running   0          2m
    istio-citadel-869c7f9498-wtldz            1/1     Running   0          2m
    istio-egressgateway-69bb5d4585-qxxbp      1/1     Running   0          2m
    istio-galley-75d7b5bdb9-c9d9n             1/1     Running   0          2m
    istio-ingressgateway-5c8764db74-gh8xg     1/1     Running   0          2m
    istio-pilot-55fd7d886f-vv6fb              2/2     Running   0          2m
    istio-policy-6bb6f6ddb9-s4c8t             2/2     Running   0          2m
    istio-sidecar-injector-7d9845dbb7-r8nq5   1/1     Running   0          2m
    istio-telemetry-7695b4c4d4-tlvn8          2/2     Running   0          2m
    istio-tracing-55bbf55878-z4rd2            1/1     Running   0          2m
    kiali-77566cc66c-kh6lm                    1/1     Running   0          2m
    prometheus-5d5cb44877-lwrqx               1/1     Running   0          2m
    ```
    {: screen}

  - `istio-sample-bookinfo` 的组件：确保部署了 BookInfo 微服务及其相应的 pod。
    ```
    kubectl get svc -n default
    ```
    {: pre}

    ```
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)          AGE
    details                   ClusterIP      172.21.19.104    <none>         9080/TCP         2m
    productpage               ClusterIP      172.21.168.196   <none>         9080/TCP         2m
    ratings                   ClusterIP      172.21.11.131    <none>         9080/TCP         2m
    reviews                   ClusterIP      172.21.117.164   <none>         9080/TCP         2m
    ```
    {: screen}

    ```
    kubectl get pods -n default
    ```
    {: pre}

    ```
    NAME                                     READY     STATUS      RESTARTS   AGE
    details-v1-6865b9b99d-7v9h8              2/2       Running     0          2m
    productpage-v1-f8c8fb8-tbsz9             2/2       Running     0          2m
    ratings-v1-77f657f55d-png6j              2/2       Running     0          2m
    reviews-v1-6b7f6db5c5-fdmbq              2/2       Running     0          2m
    reviews-v2-7ff5966b99-zflkv              2/2       Running     0          2m
    reviews-v3-5df889bcff-nlmjp              2/2       Running     0          2m
    ```
    {: screen}

### 在 UI 中安装受管 Istio 附加组件
{: #istio_install_ui}

1. 在[集群仪表板 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/containers-kubernetes/clusters) 中，单击 V1.10 或更高版本集群的名称。

2. 单击**附加组件**选项卡。

3. 在 Istio 卡上，单击**安装**。

4. **Istio** 复选框已选中。要同时安装 Istio extras 和 BookInfo 样本应用程序，请选中 **Istio Extras** 和 **Istio 样本**复选框。

5. 单击**安装**。

6. 在 Istio 卡上，验证是否列出了启用的附加组件。

接下来，可以通过查看 [BookInfo 样本应用程序](#istio_bookinfo)来试用 Istio 的功能。

<br />


## 试用 BookInfo 样本应用程序
{: #istio_bookinfo}

BookInfo 附加组件 (`istio-sample-bookinfo`) 会将 [Istio 的 BookInfo 样本应用程序 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/examples/bookinfo/) 部署到 `default` 名称空间中。此部署包括基本演示设置和缺省目标规则，以便您可以立即试用 Istio 的功能。
{: shortdesc}

4 个 BookInfo 微服务包括：
* `productpage` - 调用 `details` 和 `reviews` 微服务来填充页面。
* `details` - 包含书籍信息。
* `reviews` - 包含书籍评论并调用 `ratings` 微服务。
* `ratings` - 包含伴随书籍评论的书籍排名信息。

`reviews` 微服务具有多个版本：
* `v1` 不调用 `ratings` 微服务。
* `v2` 调用 `ratings` 微服务，并将评级显示为黑色的 1 到 5 颗星。
* `v3` 调用 `ratings` 微服务，并将评级显示为红色的 1 到 5 颗星。

其中每个微服务的部署 YAML 都已修改，以便在部署之前将 Envoy 侧柜代理作为容器预注入到微服务的 pod 中。有关手动侧柜注入的更多信息，请参阅 [Istio 文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/setup/kubernetes/sidecar-injection/)。此外，BookInfo 应用程序已由 Istio 网关在公共 IP 入口地址上公开。请注意，虽然 BookInfo 应用程序可以帮助您入门，但该应用程序并不适合用于生产目的。

开始之前，请在集群中[安装 `istio`、`istio-extras` 和 `istio-sample-bookinfo` 受管附加组件](#istio_install)。

1. 获取集群的公共地址。
  * 标准集群：
      2. 设置 Ingress 主机。
            ```
            export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
            ```
        {: pre}

      3. 设置 Ingress 端口。
            ```
            export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
            ```
        {: pre}

      4. 创建使用 Ingress 主机和端口的 `GATEWAY_URL` 环境变量。
         ```
           export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT
           ```
         {: pre}

  * 免费集群：
      1. 设置 Ingress 主机。
            此主机是集群中任一工作程序节点的公共 IP 地址。
        ```
        export INGRESS_HOST=$(kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="ExternalIP")].address}')
        ```
        {: pre}

      2. 设置 Ingress 端口。
            ```
        export INGRESS_PORT=$(kubectl get svc istio-ingressgateway -n istio-system -o jsonpath='{.spec.ports[0].nodePort}')
        ```
        {: pre}

      3. 创建使用工作程序节点和 NodePort 的公共 IP 地址的 GATEWAY_URL 环境变量。
         ```
           export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT
           ```
         {: pre}

2. 对 `GATEWAY_URL` 变量运行 curl，以检查 BookInfo 应用程序是否正在运行。`200` 响应表示 BookInfo 应用程序使用 Istio 正常运行。
     
   ```
     curl -o /dev/null -s -w "%{http_code}\n" http://${GATEWAY_URL}/productpage
     ```
   {: pre}

3.  在浏览器中查看 BookInfo Web 页面。

    对于 Mac OS 或 Linux：
    ```
    open http://$GATEWAY_URL/productpage
    ```
    {: pre}

    对于 Windows：
    ```
    start http://$GATEWAY_URL/productpage
    ```
    {: pre}

4. 尝试多次刷新该页面。不同版本的评论部分会以红色星星、黑色星星和无星星进行循环。

### 了解具体情况
{: #istio_bookinfo_understanding}

BookInfo 样本演示了 Istio 的三个流量管理组件如何一起使用，将入口流量路由到应用程序。
{: shortdesc}

<dl>
<dt>`Gateway `</dt>
<dd>`bookinfo-gateway` - [Gateway ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway) 描述了 `istio-system` 名称空间中的负载均衡器 `istio-ingressgateway` 服务，此服务充当 BookInfo 的 HTTP/TCP 流量的流入入口点。Istio 将该负载均衡器配置为在网关配置文件中定义的端口上侦听对 Istio 管理的应用程序的入局请求。</br></br>要查看 BookInfo 网关的配置文件，请运行以下命令。
<pre class="pre"><code>kubectl get gateway bookinfo-gateway -o yaml</code></pre></dd>

<dt>`VirtualService`</dt>
<dd>`bookinfo` [`VirtualService` ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService) 定义规则，用于通过将微服务定义为 `destinations` 来控制如何在服务网内路由请求。在 `bookinfo` 虚拟服务中，请求的 `/productpage` URI 会路由到端口 `9080` 上的 `productpage` 主机。通过这种方式，对 BookInfo 应用程序的所有请求都会首先路由到 `productpage` 微服务，然后再调用 BookInfo 的其他微服务。</br></br>要查看应用于 BookInfo 的虚拟服务规则，请运行以下命令。
<pre class="pre"><code>kubectl get virtualservice bookinfo -o yaml</code></pre></dd>

<dt>`DestinationRule`</dt>
<dd>网关根据虚拟服务规则路由请求后，`details`、`productpage`、`ratings` 和 `reviews` [`DestinationRules` ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule) 会定义策略，用于在请求到达微服务时应用于该请求。例如，刷新 BookInfo 产品页面时，您看到的更改是 `productpage` 微服务随机调用 `reviews` 微服务的不同版本（`v1`、`v2` 和 `v3`）的结果。版本是随机选择的，因为 `reviews` 目标规则对微服务的各`子集`或指定的版本授予同等权重。流量路由到服务的特定版本时，虚拟服务规则将使用这些子集。</br></br>要查看应用于 BookInfo 的目标规则，请运行以下命令。
<pre class="pre"><code>kubectl describe destinationrules</code></pre></dd>
</dl>

</br>

接下来，可以[使用 IBM 提供的 Ingress 子域公开 BookInfo](#istio_expose_bookinfo)，或者对 BookInfo 应用程序的服务网进行[日志记录、监视、跟踪和可视化](#istio_health)。

<br />


## 对 Istio on {{site.data.keyword.containerlong_notm}} 进行日志记录、监视、跟踪和可视化
{: #istio_health}

要对 Istio on {{site.data.keyword.containerlong_notm}} 管理的应用程序进行日志记录、监视、跟踪和可视化，可以启动在 `istio-extras` 附加组件中安装的 Grafana、Jaeger 和 Kiali 仪表板，或者将 LogDNA 和 Sysdig 作为第三方服务部署到工作程序节点。
{: shortdesc}

### 启动 Grafana、Jaeger 和 Kiali 仪表板
{: #istio_health_extras}

Istio extras 附加组件 (`istio-extras`) 会安装 [Grafana ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://grafana.com/)、[Jaeger ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://www.jaegertracing.io/) 和 [Kiali ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://www.kiali.io/)。启动其中每个服务的仪表板，可为 Istio 提供额外的监视、跟踪和可视化功能。
{: shortdesc}

开始之前，请在集群中[安装 `istio` 和 `istio-extras` 受管附加组件](#istio_install)。

**Grafana**</br>
1. 为 Grafana 仪表板启动 Kubernetes 端口转发。
  ```
  kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000 &
  ```
  {: pre}

2. 要打开 Istio Grafana 仪表板，请转至以下 URL：http://localhost:3000/dashboard/db/istio-mesh-dashboard。如果安装了 [BookInfo 附加组件](#istio_bookinfo)，那么 Istio 仪表板会显示您多次刷新产品页面时生成的流量的度量值。有关使用 Istio Grafana 仪表板的更多信息，请参阅 Istio 开放式源代码文档中的 [Viewing the Istio Dashboard ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/tasks/telemetry/using-istio-dashboard/)。

**Jaeger**</br>
1. 为 Jaeger 仪表板启动 Kubernetes 端口转发。
  ```
  kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686 &
  ```
  {: pre}

2. 要打开 Jaeger UI，请转至以下 URL：http://localhost:16686。

3. 如果安装了 [BookInfo 附加组件](#istio_bookinfo)，那么可以从**服务**列表中选择 `productpage`，然后单击**查找跟踪**。这将显示多次刷新产品页面时生成的流量的跟踪。有关将 Jaeger 与 Istio 配合使用的更多信息，请参阅 Istio 开放式源代码文档中的 [Generating traces using the BookInfo sample ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/tasks/telemetry/distributed-tracing/#generating-traces-using-the-bookinfo-sample)。

**Kiali**</br>
1. 为 Kiali 仪表板启动 Kubernetes 端口转发。
  ```
  kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001 &
  ```
  {: pre}

2. 要打开 Kiali UI，请转至以下 URL：http://localhost:20001。

3. 对于用户名和口令，都输入 `admin`。有关使用 Kiali 来可视化 Istio 管理的微服务的更多信息，请参阅 Istio 开放式源代码文档中的 [Generating a service graph ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/tasks/telemetry/kiali/#generating-a-service-graph)。

### 使用 {{site.data.keyword.la_full_notm}} 设置日志记录
{: #istio_health_logdna}

通过将 LogDNA 部署到工作程序节点，以将日志转发到 {{site.data.keyword.loganalysislong}}，从而以无缝方式来管理每个 pod 中的应用程序容器和 Envoy 代理侧柜容器的日志。
{: shortdesc}

要使用 [{{site.data.keyword.la_full}}](/docs/services/Log-Analysis-with-LogDNA?topic=LogDNA-about)，请将日志记录代理程序部署到集群中的每个工作程序节点。此代理程序从所有名称空间（包括 `kube-system`）收集 pod 的 `/var/log` 目录中存储的扩展名为 `*.log` 的日志以及无扩展名文件。这些日志包括来自每个 pod 中的应用程序容器和 Envoy 代理侧柜容器的日志。然后，代理程序会将这些日志转发到 {{site.data.keyword.la_full_notm}} 服务。

首先，请执行[使用 {{site.data.keyword.la_full_notm}} 管理 Kubernetes 集群日志](/docs/services/Log-Analysis-with-LogDNA/tutorials?topic=LogDNA-kube#kube)中的步骤，为集群设置 LogDNA。




### 使用 {{site.data.keyword.mon_full_notm}} 设置监视
{: #istio_health_sysdig}

通过将 Sysdig 部署到工作程序节点，以将度量值转发到 {{site.data.keyword.monitoringlong}}，从而从运营角度了解 Istio 管理的应用程序的性能和运行状况。
{: shortdesc}

使用 Istio on {{site.data.keyword.containerlong_notm}} 时，受管 `istio` 附加组件会将 Prometheus 安装到集群中。集群中的 `istio-mixer-telemetry` pod 会使用 Prometheus 端点进行注释，以便 Prometheus 可以聚集 pod 的所有遥测数据。将 Sysdig 代理程序部署到集群中的每个工作程序节点时，系统会自动启用 Sysdig，以检测和抓取来自这些 Prometheus 端点的数据，以便在 {{site.data.keyword.Bluemix_notm}} 监视仪表板中显示这些数据。

所有 Prometheus 工作都已完成，因此留给您的唯一工作就是在集群中部署 Sysdig。

1. 通过执行[分析 Kubernetes 集群中部署的应用程序的度量值](/docs/services/Monitoring-with-Sysdig/tutorials?topic=Sysdig-kubernetes_cluster#kubernetes_cluster)中的步骤来设置 Sysdig。

2. [启动 Sysdig UI ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](/docs/services/Monitoring-with-Sysdig/tutorials?topic=Sysdig-kubernetes_cluster#kubernetes_cluster_step3)。

3. 单击**添加新仪表板**。

4. 搜索 `Istio`，并选择 Sysdig 的某个预定义 Istio 仪表板。

有关引用度量值和仪表板、监视 Istio 内部组件以及监视 Istio A/B 部署和金丝雀部署的更多信息，请查看 Sysdig 博客帖子 [How to monitor Istio, the Kubernetes service mesh ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://sysdig.com/blog/monitor-istio/)。请查找名为“Monitoring Istio: reference metrics and dashboards”的部分。

<br />


## 为应用程序设置侧柜注入
{: #istio_sidecar}

准备好使用 Istio 来管理您自己的应用程序了吗? 部署应用程序之前，必须先决定希望如何将 Envoy 代理侧柜注入到应用程序 pod 中。
{: shortdesc}

每个应用程序 pod 都必须在运行 Envoy 代理侧柜，这样微服务才能包含在服务网中。您可以确保侧柜以自动或手动方式注入到每个应用程序 pod 中。有关侧柜注入的更多信息，请参阅 [Istio 文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/setup/kubernetes/sidecar-injection/)。

### 启用自动侧柜注入
{: #istio_sidecar_automatic}

启用自动侧柜注入时，名称空间会侦听任何新的部署，并自动修改部署 YAML 以添加侧柜。在计划将要与 Istio 集成的多个应用程序部署到名称空间中时，对该名称空间启用自动侧柜注入。请注意，缺省情况下，在 Istio 受管附加组件中，未对任何名称空间启用自动侧柜注入。

要对名称空间启用自动侧柜注入，请执行以下操作：

1. 获取要将 Istio 管理的应用程序部署到的名称空间的名称。
  ```
        kubectl get namespaces
        ```
  {: pre}

2. 将名称空间标记为 `istio-injection=enabled`。
  ```
  kubectl label namespace <namespace> istio-injection=enabled
  ```
  {: pre}

3. 将应用程序部署到已标记的名称空间中，或者重新部署已在该名称空间中的应用程序。
  * 要将应用程序部署到已标记的名称空间中，请运行以下命令：
    ```
    kubectl apply <myapp>.yaml --namespace <namespace>
    ```
    {: pre}
  * 要重新部署已部署在该名称空间中的应用程序，请删除应用程序 pod，以便使用注入的侧柜对其进行重新部署。
    ```
    kubectl delete pod -l app=<myapp>
    ```
    {: pre}

5. 如果尚未创建服务来公开应用程序，请创建 Kubernetes 服务。应用程序必须由 Kubernetes 服务公开，才能包含为 Istio 服务网中的微服务。确保遵循 [pod 和服务的 Istio 需求 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/setup/kubernetes/spec-requirements/)。

  1. 为应用程序定义服务。
    ```
              apiVersion: v1
          kind: Service
          metadata:
            name: myappservice
          spec:
            selector:
              <selector_key>: <selector_value>
            ports:
       - protocol: TCP
             port: 8080
        ```
    {: codeblock}

    <table>
    <thead>
    <th colspan=2><img src="images/idea.png" alt="“构想”图标"/> 了解服务 YAML 文件的组成部分</th>
    </thead>
    <tbody>
    <tr>
    <td><code>selector</code></td>
    <td>输入要用于将运行应用程序的 pod 设定为目标的标签键 (<em>&lt;selector_key&gt;</em>) 和值 (<em>&lt;selector_value&gt;</em>) 对。</td>
     </tr>
     <tr>
     <td><code>port</code></td>
     <td>服务侦听的端口。</td>
     </tr>
     </tbody></table>

  2. 在集群中创建服务。确保服务部署到应用程序所在的名称空间中。
    ```
    kubectl apply -f myappservice.yaml -n <namespace>
    ```
    {: pre}

现在，应用程序 pod 已集成到 Istio 服务网中，因为这些 pod 将 Istio 侧柜容器与应用程序容器一起运行。

### 手动注入侧柜
{: #istio_sidecar_manual}

如果不想对名称空间启用自动侧柜注入，那么可以手动将侧柜注入到部署 YAML 中。应用程序在名称空间中与您不希望将侧柜自动注入到的其他部署一起运行时，请手动注入侧柜。

要将侧柜手动注入到部署中，请执行以下操作：

1. 下载 `istioctl` 客户机。
  ```
   curl -L https://git.io/getLatestIstio | sh -
   ```

2. 导航至 Istio 包目录。
  ```
  cd istio-1.0.6
  ```
  {: pre}

3. 将 Envoy 侧柜注入到应用程序部署 YAML 中。
  ```
  istioctl kube-inject -f <myapp>.yaml | kubectl apply -f -
  ```
  {: pre}

4. 部署应用程序。
  ```
  kubectl apply <myapp>.yaml
  ```
  {: pre}

5. 如果尚未创建服务来公开应用程序，请创建 Kubernetes 服务。应用程序必须由 Kubernetes 服务公开，才能包含为 Istio 服务网中的微服务。确保遵循 [pod 和服务的 Istio 需求 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/setup/kubernetes/spec-requirements/)。

  1. 为应用程序定义服务。
    ```
              apiVersion: v1
          kind: Service
          metadata:
            name: myappservice
          spec:
            selector:
              <selector_key>: <selector_value>
            ports:
       - protocol: TCP
             port: 8080
        ```
    {: codeblock}

    <table>
    <thead>
    <th colspan=2><img src="images/idea.png" alt="“构想”图标"/> 了解服务 YAML 文件的组成部分</th>
    </thead>
    <tbody>
    <tr>
    <td><code>selector</code></td>
    <td>输入要用于将运行应用程序的 pod 设定为目标的标签键 (<em>&lt;selector_key&gt;</em>) 和值 (<em>&lt;selector_value&gt;</em>) 对。</td>
     </tr>
     <tr>
     <td><code>port</code></td>
     <td>服务侦听的端口。</td>
     </tr>
     </tbody></table>

  2. 在集群中创建服务。确保服务部署到应用程序所在的名称空间中。
    ```
    kubectl apply -f myappservice.yaml -n <namespace>
    ```
    {: pre}

现在，应用程序 pod 已集成到 Istio 服务网中，因为这些 pod 将 Istio 侧柜容器与应用程序容器一起运行。

<br />


## 使用 IBM 提供的 Ingress 子域公开 Istio 管理的应用程序
{: #istio_expose}

在[设置 Envoy 代理侧柜注入](#istio_sidecar)，并将应用程序部署到 Istio 服务网后，可以使用 IBM 提供的 Ingress 子域将 Istio 管理的应用程序公开给公共请求。
{: shortdesc}

{{site.data.keyword.containerlong_notm}} ALB 使用 Kubernetes Ingress 资源来控制如何将流量路由到应用程序。但是，Istio 使用 [Gateways ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway) 和 [VirtualServices ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService) 来控制如何将流量路由到应用程序。网关会配置负载均衡器，用于充当 Istio 管理的应用程序的入口点。虚拟服务定义路由规则，以便将流量正确转发到应用程序微服务。

在标准集群中，IBM 提供的 Ingress 子域会自动分配给集群，以便您可通过公共方式公开应用程序。通过将缺省 {{site.data.keyword.containerlong_notm}} ALB 连接到 Istio Ingress 网关，可以利用此子域的 DNS 条目来公开 Istio 管理的应用程序。

您可以首先试用[使用 IBM 提供的 Ingress 子域公开 BookInfo 的示例](#istio_expose_bookinfo)，或者试用[通过连接 Istio 网关和 Ingress ALB 以公共方式公开您自己的 Istio 管理的应用程序](#istio_expose_link)。

### 示例：使用 IBM 提供的 Ingress 子域公开 BookInfo
{: #istio_expose_bookinfo}

在集群中启用 [BookInfo 附加组件](#istio_bookinfo)时，会创建 Istio 网关 `bookinfo-gateway`。该网关使用 Istio 虚拟服务和目标规则来配置负载均衡器 `istio-ingessgateway`，用于通过公共方式公开 BookInfo 应用程序。在以下步骤中，将创建 Kubernetes Ingress 资源，用于将入局请求转发到 {{site.data.keyword.containerlong_notm}} Ingress ALB，再转发到 `istio-ingessgateway` 负载均衡器。
{: shortdesc}

开始之前，请在集群中[启用 `istio` 和 `istio-sample-bookinfo` 受管附加组件](#istio_install)。

1. 针对集群，获取 IBM 提供的 Ingress 子域。如果要使用 TLS，还请记录输出中 IBM 提供的 Ingress TLS 私钥。
  ```
  ibmcloud ks cluster-get --cluster <cluster_name_or_ID> | grep Ingress
  ```
  {: pre}

  输出示例：
  ```
  Ingress Subdomain:      mycluster-12345.us-south.containers.appdomain.cloud
  Ingress Secret:         mycluster-12345
  ```
  {: screen}

2. 创建 Ingress 资源。{{site.data.keyword.containerlong_notm}} ALB 使用此资源中定义的规则，将流量转发到用于公开 Istio 管理的应用程序的 Istio 负载均衡器。
  ```
  apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: myingressresource
    namespace: istio-system
  spec:
    tls:
    - hosts:
      - bookinfo.<IBM-ingress-domain>
      secretName: <tls_secret_name>
    rules:
    - host: bookinfo.<IBM-ingress-domain>
      http:
        paths:
        - path: /
          backend:
            serviceName: istio-ingressgateway
            servicePort: 80
  ```
  {: codeblock}

  <table>
  <thead>
  <th colspan=2><img src="images/idea.png" alt="“构想”图标"/> 了解 YAML 文件的组成部分</th>
  </thead>
  <tbody>
  <tr>
  <td><code>tls.hosts</code></td>
  <td>要使用 TLS，请将 <em>&lt;IBM-ingress-domain&gt;</em> 替换为 IBM 提供的 Ingress 子域。请注意，`bookinfo` 已附加到 IBM 提供的 Ingress 子域的开头。缺省情况下，已为集群注册了 IBM 提供的通配符 Ingress 子域 <code>*.&lt;cluster_name&gt;.&lt;region&gt;.containers.appdomain.cloud</code>。</td>
  </tr>
  <tr>
  <td><code>tls.secretName</code></td>
  <td>将 <em>&lt;tls_secret_name&gt;</em> 替换为 IBM 提供的 Ingress 私钥的名称。IBM 提供的 TLS 证书是通配符证书，可用于通配符子域。<td>
  </tr>
  <tr>
  <td><code>host</code></td>
  <td>将 <em>&lt;IBM-ingress-domain&gt;</em> 替换为 IBM 提供的 Ingress 子域。请注意，`bookinfo` 已附加到 IBM 提供的 Ingress 子域的开头。</td>
  </tr>
  <tr>
  <td><code>serviceName</code></td>
  <td>请注意，服务名称为 <code>istio-ingressgateway</code>，因此 ALB 会将请求从此子域转发到 Istio 负载均衡器服务。</td>
  </tr>
  </tbody></table>

3. 创建 Ingress 资源。
  ```
  kubectl apply -f myingressresource.yaml -n istio-system
  ```
  {: pre}

4. 在 Web 浏览器中，打开 BookInfo 产品页面。
  - 如果启用了 TLS：
    ```
    https://bookinfo.<IBM-ingress-domain>/productpage
    ```
    {: codeblock}
  - 如果未启用 TLS：
    ```
    http://bookinfo.<IBM-ingress-domain>/productpage
    ```
    {: codeblock}

5. 尝试多次刷新该页面。ALB 会收到对 `http://bookinfo.<IBM-domain>/productpage` 的请求，并将其转发到 Istio 网关负载均衡器。由于是 Istio 网关来管理微服务的虚拟服务和目标路由规则，因此仍会随机返回 `reviews` 微服务的不同版本。

有关 BookInfo 应用程序的网关、虚拟服务规则和目标规则的更多信息，请参阅[了解具体情况](#istio_bookinfo_understanding)。

### 通过连接 Istio 网关和 Ingress ALB，以公共方式公开您自己的 Istio 管理的应用程序
{: #istio_expose_link}

通过连接 Istio 网关和 {{site.data.keyword.containerlong_notm}} ALB，将 IBM 提供的 Ingress 子域用于 Istio 管理的应用程序。以下步骤说明了如何设置 Istio 网关，如何创建虚拟服务以用于为 Istio 管理的服务定义流量管理规则，以及如何配置 {{site.data.keyword.containerlong_notm}} Ingress ALB，使其能将流量从 IBM 提供的 Ingress 子域定向到 `istio-ingessgateway` 负载均衡器。
{: shortdesc}

开始之前：
1. 在集群中[安装 `istio` 受管附加组件](#istio_install)。
2. 安装 `istioctl` 客户机。
  1. 下载 `istioctl`。
    ```
   curl -L https://git.io/getLatestIstio | sh -
   ```
  2. 导航至 Istio 包目录。
```
    cd istio-1.0.6
    ```
    {: pre}
3. [为应用程序微服务设置侧柜注入，将应用程序微服务部署到名称空间中，并为应用程序微服务创建 Kubernetes 服务，以便可以将这些服务包含在 Istio 服务网中](#istio_sidecar)。

要连接 Istio 网关和 {{site.data.keyword.containerlong_notm}} ALB，请执行以下操作：

1. 创建网关。此样本网关使用 `istio-ingessgateway` 负载均衡器服务来公开端口 80 以用于 HTTP。将 `<namespace>` 替换为部署了 Istio 管理的微服务的名称空间。如果微服务侦听的是 `80` 之外的端口，请添加该端口。有关网关 YAML 组成部分的更多信息，请参阅 [Istio 参考文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway)。
  ```
  apiVersion: networking.istio.io/v1alpha3
  kind: Gateway
  metadata:
    name: my-gateway
    namespace: <namespace>
  spec:
    selector:
      istio: ingressgateway
    servers:
    - port:
        name: http
        number: 80
        protocol: HTTP
      hosts:
      - '*'
  ```
  {: codeblock}

2. 在部署了 Istio 管理的微服务的名称空间中应用网关。
  ```
  kubectl apply -f my-gateway.yaml -n <namespace>
  ```
  {: pre}

3. 创建虚拟服务，此虚拟服务使用 `my-gateway` 网关，并为应用程序微服务定义路由规则。有关虚拟服务 YAML 组成部分的更多信息，请参阅 [Istio 参考文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService)。

  如果已经使用 {{site.data.keyword.containerlong_notm}} ALB 来公开微服务，那么 Istio 在 `istioctl` 客户机中提供了转换器工具，可帮助您将 Ingress 资源定义迁移到相应的虚拟服务。[`istioctl` 转换器工具 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/reference/commands/istioctl/#istioctl-experimental-convert-ingress) 会尽力将 Ingress 资源转换为虚拟服务。请注意，不会转换 Ingress 注释，因为 Istio 网关不使用 Ingress 注释。输出是 Istio Ingress 配置的起点，并可能需要进行一些修改。要使用此工具，请运行以下命令：`istioctl experimental convert-ingress -f <existing_ingress_resource>.yaml > my-virtual-service.yaml`
  {: tip}

  ```
  apiVersion: networking.istio.io/v1alpha3
  kind: VirtualService
  metadata:
    name: my-virtual-service
    namespace: <namespace>
  spec:
    gateways:
    - my-gateway
    hosts:
    - '*'
    http:
    - match:
      - uri:
          exact: /<service_path>
      route:
      - destination:
          host: <service_name>
          port:
            number: 80
  ```
  {: codeblock}

  <table>
  <thead>
  <th colspan=2><img src="images/idea.png" alt="“构想”图标"/> 了解 YAML 文件的组成部分</th>
  </thead>
  <tbody>
  <tr>
  <td><code>namespace</code></td>
  <td>将 <em>&lt;namespace&gt;</em> 替换为部署了 Istio 管理的微服务的名称空间。</td>
  </tr>
  <tr>
  <td><code>gateways</code></td>
  <td>请注意，指定的是 <code>my-gateway</code>，因此网关可以将这些虚拟服务路由规则应用于 Istio 负载均衡器。<td>
  </tr>
  <tr>
  <td><code>http.match.uri.exact</code></td>
  <td>将 <em>&lt;service_path&gt;</em> 替换为入口点微服务侦听的路径。例如，在 BookInfo 应用程序中，路径定义为 <code>/productpage</code>。</td>
  </tr>
  <tr>
  <td><code>http.route.destination.host</code></td>
  <td>将 <em>&lt;service_name&gt;</em> 替换为入口点微服务的名称。例如，在 BookInfo 应用程序中， <code>productpage</code> 充当调用其他应用程序微服务的入口点微服务。</td>
  </tr>
  <tr>
  <td><code>http.route.destination.port.number</code></td>
  <td>如果微服务侦听的是其他端口，请将 <em>&lt;80&gt;</em> 替换为该端口。</td>
  </tr>
  </tbody></table>

4. 在部署了 Istio 管理的微服务的名称空间中应用虚拟服务规则。
  ```
  kubectl apply -f my-virtual-service.yaml -n <namespace>
  ```
  {: pre}

5. 可选：要创建在流量路由到每个微服务后应用的规则（例如，用于将流量发送到一个微服务的不同版本的规则），可以创建并应用 [`DestinationRules` ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule)。

6. 创建 Ingress 资源文件。{{site.data.keyword.containerlong_notm}} ALB 使用此样本资源中定义的规则，将流量转发到用于公开 Istio 管理的微服务的 Istio 负载均衡器。
  ```
  apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: my-ingress-resource
    namespace: istio-system
  spec:
    rules:
    - host: <sub-domain>.<IBM-ingress-domain>
      http:
        paths:
        - path: /
          backend:
            serviceName: istio-ingressgateway
            servicePort: 80
  ```
  {: codeblock}

  <table>
  <thead>
  <th colspan=2><img src="images/idea.png" alt="“构想”图标"/> 了解 YAML 文件的组成部分</th>
  </thead>
  <tbody>
  <tr>
  <td><code>host</code></td>
  <td>将 <em>&lt;sub-domain&gt;</em> 替换为应用程序的子域，将 <em>&lt;IBM-ingress-domain&gt;</em> 替换为 IBM 提供的 Ingress 子域。可以通过运行 <code>ibmcloud ks cluster-get --cluster &lt;cluster_name_or_ID&gt;</code> 来查找集群的 IBM 提供的 Ingress 子域。您选择的子域会自动进行注册，因为缺省情况下已为集群注册了 IBM 提供的通配符 Ingress 子域 <code>*.&lt;cluster_name&gt;.&lt;region&gt;.containers.appdomain.cloud</code>。</td>
  </tr>
  <tr>
  <td><code>serviceName</code></td>
  <td>请注意，指定的是 <code>istio-ingressgateway</code>，因此 ALB 会将入局请求转发到 Istio 负载均衡器服务。</td>
  </tr>
  </tbody></table>

7. 在部署了 Istio 管理的微服务的名称空间中应用 Ingress 资源。
  ```
  kubectl apply -f my-ingress-resource.yaml -n <namespace>
  ```
  {: pre}

8. 在 Web 浏览器中，通过输入要访问的应用程序微服务的 URL，验证是否流量正在路由到 Istio 管理的微服务。
  ```
  http://<subdomain>.<IBM-ingress-domain>/<service_path>
  ```
  {: codeblock}

<br />


## 卸载 Istio on {{site.data.keyword.containerlong_notm}}
{: #istio_uninstall}

如果您已使用完 Istio，那么可以通过卸载 Istio 附加组件来清除集群中的 Istio 资源。
{:shortdesc}

请注意，`istio` 附加组件是 `istio-extras`、`istio-sample-bookinfo` 和 [`knative`](/docs/containers?topic=containers-knative_tutorial) 附加组件的依赖项。`istio-extras` 附加组件是 `istio-sample-bookinfo` 附加组件的依赖项。

### 在 CLI 中卸载受管 Istio 附加组件
{: #istio_uninstall_cli}

1. 禁用 `istio-sample-bookinfo` 附加组件。
  ```
  ibmcloud ks cluster-addon-disable istio-sample-bookinfo --cluster <cluster_name_or_ID>
  ```
  {: pre}

2. 禁用 `istio-extras` 附加组件。
  ```
  ibmcloud ks cluster-addon-disable istio-extras --cluster <cluster_name_or_ID>
  ```
  {: pre}

3. 禁用 `istio` 附加组件。
  ```
  ibmcloud ks cluster-addon-disable istio --cluster <cluster_name_or_ID> -f
  ```
  {: pre}

4. 验证是否此集群中已禁用所有受管 Istio 附加组件。输出中不应返回任何 Istio 附加组件。
  ```
  ibmcloud ks cluster-addons --cluster <cluster_name_or_ID>
  ```
  {: pre}

### 在 UI 中卸载受管 Istio 附加组件
{: #istio_uninstall_ui}

1. 在[集群仪表板 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cloud.ibm.com/containers-kubernetes/clusters) 中，单击 V1.10 或更高版本集群的名称。

2. 单击**附加组件**选项卡。

3. 在 Istio 卡上，单击“菜单”图标。

4. 卸载单个或所有 Istio 附加组件。
  - 单个 Istio 附加组件：
    1. 单击**更新**。
    2. 清除要禁用的附加组件对应的复选框。如果清除附加组件，可能还会自动清除需要该附加组件作为依赖项的其他附加组件。
    3. 单击**更新**。这将禁用 Istio 附加组件，并且从此集群中除去这些附加组件的资源。
  - 所有 Istio 附加组件：
    1. 单击**卸载**。这将禁用此集群中的所有受管 Istio 附加组件，并且将除去此集群中的所有 Istio 资源。

5. 在 Istio 卡上，验证已卸载的附加组件是否不再列出。

<br />


### 卸载集群中的其他 Istio 安装
{: #istio_uninstall_other}

如果先前在集群中已使用 IBM Helm chart 或通过其他方法安装了 Istio，请清除该 Istio 安装后，再启用集群中的受管 Istio 附加组件。要检查 Istio 是否已在集群中，请运行 `kubectl get namespaces`，然后在输出中查找 `istio-system` 名称空间。
{: shortdesc}

- 如果是使用 {{site.data.keyword.Bluemix_notm}} Istio Helm chart 安装的 Istio，请执行以下操作：
  1. 卸载 Istio Helm 部署。
    ```
    helm del istio --purge
    ```
    {: pre}

  2. 如果使用的是 Helm 2.9 或更低版本，请删除额外的作业资源。
    ```
      kubectl -n istio-system delete job --all
      ```
    {: pre}

- 如果是手动安装的 Istio 或使用的是 Istio 社区 Helm chart，请参阅 [Istio 卸载文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/setup/kubernetes/quick-start/#uninstall-istio-core-components)。
* 如果先前已在集群中安装了 BookInfo，请清除这些资源。
  1. 将目录切换到 Istio 文件位置。
       ```
    cd <filepath>/istio-1.0.5
    ```
    {: pre}

  2. 删除集群中的所有 BookInfo 服务、pod 和部署。
    ```
    samples/bookinfo/platform/kube/cleanup.sh
    ```
    {: pre}

## 接下来要做什么？
{: #istio_next}

* 要进一步探索 Istio，您可以在 [Istio 文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/) 中找到更多指南。
    * [智能路由 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/guides/intelligent-routing.html)：此示例显示如何使用 Istio 的各种流量管理功能，将流量路由到特定版本 BookInfo 的评论和评级微服务中。
    * [深入遥测 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://istio.io/docs/guides/telemetry.html)：此示例包含如何使用 Istio Mixer 和 Envoy 代理跨 BookInfo 微服务获取统一度量值、日志和跟踪。
* 参加 [Cognitive Class: Getting started with Microservices with Istio and IBM Cloud Kubernetes Service ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://cognitiveclass.ai/courses/get-started-with-microservices-istio-and-ibm-cloud-container-service/)。**注**：您可以跳过此课程中的 Istio 安装部分。
* 请查看博客帖子 [Istio ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://itnext.io/vistio-visualize-your-istio-mesh-using-netflixs-vizceral-b075c402e18e)，了解有关使用 Istio 可视化 Istio 服务网的信息。
