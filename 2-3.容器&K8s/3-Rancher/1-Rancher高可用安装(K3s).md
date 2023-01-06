## Rancher高可用安装

### 1.Rancher
-什么是 Rancher：https://docs.rancher.cn/docs/rancher2.5/overview/_index

### 2.单节点安装
- 使用docker可以轻松实现一个单节点的Rancher

```
# 单节点
docker run --privileged -d \
  --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  --name rancher \
  rancher/rancher:latest 

# 持久化数据
docker run --privileged -d \
  --restart=unless-stopped \
  -v /opt/rancher:/var/lib/rancher \
  -p 80:80 -p 443:443 \
  --name rancher \
  rancher/rancher:latest 


# 使用自有证书
docker run --privileged -d \
  --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -v /etc/rancher/ssl:/etc/rancher/ssl \
  --name rancher \
  rancher/rancher:latest \
  --no-cacerts 
```

### 2.高可用安装
- 官方推荐安装架构：https://docs.rancher.cn/docs/rancher2.5/overview/architecture-recommendations/_index
- ![](https://docs.rancher.cn/assets/images/rancher2ha-109862fddef6042942f103cfc94d892b.svg)
- 我们将Rancher安装在精简版的k3s中， k3s的安装过程：略
- 安装完k3s后可以使用helm进行Rancher的安装

#### 2.1安装Helm
- Helm：https://helm.sh/zh/docs/
- 脚本安装：curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
- 手动安装：
    + 下载需要的版本：`https://github.com/helm/helm/releases`
    + 解压：`tar  -xf  ....`
    + 移动：`mv linux-amd64/helm /usr/local/bin/helm`
    + 执行权限：`chmod  + /usr/local/bin/helm`
    + 验证：`helm version`


#### 2.2 Helm安装Rancher
- 首选创建命名空间：`kubectl create namespace cattle-system`
- 证书问题：推荐自己使用自有证书，没有证书的情况下使用`cert-manager`来生成管理维护证书
- 使用`cert-manager`部分参见文档：https://docs.rancher.cn/docs/rancher2.5/installation/install-rancher-on-k8s/_index
- 添加Rancher的Chart国内源源：
- `helm repo add rancher-stable http://rancher-mirror.oss-cn-beijing.aliyuncs.com/server-charts/stable`
- export KUBECONFIG=/etc/rancher/k3s/k3s.yaml

```
# 无自有证书
## Rancher生成方式
helm install rancher rancher-<CHART_REPO>/rancher \
  --namespace cattle-system \
  --set hostname=rancher.my.org \
  --set replicas=3

## letsEncrypt生成方式
helm install rancher rancher-latest/rancher \
  --namespace cattle-system \
  --set hostname=rancher.my.org \
  --set replicas=3 \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=me@example.org

# 自有证书
helm install rancher rancher-<CHART_REPO>/rancher \
 --namespace cattle-system \
 --set hostname=rancher.my.org \
 --set replicas=4 \
 --set ingress.tls.source=secret  \
 --set rancherImage=registry.cn-hangzhou.aliyuncs.com/rancher/rancher  \
 --set systemDefaultRegistry=registry.cn-hangzhou.aliyuncs.com
```

- 使用自有证书还需要自己手动或者Rancher界面添加该证书：https://docs.rancher.cn/docs/rancher2.5/installation/resources/tls-secrets/_index/

```
kubectl -n cattle-system create secret tls tls-rancher-ingress \
  --cert=tls.crt \
  --key=tls.key
```

#### 2.3添加域名解析
- 根据架构，在k3s外部应该有个四层代理来实现负载均衡，可以使用Nginx或者HAproxy或者是VIP的方式
- 添加域名解析指向该代理，这样就可以实现整体的高可用
  
#### 2.4验证参考命令
- pod是否正常：`kubectl -n cattle-system get pods`
- 查看日志：`kubectl -n cattle-system logs -f name`
- pod详情：`kubectl -n cattle-system describe pods`
