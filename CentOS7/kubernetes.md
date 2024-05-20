# 1. Master 설치
## 1-1. 사전 작업
### 1-1-1 메모리 swap off
``` bash
swapoff -a
```

### 1-1-2 메모리 swapp off (재부팅 후 설정 초기화 방지)
``` bash
vi /etc/fstab
```

``` bash
# 이 줄을 주석처리 해준다.
# /dev/mapper/centos-swap swap swap defaults 0 0
```

### 1-1-3 swap 영역 확인
``` bash
free -h
```

실행 결과
``` bash
              total        used        free      shared  buff/cache   available
Mem:           15Gi       255Mi        13Gi       773Mi       1.4Gi        13Gi
Swap:            0B          0B          0B
```

## 1-2 RKE2 를 이용해 쿠버네티스 설치
명령어 실행 전 root 계정 전환 필요
``` bash
su root
```

> 설치 공식 문서는 [RKE2 Quick Start](https://docs.rke2.io/install/quickstart) 참고

curl 명령어 실행
``` bash
curl -sfL https://get.rke2.io | sh -
```

실행 결과
``` bash
[INFO]  finding release for channel stable
[INFO]  using 1.28 series from channel stable
Rancher RKE2 Common (stable)                                                                                                                                            5.0 kB/s | 2.9 kB     00:00    

...

Rancher RKE2 1.28 (stable)      
Installed:
  rke2-common-1.28.9~rke2r1-0.el8.x86_64                               rke2-selinux-0.18-1.el8.noarch                               rke2-server-1.28.9~rke2r1-0.el8.x86_64                              

Complete!
```

## 1-3 RKE2 서비스 시작 및 등록
> ### config.yaml 설정 (선택사항)
> 
> ``` bash
> mkdir -p /etc/rancher/rke2/
> vi /etc/rancher/rke2/config.yaml
> ```
> 
> config.yaml 
> ``` yaml
> node-name: master1 # agent 이름
> ```

``` bash
systemctl enable rke2-server
```
``` bash
systemctl start rke2-server
```
``` bash
systemctl status rke2-server
```

실행 결과
``` bash
● rke2-server.service - Rancher Kubernetes Engine v2 (server)
   Loaded: loaded (/usr/lib/systemd/system/rke2-server.service; enabled; vendor preset: disabled)
   Active: active (running)
   ...
```

> ### 로그 확인 명령어
>
> ``` bash
> journalctl -u rke2-server -f
> ```

## 1-4 kubectl 환경변수 등록
root 계정 접속한 상태에서 실행
``` bash
mkdir ~/.kube/
cp /etc/rancher/rke2/rke2.yaml ~/.kube/config
export PATH=$PATH:/var/lib/rancher/rke2/bin/
echo 'export PATH=/usr/local/bin:/var/lib/rancher/rke2/bin:$PATH' >> ~/.bashrc
```

정상 실행 확인
``` bash
kubectl get nodes
```

실행 결과
``` bash
NAME      STATUS   ROLES                       AGE    VERSION
master1   Ready    control-plane,etcd,master   111s   v1.28.9+rke2r1
```

## 1-5 Woker 노드 등록에 필요한 token 확인
``` bash 
cat /var/lib/rancher/rke2/server/node-token
```

실행 결과
``` bash 
K10 ... 132::server:74 ... 39
```

# 2. Woker 설치
## 2-1. 사전 작업
### 2-1-1 메모리 swap off
``` bash
swapoff -a
```

### 2-1-2 메모리 swapp off (재부팅 후 설정 초기화 방지)
``` bash
vi /etc/fstab
```

``` bash
# 이 줄을 주석처리 해준다.
# /dev/mapper/centos-swap swap swap defaults 0 0
```

### 2-1-3 swap 영역 확인
``` bash
free -h
```

실행 결과
``` bash
              total        used        free      shared  buff/cache   available
Mem:           15Gi       255Mi        13Gi       773Mi       1.4Gi        13Gi
Swap:            0B          0B          0B
```

## 2-2 RKE2 를 이용해 쿠버네티스 설치
명령어 실행 전 root 계정 전환 필요
``` bash
su root
```

curl 명령어 실행
``` bash
curl -sfL https://get.rke2.io | INSTALL_RKE2_TYPE="agent" sh -
```

실행 결과
``` bash
[INFO]  finding release for channel stable
[INFO]  using 1.28 series from channel stable
Rancher RKE2 Common (stable)                                                                                                                        1.6 kB/s | 2.9 kB     00:01    
Rancher RKE2 1.28 (stable)                                                                                                                          2.4 kB/s | 4.6 kB     00:01    
Dependencies resolved.

...

Installed:
  rke2-agent-1.28.9~rke2r1-0.el8.x86_64                        rke2-common-1.28.9~rke2r1-0.el8.x86_64                        rke2-selinux-0.18-1.el8.noarch                       

Complete!
```

## 2-3 config.yaml 설정
``` bash
mkdir -p /etc/rancher/rke2/
vi /etc/rancher/rke2/config.yaml
```

config.yaml 
``` yaml
server: https://<server>:9345
token: <token from server node> # 마스터 노드 토큰
node-name: woker1 # agent 이름
```

> Master 노드 토큰 [1-5](#1-5-woker-노드-등록에-필요한-token-확인) 확인

## 2-4 RKE2 서비스 등록
``` bash
systemctl enable rke2-agent
```
``` bash
systemctl start rke2-agent
```
``` bash
systemctl status rke2-agent
```

실행 결과
``` bash
● rke2-agent.service - Rancher Kubernetes Engine v2 (agent)
   Loaded: loaded (/usr/lib/systemd/system/rke2-agent.service; disabled; vendor preset: disabled)
   Active: active (running)
   ...
```

> ### 로그 확인 명령어
> ``` bash
> journalctl -u rke2-agent -f
> ```

## 2-5 노드 확인 (Master)
``` bash
kubectl get nodes
```

실행 결과
``` bash
NAME      STATUS   ROLES                       AGE     VERSION
master1   Ready    control-plane,etcd,master   9m46s   v1.28.9+rke2r1
woker1    Ready    <none>                      46s     v1.28.9+rke2r1
```

# 3. Rancher 설치 (Master)
## 3-1 Helm 설치
``` bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

## 3-2 cert-manager 설치 (선택 사항)
> 공용 또는 개인 CA 서명 인증서를 사용하는 경우 **3-2 생략**
>
> `Rancher 생성 TLS 인증서` 또는 `Let's Encrypt 인증서` 사용시 **3-2 진행 필요**
>
>> 자세한 설명은 [여기](https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/install-upgrade-on-a-kubernetes-cluster#3-choose-your-ssl-configuration) 참고

> 설치 공식 문서는 [cert-manager docs](https://cert-manager.io/docs/installation/helm/) 참고

저장소 추가
``` bash
helm repo add jetstack https://charts.jetstack.io --force-update
```

저장소 업데이트
``` bash
helm repo update
```

kubectl 을 이용하여 CRDs 설치
``` bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.14.5/cert-manager.crds.yaml
```

cert-manager 설치
``` bash
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.14.5
```

실행 결과
``` bash
NAME: cert-manager
LAST DEPLOYED: Mon May 20 14:07:04 2024
NAMESPACE: cert-manager
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
cert-manager v1.14.5 has been deployed successfully!

In order to begin issuing certificates, you will need to set up a ClusterIssuer
or Issuer resource (for example, by creating a 'letsencrypt-staging' issuer).

More information on the different types of issuers and how to configure them
can be found in our documentation:

https://cert-manager.io/docs/configuration/

For information on how to configure cert-manager to automatically provision
Certificates for Ingress resources, take a look at the `ingress-shim`
documentation:

https://cert-manager.io/docs/usage/ingress/
```

## 3-3 Rancher 설치
> 설치 공식 문서는 [Install/Upgrade Rancher on a Kubernetes Cluster](https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/install-upgrade-on-a-kubernetes-cluster) 참고

저장소 추가 (stable)
``` bash
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
```

namespace 생성
``` bash
kubectl create namespace cattle-system
```

(아래는 letsEncrypt 예시)
``` bash
helm install rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=rancher.my.org \
  --set bootstrapPassword=admin \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=me@example.org \
  --set letsEncrypt.ingress.class=nginx
```

> `letsEncrypt 인증서` 외 `Rancher 생성 TLS 인증서` 또는 `공용 또는 개인 CA 서명 인증서` 를 사용하는 경우 [여기](https://ranchermanager.docs.rancher.com/getting-started/installation-and-upgrade/install-upgrade-on-a-kubernetes-cluster#5-install-rancher-with-helm-and-your-chosen-certificate-option) 참고

실행결과
<pre>
<code>
NAME: rancher
LAST DEPLOYED: Mon May 20 15:40:15 2024
NAMESPACE: cattle-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Rancher Server has been installed.

NOTE: Rancher may take several minutes to fully initialize. Please standby while Certificates are being issued, Containers are started and the Ingress rule comes up.

Check out our docs at https://rancher.com/docs/

If you provided your own bootstrap password during installation, browse to https://rancher.my.org to get started.

If this is the first time you installed Rancher, get started by running this command and clicking the URL it generates:

```
echo https://rancher.my.org/dashboard/?setup=$(kubectl get secret --namespace cattle-system bootstrap-secret -o go-template='{{.data.bootstrapPassword|base64decode}}')
```

To get just the bootstrap password on its own, run:

```
kubectl get secret --namespace cattle-system bootstrap-secret -o go-template='{{.data.bootstrapPassword|base64decode}}{{ "\n" }}'
```


Happy Containering!
</code>
</pre>

# 기타 명령어
## RKE2 삭제
``` bash
/usr/bin/rke2-uninstall.sh
```

> RKE2 Uninstall [공식 문서](https://docs.rke2.io/install/uninstall)

## helm 삭제
``` bash
rm -rf $HOME/.cache/helm
rm -rf $HOME/.config/helm
rm -rf $HOME/.local/share/helm
rm -rf /usr/local/bin/helm
```

> helm uninstalling [공식 문서](https://helm.sh/docs/faq/uninstalling/)

## kubectl 환경변수 삭제
``` bash
rm -rf ~/.kube/
```

> ### ~/.bashrc 파일 변경 필요
> export PATH=/usr/local/bin:/var/lib/rancher/rke2/bin:$PATH 구문 삭제