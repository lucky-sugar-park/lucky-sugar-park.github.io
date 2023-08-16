---
title: "Kebernetes istio gateway 생성시 tls 설정방법 (argocd와 kubernetes-dashboard를 외부로 노출하기)"
last_modified_at: 2023-08-16T10:05:02-05:00
categories:
  - Blog
tags:
  - istio
  - ingressgateway
  - tls
  - argocd
  - kubernetes-dashboard
---

> istio ingressgateway 환경에서 어플리케이션에 사용될 gateway와 virtualservice를 생성하면서 (tls) 관련한 수 많은 시행착오를 겪었다.  
> (너무 당연한 것이라 그런지) gateway에서 tls 설정관련해서 설명해 놓은 자료들이 istio에 나와 있는 기본적인 예제 중심이라,  
> 내가 하고자 하는 환경에 맞는 것이 없었고, 자세하게 설명해 놓지 않아서 (아주 간단한 설정을 가지고) 엄청난 삽질을 했던 것 같다.  
> 시간이 조금만 지나도 기억해 내지 못하는 것도 있고, 다른 사람들에게 도움이 될 수 있을까 하여 기록은 남긴다
<br/>

#### 환경 
- Kubernetes version: 1.24
- Istio version: 1.18.2
- Argocd version: 2.04
- Kubernetes-dashboard version: v2.6.1

#### 대상 Application으로 argocd와 kubernetes-dashboard를 선택한 이유
istio ingressgateway 환경에서 어플리케이션용 gateway를 생성할 때에, 사용자 웹브라우저 ~ istio ingressgateway 구간은 보안상의 이유로 https를 적용해야 한다.  
istio ingressgateway 이후부터 실제 어플리케이션 서버까지는 http 방식을 적용해도 (보안)문제가 되지 않기 때문에,  
istio ingressgateway 이후부터는 HTTP 방식으로 처리해도 무리가 없다. (Argocd 서버와 같은 경우)  
하지만 Kubernetes Dashboard 같은 경우에는 어플리케이션 서버 자체에서 HTTPS 방식 이외의 접근을 허락하지 않는다.  
- 로그인을 우회하는 방법이 있기는 하지만, 이 방법은 너무 위험해서 사용을 추천하지 않는다
- 구글링을 해 보면 deployment.yaml에 pod 실행시 우회 옵션을 어렵지 않게 찾을 수 있음 (여기에서는 다루지 않을 것임-어차피 사용하면 안되기 때문임)
이런 경우에는 Kubernetes Dashboard 서버까지 HTTPS 터널링을 유지해야 하며  
<br/>
istio gateway를 생성할 때에 tls mode에 따라서 달리 동작하게 된다.
<br/>
이 두 가지 유형을 모두 테스트 해보기 위해서 argocd와 kubernetes-dashboard를 선택하였음  
<br/>
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/691c8b37-37d4-40e6-98b8-756592e38543)  

#### Argocd 설정
Argocd Gwateway를 생성하기 전에 Argocd가 Kubernetes Cluster에 설치되어 있어야 한다.   
설치 방법은 여러 가지가 있지만, 가장 간단하게는 ```kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml```   
명령어를 통해서 설치할 수 있다 (namespace는 argocd로 주었음)   

1. Gateway 생성
Gateway는 ingressgateway에 특정 호스트이름과 포트 (80, 443 등)로 요청이 오면 Mapping 되는 virtual service로 route하도록 룰을 등록하기 위해서 생성한다고 생각하면 된다.
아래의 예제는 argocd gateway를 생성하는 yaml 예제이다.  
<br/>  

```
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: argocd-gateway
  namespace: argocd
spec:
  selector:
    app: istio-ingressgateway # 여기에 istio-ingressgateway 이름을 줘야 한다.
  servers:
  - hosts:
    - argocd.example.com
    port:
      name: http
      number: 80
      protocol: HTTP
    tls:
      httpsRedirect: true  # 사용자가 http 방식으로 요청하게 되면 https 방식으로 (자동) redirection 한다
  - hosts:
    - argocd.example.com
    port:
      name: https
      number: 443
      protocol: HTTPS
    tls:
      credentialName: credentialName # 서버 Cert와 Key가 포함된 secret 이름이어야 한다
      mode: SIMPLE # SIMPLE로 설정하면, istio 자체에게 tls handshake를 진행하며, 성공할 경우에 argocd 서버로 http 방식의 통신을 진행한다
```  

2. Argocd Virtual Service 생성  
Gateway에서 tls 모드를 SIMPLE로 설정했기 때문에 Virtual Service로 넘어오는 것은 HTTP 패킷만 넘어오게 된다.  
(이는 Gateway에서 https에 필요한 Cert 인증과 Handshake를 모두 완료했기 때문이다-istio 공식문서에서는 이것을 tls termination이라고 부른다)   
따라서 별도의 tls용 route를 설정할 필요는 없다.  
아래의 코드를 참조한다  
<br/>  

```
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: argocd-route
  namespace: argocd
spec:
  gateways:
  - argocd-gateway
  hosts:
  - argocd.example.com
  http:
  - match:
    - uri:
        prefix: /   # istio 공식문서에서는 여러가지 방식의 match 유형을 설명하고 있다. 필요에 따라 다양하게 route할 수 있다.  
    route:
    - destination:
        host: argocd-server.argocd.svc.cluster.local
        port:
          number: 80
```
   
3. secret 생성
위 설정에서 tls 모드를 SIMPLE로 설정하게 되면 istio의 envoy proxy에 의해서 tls cert 인증 및 tls handshake가 진행될 텐데,  
이 때에 secret에 등록된 인증서가 잘못되었거나 secret리 istio와 동일한 namespace가 아니라면 handshake에 실패하게 된다.
secret 설정은 ```kubectl create secret <secert name> tls -n <namespace-istio와 동일해야함> --key <생성된 key 파일 패스> --cert <생성된 cert 파일 패스>```  명령어로 생성할 수 있다.  
<br/>
- key file과 cert file를 생성하는 방법은 [SSL서트 발급방법](https://www.lesstif.com/system-admin/openssl-root-ca-ssl-6979614.html)을 참고한다.  
- key file과 cert file를 base64로 Encoding된 pem 파일로 변경하고자 할 경우에는 [key와 crt를 pem으로 변경하는 방법](https://lahuman.github.io/cet_key_to_pem/)을 참고한다.
- pem 형태로 변경하지 않아도 secret를 등록할 수 있으니 참고한다.

4. Argocd 설정변경  
Argocd 같은 경우에는 기본적으로 HTTPS 방식을 지원한다.  때문에 HTTP 방식으로 접근하기 위해서는 서버 시작 시에 옵션을 줘야 한다.   
아래의 코드를 참조한다.
<br/>
```
template:  
  metadata:  
  ...  
  spec:  
    containers:  
    - command:  
      - argocd-server  
      - --staticassets  
      - /shared/app  
      - --insecure  # 이 부분을 추가해 줘야 함  
    image: quay.io/argoproj/argocd:v2.0.4
    ...
```

#### Kubernetes dashboard 설정
Kubernetes-Dashboard Gwateway를 생성하기 전에 Kubernetes-Dashboard가 Kubernetes Cluster에 설치되어 있어야 한다.   
설치 방법은 여러 가지가 있지만, 가장 간단하게는 ```kubectl apply -n kubernetes-dashboard -f [https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml](https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml)https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml``` 명령어를 통해서 설치할 수 있다 (namespace는 kubernetes-dashboard로 주었음)   
<br/>  

1. Gateway 생성
Gateway는 ingressgateway에 특정 호스트이름과 포트 (80, 443 등)로 요청이 오면 Mapping 되는 virtual service로 route하도록 룰을 등록하기 위해서 생성한다고 생각하면 된다.  
아래의 예제는 kubernetes-dashboard gateway를 생성하는 yaml 예제이다.   
<br/>  

```
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: kubernetes-dashboard-gateway
  namespace: kubernetes-dashboard
spec:
  selector:
    app: istio-ingressgateway
  servers:
  - hosts:
    - dashboard.example.com
    port:
      name: http
      number: 80
      protocol: HTTP
    tls:
      httpsRedirect: true  # https로만 접근할 수 있도록 httpsRedirect를 true로 설정함  
  - hosts:
    - dashboard.example.com
    port:
      name: https
      number: 443
      protocol: HTTPS
    tls:
      mode: PASSTHROUGH # https 통신을 Kubernetes-dashboard 서버와 직접 할 수 있도록 PASSTHROUGH로 설정함   
```  
<br/>

2. Kubernetes-dashboard Virtual Service 생성  
Gateway에서 http 포트의 httpsRedirect를 true로, tls 모드를 PASSTHROUGH로 설정했기 때문에 Virtual Service로 넘어오는 것은 HTTPS 패킷만 넘어오게 된다.  
(이는 Kubernetes-Dashboard 서버와 클라이언트간 https에 필요한 Cert 인증과 Handshake를 직접 수행한다는 것을 의미한다)   
아래의 코드를 참조한다   

```
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: kubernetes-dashboard-virtualservice
  namespace: kubernetes-dashboard
spec:
  gateways:
  - kubernetes-dashboard-gateway
  hosts:
  - dashboard.example.com
  http:  # 이 부분으로 route되는 경우는 없음 (Gatewway의 http 설정에서 httpsRedirect를 true로 설정했기 때문, 설명을 위해서 표시했음)
  - match:
    - uri:
        prefix: /
    route:
    - destination:
        host: kubernetes-dashboard.kubernetes-dashboard.svc.cluster.local
        port:
          number: 80
  tls:  # 이 부분으로만 route 됨 -> Kubernetes-dashboard 서버와 직접 https 통신을 수행함 - kubernetes-dashboard 서버는 https 방식만 지원함
  - match:
    - port: 443
      sniHosts:
      - dashboard.example.com
    route:
    - destination:
        host: kubernetes-dashboard.kubernetes-dashboard.svc.cluster.local
        port:
          number: 443
```
<br/>

3. secret 생성  
argocd 부분 참조 (argocd와 동일한 secret을 사용해도 상관 없음)   

<br/>
4. Kubernetes-Dashboard 설정변경  
Kubernetes-Dashboard 같은 경우에는 HTTPS 방식만을 지원하기 때문에 서버 인증서를 Kubenetes-Dashboard 서버가 제공할 수 있어야 하며,  
Cert 인증 후에 클라이언트 (웹 브라우저)와 tls handshake를 직접 처리해야 한다.  
<br/>

- 먼저 서버에서 인증서가 포함된 secret를 등록해야 한다. 기존에 등록된 "kubernetes-dashboard-certs"라는 이름의 secret를 제거한다   
  ```kubectl delete secret kubernetes-dashboard-certs -n kubernetes-dashboard```
- 삭제한 secret과 동일한 이름으로 서버 인증서가 포함된 secret를 생성한다.
  ```kubectl create secret kubernetes-dashboard-certs -n kubernetes-dashboard --cert <cert file path> --key <key file path>```
- Kubernetes-dashboard deployment에 key와 cert 정보를 추가해 준다.
  ```kubectl edit deployment kubernetes-dashboard -n kubernetes-dashboard```
아래의 코드를 참조한다.  
<br/>

```
apiVersion: apps/v1  
...  
spec:
  ...  
  template:
    containers:
    - args:  
      - --namespace=kubernetes-dashboard  
      - --auto-generate-certificates    
      - --tls-cert-file=/tls.crt  # 이 부분을 추가해 줘야 함   
      - --tls-key-file=/tls.key   # 이 부분을 추가해 줘야 함  
    image: kubernetesui/dashboard:v2.6.1
    ...
```
<br/>
