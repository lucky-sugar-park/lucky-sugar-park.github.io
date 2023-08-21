---
title: "Kubernetes istio + oauth2-proxy + keycloak 구성시 oauth2-proxy deployment 예시"
last_modified_at: 2023-08-16T10:05:02-05:00
categories:
  - Blog
tags:
  - istio
  - ingressgateway
  - oauth2-proxy
  - keycloak
---

> istio의 envoy-proxy 서버는 http header의 크기를 default로 60 kb, max 96 kb로 제한하고 있다.  
> 때문에 oauth2-proxy에서 upstream 주소로 전달하는 header 내용을 너무 많이 설정하게 되면  
> istio-envoy 서버에서 "431 Request Header Fields Too Large" 에러를 리턴하게 된다.

<br/>
#### envoy-proxy의 http header 크기를 늘리는 방법 
위에 기술했듯이 default는 60 kb이고 max 크기는 96 kb 이다.  
설정은 istio의 EnviyFilter를 통해서 할 수 있다. 아래는 yaml 예시이다.   
<br/>
```
apiVersion: networking.istio.io/v1alpha3  # v1alpha3가 최신이다
kind: EnvoyFilter
metadata:
  name: max-request-headers
  namespace: nginx  # 적용하고자 하는 네임스페이스
spec:
  workloadSelector:
    labels:
      app: istio-ingressgateway  # 이 정보로 istio ingressgateway와 연결함
  configPatches:
    - applyTo: NETWORK_FILTER
      match:
        context: ANY
        listener:
          filterChain:
            filter:
              name: envoy.filters.network_connection_manager
      patch:
        operation: MERGE
        value:
          typed_config:
            #"@type": "type.googleapis.com/envoy.config.filter.network.http_connection_manager.v2.HttpConnectionManager" => v2일 경우 세팅 (deplicated 됨_
            "@type": "type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager" # => v3일 경우 세팅
            max_request_headers_kb: 96 # 최대 96 kb이다.
```
<br>
#### oauth2-proxy deployment yaml 세팅예시
header에 많은 정보를 담으면 좋겠지만, 데이터 량이 늘어남으로 네트워크 성능에 안좋은 영향을 미칠 수 있으며   
무엇보다 istio envoy proxy 서버의 header 크기 제한에 걸려 에러를 유발할 수 있다.   
아래는 (431 에러를 유발하는 예시이다 - 너무 많은 정보를 헤더에 담도록 세팅한 경우)   
<br/>
```
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "63"
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-oauth2-proxy","namespace":"nginx"},"spec":{"selector":{"matchLabels":{"app":"nginx-oauth2-proxy"}},"template":{"metadata":{"labels":{"app":"nginx-oauth2-proxy"}},"spec":{"containers":[{"args":["--provider=oidc","--cookie-secure=false","--cookie-samesite=lax","--cookie-refresh=1h","--cookie-expire=4h","--cookie-name=auth","--set-authorization-header=true","--email-domain=*","--http-address=0.0.0.0:4180","--upstream=static://200","--skip-provider-button=true","--whitelist-domain=*.3d-excellence.com","--oidc-issuer-url=http://keycloak.dev.3d-excellence.com/realms/mytest","--cookie-domain=.3d-excellence.com","--cookie-secret=isparkiskingoftheworld!!","--client-id=nginx"],"env":[{"name":"OAUTH2_PROXY_CLIENT_ID","value":"nginx"},{"name":"OAUTH2_PROXY_CLIENT_SECRET","value":"nginx"}],"image":"quay.io/oauth2-proxy/oauth2-proxy:latest","name":"oauth2-proxy","ports":[{"containerPort":4180,"protocol":"TCP"}],"readinessProbe":{"httpGet":{"path":"/ping","port":4180},"periodSeconds":3}}]}}}}
  creationTimestamp: "2023-08-02T00:15:43Z"
  generation: 189
  name: nginx-oauth2-proxy
  namespace: nginx
  resourceVersion: "43959104"
  uid: ee538a12-233d-4b7c-864c-505d97a0826a
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: nginx-oauth2-proxy
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx-oauth2-proxy
    spec:
      containers:
      - args:
        - --provider=oidc
        - --cookie-secure=false
        - --cookie-samesite=lax
        - --cookie-refresh=1h
        - --cookie-expire=4h
        - --cookie-name=auth
        - --set-xauthrequest=true
        - --pass-authorization-header=true
        - --pass-access-token=true
        - --set-authorization-header=true
        - --skip-auth-strip-headers=false
        - --email-domain=*
        - --http-address=0.0.0.0:4180
        - --upstream=http://nginx.dev.3d-excellence.com
        - --skip-provider-button=true
        - --whitelist-domain=".dev.3d-excellence.com"
        - --cookie-domain=".dev.3d-excellence.com"
        - --oidc-issuer-url=https://keycloak.dev.3d-excellence.com/realms/mytest
        - --profile-url=https://keycloak.dev.3d-excellence.com/realms/mytest/protocol/openid-connect/userinfo
        - --login-url=https://keycloak.dev.3d-excellence.com/realms/mytest/protocol/openid-connect/auth
        - --redeem-url=https://keycloak.dev.3d-excellence.com/realms/mytest/protocol/openid-connect/token
        - --validate-url=https://keycloak.dev.3d-excellence.com/realms/mytest/protocol/openid-connect/userinfo
        - --cookie-domain=.3d-excellence.com
        - --cookie-secret=isparkiskingoftheworld!!
        - --client-id=nginx
        - --client-secret=nginx
        - --ssl-insecure-skip-verify
        - --request-logging=true
        - --auth-logging=true
        - --standard-logging=true
        - --reverse-proxy=false
        - --scope=openid
        env:
        - name: OAUTH2_PROXY_CLIENT_ID
          value: nginx
        - name: OAUTH2_PROXY_CLIENT_SECRET
          value: nginx
        image: quay.io/oauth2-proxy/oauth2-proxy:latest
        imagePullPolicy: Always
        name: oauth2-proxy
        ports:
        - containerPort: 4180
          protocol: TCP
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /ping
            port: 4180
            scheme: HTTP
          periodSeconds: 3
          successThreshold: 1
          timeoutSeconds: 1
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```
