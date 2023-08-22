---
title: "Kubernetes istio envoy proxy 로그 보기"
last_modified_at: 2023-08-22T13:38:02-05:00
categories:
  - Blog
tags:
  - Kubernetes
  - istio envoy proxy log
---

<br/>

- 로그 보기  
  - ```kubectl logs -f <pod name> -n <namespace> -c istio-proxy``` (-f 는 tailing 옵션)

- 로그 레벨 변경
  - pod 접속 (```k exec -it -n <namespace> <pod> -c istio-proxy /bin/bash```)
  - 로그레벨 변경 (```curl -X POST http://localhost:15000/logging?level=trace```)
  - 위의 것을 한번에 할 수도 있음 (```kubectl exec -it -n <namespace> <pod-name> -c istio-proxy exec --   curl -X POST http://localhost:15000/logging?level=trace```)  
