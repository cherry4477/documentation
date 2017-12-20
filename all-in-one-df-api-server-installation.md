# DF all-in-one api server 安装说明(v1.0)

### 以下内容将指导你安装一个all-in-one的DF 1.0 api server。

*环境要求linux 系统，已安装docker，docker正常使用，可访问互联网（用于拉取镜像）。*

1. 用docker 起一个DF 1.0的all-in-one模式的api server。

*如果机器无法访问互联网，需把镜像导入到本机，并设置好名称和tag。*

```bash
docker run -d -p 8443:8443 --name "openshift-origin" \
--privileged --net=host \
-v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys:ro \ 
-v /var/lib/docker:/var/lib/docker:rw \
registry.dataos.io/openshift/ldp-origin:v1.1.6-ldp0.4.19 start
```
2. 进入到api server容器内部并添加集群管理员

```bash
docker exec -it openshift-origin bash

oc adm policy add-cluster-role-to-user cluster-admin admin

#退出容器
exit
```

3. 从api server容器复制oc客户端到本地
```bash
docker cp openshift-origin:/usr/bin/oc /usr/local/bin
```

4. 本地登录api server

```bash
oc login $masterip:8443 -u admin -p xxxx(密码任意,登录成功返回Login successful)
```
5. 创建永久的apiserver访问token

```bash
oc project default
oc create sa ocm
oc adm policy add-cluster-role-to-user cluster-admin \
system:serviceaccount:default:ocm
oc serviceaccounts get-token ocm -n default
```

6. 创建service-broker

```bash
oc new-servicebroker $sbname \
--username="$user" --password="$pass" --url=“$dpbroker”
```
7.  检查sb服务是否创建

```bash
oc get sb $sbbrokername
oc get bs -n openshift
```




