---
title: "Kebernetes에서 istio ingressgateway로 argocd 접근환경 구축"
last_modified_at: 2023-08-08T13:08:02-05:00
categories:
  - Blog
tags:
  - istio
  - ingressgateway
  - argocd
---

> istio ingressgateway를 통해서 argocd-server에 접근할 수 있는 환경을 구축한다.  
> Kubernetes Cluster 내의 많은 서비스들을 노출시켜서 사용자들이 접근할 수 있도록 구축할 필요가 있다.  
> 이렇게 하기 위해서는 보통은 service를 LoadBalance로 설정하여 public IP를 할당하도록 해야 한다.  
> 또는 AWS나 GCP 등과 같은 클라우드 서비스를 이용할 경우에는 자동으로 public IP와 연결된 도메인을 할당해 준다.  
> 하지만 이런 방식은 하면 외부 노출이 필요한 수 만큼의 public IP가 필요하게 되고,  
> 클라우드 서비스를 이용할 경우에는 그 만큼의 비용이 증가한다.  
>
> 때문에 외부로부터의 요청을 ingressgateway로 단일화하게 ingressgateway에서 요청 도메인 또는 URI 패턴 등에 근거하여  
> 내부 서비스로 route하도록 구성할 수 있다.
> istio를 활용하면, 이러한 일들은 손쉽게 처리할 수 있는데, 이번 글에서는 istio 기반으로 argocd 서비스를 연결하는 방법을 기술한다.

## 작업순서 
istio의 ingressgateway로 argocd-server 서비스를 (srcure ingress를 구축) 제공하기 위해서는 먼저 해당 도메인에 대한 인증서가 발급이 되어 있어야 하며, 
발급된 인증서가 istio에 secret로 등록이 되어 있어야 한다.  
(미리 등록하지 않을 경우에는 나중에 gateway를 생성할 때에 serverCertificate와 privateKey 파일을 yaml 파일에 등록해 줄 수도 있다)  
다음과 같은 순서로 작업해야 한다.  
<br/>  

0. argocd namespace에 label 넣어주기  
1. 도메인 인증서 발급 (cert 파일과 privateKey 파일이 있어야 함)  
2. 인증서를 istio (namespace: Defauylt로는 istio-system)에 등록한다.  
3. Argocd에 대한 Gateway와 VirtualService를 생성한다.   
4. argocd-service pod의 구동 옵션에 --insecure를 넣어준다.   
5. 웹 브라우저로 잡 접속되는지 확인한다. 

#### 네임스페이스에 라벨 넣어주기  
argocd namespcae에 다음과 같이 Label를 넣어준다.  
```kubectl label namespace argocd istio-injection=enabled```  

#### 도메인 인증서 준비  
[링크](https://www.lesstif.com/system-admin/openssl-root-ca-ssl-6979614.html)를 참조한다.  
위 링크에는 OpenSSL로 RootCA 생성 및 SSL 인증서를 발급하는 절차를 자세하게 기술하고 있다.  
이미 공인 RootCA 등의 기관으로부터 발급된 인증서가 있다면, 그걸 사용하면 된다.  
인증서를 생성한 이후에는 certificate 파일과 private key 파일 모두 pem이라는 확장자로 변환해 준다.  
[링크](https://lahuman.github.io/cet_key_to_pem/)에 pem확장자를 생성하는 방법을 기술하고 있다.  

#### 인증서 등록 
인증서가 준비되었다면 인증서를 istio에 secret으로 등록한다.  
인증서 키 파일과 (key 파일) 서트파일이 (crt 파일) 준비되어 있어야 한다.  
등록하는 방법은 [링크](https://zorba91.tistory.com/335)를 참조한다.  

#### Gateway와 VirtualService 생성  
인증서를 istio에 sercet로써 등록한 이후에는 외부로터의 (특정 도메인으로 요청이 왔을 때애) 실제 argocd 서비스로 routing 할 수 있도록  
Gateway와 VirtualService를 생성해 주어야 한다.  
아래의 샘플 yaml 파일을 참조한다.  
<br/>
```
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: argocd-gateway  # gateway 이름 (편한 이름을 주면 된다)
  namespace: argocd     # namespace
spec:
  selector:
    app: istio-ingressgateway # istio-ingressgateway에 등록된다
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    tls:  # secure로 하기 위해서는 httpsRedirection이 true로 정의되어야 한다
      httpsRedirect: true
    hosts:
    - "argocd.example.com"  # 등록된 도메인이름 (웹 브라우저에서 타이핑 하는 이름)
  - port:
      number: 443
      name: https
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: credentialName # istio에 등록시킨 credential 이름
      # serverCertificate: /etc/pki/tls/certs/myservercert.crt.pem pem 파일 위치
      # privateKey: /etc/pki/tls/private/myservercert.key.pem pem 파일 위치
    hosts:
    - "argocd.example.com"
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: argocd-route
  namespace: argocd
spec:
  hosts:
  - "argocd.example.com"
  gateways:
  - argocd-gateway # 연결하고자 하는 gateway
  http:
  - route:
    - destination:
        host: argocd-server.argocd.svc.cluster.local
        port:
          number: 80
```
<br/>
Kubernetes Cluster에 적용한다.  
```kubectl apply -f myyaml.yaml```

#### Argocd-server insecure로 설정  
argocd-server의 deployment에서 argocd-server 시작 옵션에 --issecure를 넣어줘서 tls 모드를 사용하지 않도록 한다.   
아래의 yaml 파일을 참조한다.  

```kubectl edit deployment argocd-server -n argocd``` 명령어로 수정할 수 있다.  
<br/>

```
...
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app.kubernetes.io/name: argocd-server
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app.kubernetes.io/name: argocd-server
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - podAffinityTerm:
              labelSelector:
                matchLabels:
                  app.kubernetes.io/name: argocd-server
              topologyKey: kubernetes.io/hostname
            weight: 100
          - podAffinityTerm:
              labelSelector:
                matchLabels:
                  app.kubernetes.io/part-of: argocd
              topologyKey: kubernetes.io/hostname
            weight: 5
      containers:
      - command:
        - argocd-server
        - --staticassets
        - /shared/app
        - --insecure  # 이 부분에서 tls 해제 옵션을 넣어준다. pod가 다시 시작할 때에 적용된다.  
        image: quay.io/argoproj/argocd:v2.0.4
        imagePullPolicy: Always
...
```
<br/>

저장하고 빠져 나오면 적용된다.  
``` kubectl delete <argocd-server-pod-name> -n argocd``` 명령어를 수행하면 기존 pod가 delete되고  
새로운 pod가 뜨면서 insecure 모드로 구동된다.  


## 참조문서
- [Secure ingress](https://istiobyexample.dev/secure-ingress/)  
- [istio gateway tls 흐름](https://do-hansung.tistory.com/85)  
