---
title: "Istio prometheus 환경에서 istio_request_* 이름의 istio standard Metrics를 나오도록 하는 방법"
last_modified_at: 2023-09-06T07:26:02-05:00
categories:
  - Blog
tags:
  - Kubernetes
  - Istio
  - Prometheus
  - Standard metrics
---

> Istio 공식 문서에 따르면 Istio 환경에서 Prometheus를 설치하면 istio_requests_* 이름의 standard metrics를 Default로 prometheus에서 수집할 수 있다고 나와있다.   
> 하지만 나의 환경에서는 istio_requests_로 시작되는 Metrics를 찾을 수 없었다. stackoverflow와 isito 공식 github에 올라온 수 많은 질문/답글들은 크게 도움이 되지 않았다.   
> 수 많은 시행착오 끝에 결국 metrics를 생성하는데에 성공했는데(역대급 삽질 ㅠㅠ, 눈물나옴), 이 경험을 기록으로 남긴다.

#### Istio, Prometheus에서 Metrics를 수집하는 방식   
먼저 [Istio와 Prometheus 진영에서의 metrics 수집과 관련한 이력을](https://superorbital.io/blog/istio-metrics-merging/) 참고하자.   
요약하면 두 진영 사이에서 mTLS 인증관련 이슈가 있었고, metrics 수집관련 약간의 견해 차이가 있었던 것 같다.   

prometheus에서는 prometheus.io/scrape 어노테이션의 값이 true인 pod에 대해서 수집을 시도한다.   
물론 pod에서 설정한 prometheus.io/path와 prometheus.io/port 포트 값을 이용한다.    

한편 Istio는 어플리케이션 POD 내의 Istio-proxy 컨테이너와 어플리케이션 컨테이너에 위 3가지 어노테이션을 자동으로 주입한다.  
주소는 /stats/prometheus이고 포트는 15020으로 자동 할당된다.   

#### Istio와 Prometheus 설치 방식   
Istio는 istioctl을 통해서 profile=demo로 설치를 진행했었고, Istio 공식 문서에 있는 [Prometheus 설치가드](https://istio.io/latest/docs/ops/integrations/prometheus/)에 따라   
설치 하였다 (```kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.18/samples/addons/prometheus.yaml```)   
- istio 버전: 1.18, prometheus 버전: 2.41.0

prometheus를 설치한 후에 prometheus 화면에서 metrics를 검색해 보니, 다른 것들은 모두 보이는데 istio_requests_* 이름으로 시작되는 metrics만 보이지 않았다.   
- 좀 깊이 들어가보니 이 metrics 들이 매우 유용하다는 것을 느꼈고
- 모니터링 대시보드를 구축할 때에 활용하면 좋겠다고 판단했다
- 물론 metrics를 생성하도록 (어플리케이션에서) 구현하는 방법도 있지만 istio를 활용하면 훨씬 간단하다.

#### Istio에서 Metrics를 Customization 하는 방식  
Metrics가 많으면 많을 수록 좋겠지만, 너무 많은 Metrics를 생성/추적하게 되면 성능에 좋지 않은 영향을 미칠 수 있으므로,   
적절하게 (필요한 정도로만) 조절할 필요가 있다.  
Istio에서는 모니터링을 위한 metrics를 Customization할 수 있는 여러 가지 방법을 제공하고 있다.   
그 중에서 Telemetry라는 CRD를 활용하는 방법으로 시도 해 보았다.   
[공식문서](https://istio.io/latest/docs/reference/config/telemetry/)에 가면 다양한 예제가 있으니 참조하면 된다.   

아래와 같이 설정해서 K8S에 적용하니 istio_requests_*로 시작되는 이름의 metrics가 생성되었다.   

```
apiVersion: telemetry.istio.io/v1alpha1
kind: Telemetry
metadata:
  name: my-metrics
  namespace: istio-system
spec:
  # no selector specified, applies to all workloads in the namespace
  metrics:
  - providers:
    - name: prometheus
```

위의 코드에서 핵심은 metrics의 provider로써 prometheus를 지정해 주었다는 것이다.   
사실 공식문서에 의하면 Telemetry 설정 없이 모든 standard metrics가 보여야 하는데, 그렇지 않은 걸로 봐서   
istio 버전별로 약간의 차이가 있고 (버그가 되었건 아니면 문서에서 누락되었건) 문서에 모두 반영하지 못한 듯 하다   
(실제 구글링해서 나온 결과들은 모두 1.18보다 예날 버전 이었음)   
