---
title: "Kebernetes에서 istio와 keycloak를 활용해서 통합인증관리 구축"
last_modified_at: 2023-08-03T07:36:02-05:00
categories:
  - Blog
tags:
  - istio
  - oauth2-proxy
  - keycloak
---

> 프롤로그  
> Kubernetes 환경에서 Micro service 아키텍처를 적용하고 서비스 메쉬를 구축할 필요성이 발생했다.
> 많은 리서치와 고민 끝에 (직접 IDP 역할을 할 수도 있지만) 다양한 IDP와 손쉽게 연계할 수 있는 keycloak를 적용하기로 결정햐였다.
> (DEX도 고려하였으나, 오픈소스의 완성도나 기능 측면에서 Keycloak이 뛰어난 것으로 판단하였다)  
> 서비스 매쉬로 istio를 적용하는 것까지는 어렵지 않았는데,
> 서비스 매쉬의 중요한 기능 중의 하나인 oauth2와 SAML 방식의 인증을 모두 지원할 수 있는 시스템을 구축하는 것은 결코 쉽지 않았다.
> 무엇보다 사례를 찾기가 어려웠고, 설령 유사한 사례를 찾았어도 oauth2-proxy나 keycloak 버전이 달라서 에러가 발생하기도 하였다.
> istio나 keycloak, oauth2-proxy의 각각 단편적인 설명을 하는 글들은 많이 있었는데,
> 이 들을 엮어서 실제로 어플리케이션이 구동하도록 구성하는 글은 찾을 수 없었으며, 기록으로 남겨야 겠다는 생각이 들었다.

## 개요 
아래 그림은 "https://napo.io/posts/stio-oidc-authn-authz-with-oauth2-proxy/"에서 가져온 그림이다. istio + oauth2-proxy + keycloak 구성의 이해를 돕기 위함이다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/107d9c31-d14c-42ec-bf86-904c64bcf3d2)  

위 그림을 기반으로 kubernetes like하게 그림을 변경해 보았다.  
테스트를 위해서 별도의 어플리케이션을 구성해야 하지만, (귀차니즘에 의해서) nginx 서버로 이를 대체하였다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/160ea149-d3d2-4151-b488-af9149da63bb)  

istio + oauth2-proxy + keyclock의 사용자 인증 데이터 시퀀스는 아래 그림과 같다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/2877ac1b-71f4-48d8-8e12-df141e1bb18d)  

## 진행 순서
테스트를 위한 환경 구성 진행 순서는 다음과 같다.  
0. 전제조건  
1. 설치  
1.1 istio 설치 (v1.18)  
1.2 Keycloak 설치 (v21.1)  
1.3 OAuth2-proxy 설치 (v7.4)  
1.4 Nginx 설치  
2. 설정  
2.1 istio ingressgateway에 Route rule 적용 (Gateway와 Virtual Service 생성)  
2.2 Keycloak 설정 (github과 google의 Oauth2 서비스를 위한 클라이언트 등록 포함)  
2.3 Oauth2-proxy와 keycloak 연결  
2.4 Istio와 Oauth2-proxy 연결  
2.5 Istio에 AuthorizationPolicy 적용  
3. 테스트  

## 전제조건 
- Kubernetes cluster가 구성되어 있어야 함
  - 간단한 테스트이기 때문에 5~6 vcore 정도만 확보되면 됨
- 본 시험 구성은 AWS Kubernetes Cluster 환경에서 구성하였음
- Helm 3가 설치되어 있어야 함

## 설치
#### Istio 설치
istio 설치 방법은 크게 3가지로 요약할 수 있다.  
- istioctl 도구를 사용하는 방법
- helm 차트를 사용하는 방법
- Operator를 사용하는 방법
  
istioctl을 사용하는 방법이 가장 쉽고 확실한 방법이지만, 기업내의 방화벽, 프락시 등에 의한 제약이 있을 경우에는 (필요한 컨테이너 이미지들을 내부 리포지터리에 저장해 놓고) 헬름차트를 사용해야 한다. 구글링을 해보면 많은 헬름차트가 공개되어 있으니 적절한 것을 사용할 수 있다.  
여기에서는 istioctl를 사용하였다.  

istioclt 다운로드 및 설치
- curl -L https://istio.io/downloadIstio | sh - # 최신버전을 다운받음 (특정 버전을 원할 경우 istio 공식 홈페이지 참조)
- cd istio-1.18.2 # 위 명령을 실행하면 버전에 해당하는 디렉토리가 생성됨
- export PATH=$PWD/bin:$PATH
- istioctl install -set profile=demo -y # 데모 프로파일로 설치
- 네트워크에 문제가 없다면 1 ~ 2분 이내에 설치가 완료될 것인다.

아래 목록 모두 9개의 프로파일을 제공하는데, 약간의 설치요소 차이가 있다 (설치 이후에 수정 가능하다)  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/cbef154f-1cfa-422b-a6e0-d41971a6c700)  

- ambient.yaml
- default.yaml
- demo.yaml
- empty.yaml
- external.yaml
- minimal.yaml
- openshift.yaml
- preview.yaml
- remote.yaml

 설치가 완료되면 pod, service, deployment, replicaset 등이 설치된다.  
 ```kubectl get all -n istio-system``` 명령어로 확인해 볼 수 있다 (기본적으로는 istio-system이라는 이름의 namespace로 설치된다)  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/7b3d9d56-daf7-4de7-a864-de7a357ecbaf)  
  


#### keycloak 설치  
[여기](https://wlsdn3004.tistory.com/10)를 참조하였다.  
1) 네임스페이스 생성 (편의상 keycloak이라는 네임스페이스를 생성하였음)
   - ``` kubectl create ns keycloak```  
2) keycloak 네임스페이스에 istio 적용
   - ```kubectl label namespace keycloak istio-injection=enabled```  
3) 헬름 리포지터리 등록
   - ```helm repo add bitnami https://charts.bitnami.com/bitnami```  
   - keycloak 차트가 있는 헬름 리포지터리는 여러 곳이 있으며, 여기서는 bitnami를 사용하였음
4) 설정 값 변경
   - helm ckart에서 정의한 values.yaml 파일 안의 내용을 변경하는 것임
   - ```mkdir keycloak && cd keycloak```  
   - ```helm show values bitnami/keycloak > values.yaml```  
   - ```vi values.yaml```  
   - adminUser: "admin"과 adminPassword: "admin" 부분만 변경하였음 (keycloak 관리화면 접속시 필요함)  
<br/>
```  
...
auth:
  ## @param auth.adminUser Keycloak administrator user
  ##
  adminUser: admin
  ## @param auth.adminPassword Keycloak administrator password for the new user
  adminPassword: "admin"
...
...
service
  #annotations
  #  service.beta.kubernetes.io/aws-load-balance-schema: internet-facing
  #  service.beta.kubernetes.io/aws-load-balancer-type: nlb
  ## @param service.type Kubernetes service type
  ##
  # type: LoadBalancer ==> AWS에서 제공하는 nlb를 적용할 경우에는 LoadBalancer를 적용함
  type: ClusterIP # 여기서는 istio ingressgateway를 적용할 것이므로 ClusterIP를 설정하였음 (nlb는 비용이 들어감)  
```  

5) 설치
   - ```helm install keycloak --createnamespace -n keycloak bitnami/keycloak -f values.yaml```  
   - 정상적으로 설치되면 keycloak 접속 url과 관리자 계정/비번을 얼려주는 화면이 나타남
   - ```kubectl get all -n keycloak``` 명령어를 수행해보면 pod, service, statefulset이 생성된 것을 볼 수 있음  
   - AWS에서 제공하는 NLB를 적용할 경우에는 LoadBalancer와 nlb를 설정 (여기에서는 istio ingressgateway를 적용할 예정이므로 설정하지 않음)
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/8a9f4daf-4c72-4297-b5c2-0e60798ef020)    

<br/>
```
Keycloak can be accessed through the following DNS name from within your cluster:  
    - keycloak.keycloak.svc.cluster.local  
To access Keycloak from outside the cluster execute the following commands:  
1) Get the Keycloak URL by running these commands:
   - NOTE: It may take a few minutes for the LoadBalancer IP to be available. (80)
        You can watch its status by running 'kubectl get --namespace keycloak svc -w keycloak'
   - export HTTP_SERVICE_PORT=$(kubectl get --namespace keycloak -o jsonpath="{.spec.ports[?(@.name=='http')].port}" services keycloak)
   - export SERVICE_IP=$(kubectl get svc --namespace keycloak keycloak -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
   - echo "http://${SERVICE_IP}:${HTTP_SERVICE_PORT}/"
2) Access Keycloak using the obtained URL.
3) Access the Administration Console using the following credentials:
   - echo Username: admin
   - echo Password: $(kubectl get secret --namespace keycloak keycloak -o jsonpath="{.data.admin-password}" | base64 -d)
```  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/442cce0d-d311-45be-bfb4-07c41d38d61e) 


#### oauth2-proxy 설치
oauth2-proxy는 kubectl 도구를 이용해서 직접 설치 하였다.
1) keycloak.yaml이라는 이름의 파일을 생성 (파일이름은 편한데로 하면 됨)
   - ```vi keycloak.yaml```  

<br/><br/>
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-oauth2-proxy
  namespace: nginx #설치할 네임스페이스
Spec:
  selector:
    matchLabels:
      app: nginx-oauth2-proxy
  template:
    metadata:
      labels:
        app: nginx-oauth2-proxy
    spec:
      containers:
        - name: oauth2-proxy
          #image: quay.io/oauth2-proxy/oauth2-proxy:v6.1.1
          image: quay.io/oauth2-proxy/oauth2-proxy:latest
          args:
            - --provider=oidc
            - --cookie-secure=false
            - --cookie-samesite=lax
            - --cookie-refresh=1h
            - --cookie-expire=4h
            - --cookie-name=auth
            - --set-authorization-header=true
            - --email-domain=*
            - --http-address=0.0.0.0:4180
            - --upstream=static://200
            - --skip-provider-button=true
            - --whitelist-domain=*.example.com
            - --oidc-issuer-url=http://keycloak.example.com/realms/mytest # mytest는 keycloak에 등록된 realm 이름
            - --cookie-domain=.3d-excellence.com
            - --cookie-secret=16바이트또는24바이트문자
            - --client-id=nginx # keycloak에 등록된 클라이언트 아이디
          env:
            - name: OAUTH2_PROXY_CLIENT_ID
              value: nginx # keycloak에 등록된 클라이언트 이름
            - name: OAUTH2_PROXY_CLIENT_SECRET
              value: nginx # keycloak에 등록된 클라이언트 비번
          ports:
            - containerPort: 4180
              protocol: TCP
          readinessProbe:
            periodSeconds: 3
            httpGet:
              path: /ping
              port: 4180
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-oauth2-proxy
  namespace: nginx
spec:
  selector:
    app: nginx-oauth2-proxy
  ports:
    - name: http
      port: 4180
```

2) oauth2-proxy를 설치한다
   - ```kubectl install -f oauth2-proxy.yaml```
3) 설치가 완료되면 pod, service, deployment, replicaset이 생성된 것을 확인할 수 있다

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/fba6f297-b132-4106-95c7-ed7c16da716b)  


#### Nginx 설치
nginx는 istio를 이용한 사용자 인증이 잘 동작하는지의 여부를 확인하기 위함이며, kubectl을 통해서 설치하였다.
1) 아래는 nginx 설치를 위한 yaml 파일이다.  

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-nginx
  namespace: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-nginx
  template:
    metadata:
      labels:
        app: hello-nginx
    spec:
      containers:
        - name: hello-nginx
          image: nginx:latest
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: hello-nginx
  namespace: nginx
spec:
  selector:
    app: hello-nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

2) nginx 설치
   - ```kubectl install -f nginx.yaml```  
3) 설치가 완료되면 pod, service, deployment, replicaset이 생성된 것을 확인할 수 있다.  
   (아래 그림에서는 oauth2-proxy와 동일한 namespace로 생성했기 때문에 동일 네임스페이스에서 검색된다)

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/182a5c20-f293-48f9-b144-ade0bddd9708)  


## 설정
#### istio ingressgateway에 Route rule 적용 (Gateway와 Virtual Service 생성)
설치가 완료되었으면 istio를 이용해서 인증 메커니즘이 잘 동작하도록 설정해 주어야 한다.  
먼저 (istio ingressgateway에서) 사용자 요청을 적절한 곳에 (Nginx와 Keycloak) route할 수 있도록 Gateway와 Virtual Service를 생성해야 한다.  

Gateway와 Virtual service는 (편의상) Route 대상이 되는 Nginx와 keycloak namespace에서 각각 설정하였다. (yaml 파일 참조)

- keycloak-virtual-service.yaml   
<br/><br/>  
```
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: keycloak-gateway
  namespace: keycloak
spec:
  selector:
    app: istio-ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: keycloak-route
  namespace: keycloak
spec:
  hosts:
  - "keycloak.example.com"
  gateways:
  - keycloak-gateway
  http:
  - match:
    - uri:
        prefix: "/"
    route:
    - destination:
        host: keycloak.keycloak.svc.cluster.local  
```
<br/><br/>
- nginx-virtual-service.yaml  <br/>
<br/><br/>
```
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: nginx-gateway
  namespace: nginx
spec:
  selector:
    app: istio-ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "nginx.dev.3d-excellence.com"
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: nginx-route
  namespace: nginx
spec:
  hosts:
  - "nginx.example.com"
  gateways:
  - nginx-gateway
  http:
- match:
    - uri:
        prefix: "/oauth2"
    rewrite:
      uri: "/"
    route:
    - destination:
        host: nginx-oauth2-proxy.nginx.svc.cluster.local
        port:
          number: 80
  - route:
    - destination:
        host: hello-nginx.nginx.svc.cluster.local
        port:
          number: 80
```  

성공적으로 설치되면 keycloak, nginx 네임스페이스에 VirtualService와 Gateway가 생성된다.  
VirtualService와 Gateway는 ```kubectl get all -n keycloak```로 검색되지 않으며   
```kubectl get VirtualSerivce -n keycloak``` 으로 명령해야 검색이 된다.  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/19ca4316-3363-416f-8cb4-33045d6883cc)  


#### keycloak 설정
다음은 keycloak을 설정한다.  1) Test용 Realm을 하나 생성하고 2) Oauth2-proxy가 사용할 클라이언트를 생성한 후에  
3) 인증안된 사용자가 http://nginx.example.com에 접속할 때에 keycloak의 인증 페이지를 보여주고 사용자의 SNS 아이디 (google or github)을 통해서 인증을 시도할 수 있도록 로긴 화면을 구성한다.  

Keycloak의 관리자용 상세 매뉴얼은 [공식 홈페이지](https://www.keycloak.org/docs/latest/server_admin/)를 참조한다.  
1) Keycloak 관리자 로그인  
   - 웹 브라우저로 http://keycloak.example.com에 접속한다 (istio ingressgateway 설정에 의해서 keycloak 서비스에 접속됨)
   - 관리자 아이디와 비번을 입력하고 로그인을 수행함 (keycloak 설치 시에 values.yaml 파일에 설정했었던 아이비와 비번입력- admin/admin)
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/d3e282ba-d2ed-46c9-a0e4-31f98c2e804b)

아래 그림은 관리자 로그인에 성공했을 때의 초기 화면이다  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/8ccb07eb-0d0c-43b8-8eb4-c2e78deec773)  

2) 신규 realm 생성
   - realm 이름: mytest (편한 이름으로 하면 됨)
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/a1d302a2-906e-4f68-9f3a-a57e2de854f8)

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/a06e9432-1b02-40bd-8e24-62bd61f7ce26)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/6a4aac7b-1418-40ac-9c65-f1b831917cc4)  

3) 클라이언트 생성
   - Oauth2-proxy가 keycloak에 접근할 때에 사용할 클라이언트를 생성한다.
     - client ID: 편한 이름으로 생성하면 됨
     - rootURL, HomeURL, valid redirect URI 등을 입력함 (특히 valid redirect URI는 사용자 인증에 성공하면 redirect해주는 URI이다)
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/1d16560b-fac4-4fea-9553-2f8c6c84f1ad)

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/4a8f4e04-9cc5-441e-be4e-8bf6f4f01bcb)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/e8812bfa-696a-4fe7-847a-80468adf833b)  

4) 사용자 로그인 화면 옵션 설정
   - 사용자 로그인 화면에는 다양한 옵션이 있으며, Realm 별로 설정할 수 있다.   
   - 설정된 내용에 따라서, 해당 realm 에 해당하는 사용자의 로그인 화면이 달라진다.
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/10d5fb51-524a-44bc-9a72-34082ed06607)  

5) 외부 Social IDP 설정
   - 구글, github, facebook 등의 oauth2 IDP를 등록할 수 있음  
   - 여기에 등록된 Social IDP는 해당 Realm 사용자가 로그인 시에 로그인 화면에 링크로 표시된다.  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/3e3c5954-7fb7-4b59-ada2-9e6f124a08cc)

아래의 그림은 구글로부터 사용자 인증을 위한 구글 IDP를 등록하는 예시이다  
![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/d6b841f9-c84f-4ddd-846b-cf5ec87d1df3)  

#### Oauth2-proxy와 Keycloak 연결
- Oauth2-proxy 설치시에 등록한 keycloak 정보에 의해서 연결됨. 정보가 잘못되면 oauth2-proxy 설치에 실패하므로 kubectl로 확인 가능함
- 상세한 내용은 Oauth2-proxy 설치 부분을 참조한다.

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/23bf2bc8-ca9f-4163-ad12-f8cfef81792f)  

#### istio와 Oauth2-proxy 연결
istio가 설치된 namespace에서 istio라는 이름의 configmap 내에 extensionProvider로써 Oauth2-proxy를 등록해 주고 configmap을 업데이트 하면 됨
```kubectl apply -f yamlfilename.yaml```
<br/>
```
kind: ConfigMap
apiVersion: v1
metadata:
  name: istio
  labels:
    istio.io/rev: nginx
data:
  mesh: |-
    accessLogFile: /dev/stdout
    defaultConfig:
      discoveryAddress: istiod.istio-system.svc:15012
      proxyMetadata: {}
      tracing:
        zipkin:
          address: localhost:9411
    enablePrometheusMerge: true
    enableTracing: false
    outboundTrafficPolicy:
      mode: ALLOW_ANY
    trustDomain: cluster.local
    extensionProviders: # 이부분에서 oauth2-proxy 정보를 등록해 주어야 함
    - name: oauth2-proxy
      envoyExtAuthzHttp:
        service: nginx-oauth2-proxy.nginx.svc.cluster.local
        port: 80
        timeout: 1.5s
        includeHeadersInCheck: ["authorization", "cookie"]
        headersToUpstreamOnAllow: ["x-forwarded-access-token", "authorization", "path", "x-auth-request-user", "x-auth-request-email", "x-auth-request-access-token"] # 여기에 application으로 넘겨야 할 헤더 정보가 있으면 기술해 줘야 한다 (x-forwarded-access-token 헤더는 x_forwarded_access_token 이름으로 참조할 수 있음)
        headersToDownstreamOnDeny: ["content-type", "set-cookie"]
  meshNetworks: 'networks: {}'
```

#### istio에 AuthorizationPolicy 적용
마지막으로 istio에 AuthorizationPolicy를 적용해야 함
```kubectl apply -f yamlfilename.yaml```
<br/>
```
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: nginx=oauth2-policy
  namespace: istio-system # istio가 설치된 네임스페이스 이름
spec:
  selector:
    matchLabels:
      istio: ingressgateway
    action: CUSTOM
    provider:
      name: nginx-oauth2-proxy  # ConfigMap에서 정의된 extensionProvider 이름
    rules:
    - to:
      - operation:
          hosts: ["nginx.example.com", "other.example.com"] # 인증을 적용할 호스트 이름 목록
          notPaths: ["/auth/*"] # 인증예외 uri postfix rule
```

<br/>
## 테스트
1) http://nginx.example.com 접속 및 사용자 인증
   - nginx.example.com 도메인이 virtual service에 의해서 AuthorizationPolicy 적용을 받으므로 oauth2-proxy로 forward되고 oauth2-proxy에 의해서 keyclock 인증화면으로 redirection 되고 사용자 웹 브라우저에서 keycloak 로그인 화면이 나타남
   - 이 때에 oauth2-proxy는 특정 realm과 특정 클라이언트로 keycloak에 로그인 화면을 요청하게 되므로 keycloak에 등록된 mytest라는 reaml 의 로그인 화면이 나타남
2) 구글 또는 깃헙을 클릭한 후에 해당 로그인 화면에서 로그인 하면, 사용자 인증처리가 완료되며,
   사용자 화면은 nginx의 초기 화면으로 redirection 됨.

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/4c32bd8f-0366-4ef6-8875-97eb9ba49b81)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/37dae618-7bf9-4968-8e2c-a7dc85405b1f)  

## 추가정보
1) Keycloak, Oauth2-proxy, nginx 모두 https로 서비스 하고자 할 경우에
   - Ingressgateway를 통해서 서비스할 경우에는 keycloak과 nginx용으로 gateway와 virtualservice를 생성해 줘야 한다
   - 생성하는 방법은 [여기](./2023-08-16-istio-gateway-tls.md) 를 참조한다
2) 이 때에 Oauth2-proxy pod 생성시에 keycloak에 연결을 시도할 때에 (https로 통신할 경우에) keycloak의 인증서를 검증하고 tls 통신을 하게 되는데
   - 인증서 검증이 꽤 까다롭다 (URL: keycloak.example.com까지 모두 일치해야 하고 Self signed CA인 경우 Unkonwn authority 에러롤 띄우면서 pod 생성이 안됨)
   - Oauth2-proxy 구동 옵션 중에 --ssl-insecure-skip-verify를 추가함으로써 해결하였음

## 에필로그  
istio, oauth2-proxy, keycloak 관련해서 따로 따로 설명해 놓은 자료들은 제법 있었으나 kubernetes 환경에서 연결 및 동작을 설명하는 자료를 찾지 못했다.  
방법을 찾고 구성하는데에 꼬박 하루의 시간이 걸린 것 같다.  
누군에게게 도움이 되었으면 하는 바람으로 기록을 남겨본다.  

(추가적으로),  keycloak에 상당히 많은 기능들이 있었으며, 이 기능들을 잘 이해하면 훨씬 풍부하게 시스템을 구축할 수 있을 것 같은 느낌이다.   
기회가 된다면 keycloak 관리자용 기능을 deep dive해서 기록으로 남겨 놓고 싶다.  
