---
title: "Centos에서 kubernetes 설치하는 방법"
last_modified_at: 2023-06-09T09:31:02-05:00
categories:
  - Blog
tags:
  - Post Formats
  - readability
  - standard
---

centos 7 기준   
> 참고한 내용
- https://javacan.tistory.com/entry/k8s-install-in-centos7   
- https://gruuuuu.github.io/cloud/k8s-install/#   
- https://ohoroyoi.tistory.com/150   
- https://medium.com/finda-tech/overview-8d169b2a54ff   

1. 설치 준비사항   
1.1 hostname 설정   
    - hostnamectl set-hostname   
    - vim /etc/hosts   
      # kubernetes cluster   
        x.x.x.x kube-m   
        x.x.x.x kube-n01   
        x.x.x.x kube-n02   
1.2 docker 설치
    - yum install -y yum-utils device-mapper-persistent-data lvm2   
    - yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo   
    - yum install docker-ce   
    - when The kubelet is unhealthy due to a misconfiguration of the node in some way (required cgroups disabled) error  
      ```
      # cat << EOF > /etc/docker/daemon.json  
      {  
         "exec-opts": ["native.cgroupdriver=systemd"]  
      }  
      EOF  
      # systemctl restart docker  
      # systemctl start docker && systemctl enable docker  
      ```
1.3 방화벽 설정(비 활성화)  
    ```
    # systemctl stop firewalld  
    # systemctl disable firewalld  
    # systemctl status firewalld  
    ```
1.4  Update Iptables Settings (M,N)  
    ```
    # cat <<EOF >  /etc/sysctl.d/k8s.conf  
      net.bridge.bridge-nf-call-ip6tables = 1  
      net.bridge.bridge-nf-call-iptables = 1  
      EOF   
    #sysctl --system  
    ```
    - net.bridge.bridge-nf-call-iptables = 1 의 의미 :  
      centOS와 같은 리눅스 배포판은 net.bridge.bridge-nf-call-iptables의 default값이 0이다.  
      이는 bridge 네트워크를 통해 송수신 되는 패킷이 iptable 설정을 우회한다는 의미.  
      하지만 컨테이너의 네트워크 패킷이 호스트머신의 iptable 설정에 따라 제어되도록 하는 것이 바람직하며, 이를 위해서는 값을 1로 설정해야한다.  
1.5 Disable SELinux (M,N)  
    ```
    # setenforce 0  
    # sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config  
    ```
    - SELinux는 Linux의 보안을 강화해주는 보안 커널이며, application의 취약점으로 인한 해킹을 방지해주는 핵심 구성 요소입니다.  
      총 세가지 모드가 있습니다.  
      enforce (Default) : SELinux의 rule에 어긋나는 operation은 거부  
      permissive : rule에 어긋나는 동작이 있을 경우 audit log를 남기고 operation은 허용  
      disable : 제한없음  
      production서버일 경우 SELinux를 끄기 보다는 서비스가 SELinux하에서 잘 동작하도록 하는것이 바람직하며, 개발서버일 경우 Permissive모드로 진행하다가 추후에 enforce모드로 전환하는것이 편합니다.  
1.6 Disable SWAP (M,N)  
    - swapoff -a  
    - vim /etc/fstab
      # 아래 부분을 찾아서 주석처리 해줍니다.  
      # LABEL=SWAP-xvdb1 swap swap defaults    0 0 
      #/dev/mapper/centos-swap swap                    swap    defaults        0 0
      $ reboot  
2. kubernetes 설치  
2.1 Kubernetes Repo 추가 (M,N)  
    - cat <<EOF > /etc/yum.repos.d/kubernetes.repo  
      [kubernetes]  
      name=Kubernetes  
      baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64  
      enabled=1  
      gpgcheck=1  
      repo_gpgcheck=1  
      gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg  
      exclude=kube*   
      EOF
2.2 kubelet, kubeadm, kubectl 설치 (M,N)  
    - yum install -y kubelet-1.18.8* kubeadm-1.18.8* kubectl-1.18.8* --disableexcludes=kubernetes  
    - yum install -y kubelet-1.17.11* kubeadm-1.17.11* kubectl-1.17.11* --disableexcludes=kubernetes  
    - yum install -y kubelet-1.15.12* kubeadm-1.15.12* kubectl-1.15.12* --disableexcludes=kubernetes  
    - systemctl enable kubelet && systemctl start kubelet 
2.3 cgroupfs 관련 문제 
    1) docker가 systemd를 사용하도록 설정  
       - cat << EOF > /etc/docker/daemon.json  
         {  
           "exec-opts": ["native.cgroupdriver=systemd"]  
         }  
         EOF  
         -> docker 서비스 재시작: systemctl restart docker  
     2) 또는 update kubelet to use cgroupfs
        - sed -i -E 's/--cgroup-driver=systemd/--cgroup-driver=cgroupfs/' /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
        - systemctl restart kubelet.service   
2.3 Master 초기화 (M)  
    - kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=10.128.0.22   // 얘가 동작함
    - kubeadm init --pod-network-cidr=10.244.0.0/16 //--apiserver-advertise-address=34.22.22.22 -> 얘가 없어야 설치됨 (아마도 hostname을 찾지 못해서임)
    - solution for master node ini  
       -> error 발생시  
          = echo '1' > /proc/sys/net/ipv4/ip_forward  
          = swapoff -a
    - when error with port in use
       -> netstat -lnp | grep 1025
          = kill processid
       -> or kubeadm reset
    - when error with /etc/kubernetes/manifests/....yanl
       -> delete all files by manual
       -> or kubeadm reset
2.4. After master node installed
    - mkdir -p $HOME/.kube
    - sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    - sudo chown $(id -u):$(id -g) $HOME/.kube/config
    - copy token as below for worker node's join
2.5 belows are to be used for worker node installation
    kubeadm join 10.128.0.15:6443 --token womog9.fmxqma0t1kkwhsjc \
        --discovery-token-ca-cert-hash sha256:556e1212fb36981acfdfebfb18d1eb070f0d0cf52bda33d096760caa9aea0c39
    kubeadm join 192.168.0.21:6443 --token 90c718.0zds7hq3fjro8vnk \
        --discovery-token-ca-cert-hash sha256:7640c1de58e19ceb024811d15fb354b025cea751a30a8352ae2f258e6df0a0c4   
    discovery-token-ca-cert-hash 얻어오는 방법  
    openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
2.6 환경 변수 설정   
    root 계정을 이용해서 kubectl을 실행할 경우 다음 환경 변수를 설정한다.   
    - export KUBECONFIG=/etc/kubernetes/admin.conf   
    
2.7. show token list created with kubeadm init
   - kubeadm token list

2.8. To install CNI (in case of Flannel) -> 각 노드에 네트워크가 동작하기 위해서는 flannel을 반드시 설치 해야 함
   - kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

2.9. WEB Dashboard 설치  
   - kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml

2.9. To deploy into master node (ex: for development)  
   - kubectl taint nodes --all node-role.kubernetes.io/master-
   
2.10. Ingress Nginx install
   - kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.34.1/deploy/static/provider/baremetal/deploy.yaml
   - curl --resolve alicek106.example.com:80:13.209.83.234 alicek106.example.com/echo-hostname // hostname으로 uri 호출하는 방법
   
2.11. 동적 볼륨 프로비저닝
   - apiVersion: storage.k8s.io/v1
     kind: StorageClass
     metadata:
      name: standard
      annotations:
       storageclass.kubernetes.io/is-default-class: "true" // -> 얘를 해야 함. kubectl get storageclass 할 때에 default가 표시되면 성공한 것임  
     provisioner: kubernetes.io/gce-pd
     parameters:
      type: pd-standard
   - apiVersion: storage.k8s.io/v1
     kind: StorageClass
     metadata:
      name: fast
    provisioner: kubernetes.io/gce-pd
    parameters:
     type: pd-ssd
  - 관리자는 storageclass.kubernetes.io/is-default-class 어노테이션을 추가해서 특정 StorageClass 를 기본으로 표시할 수 있다
  - 기본 pv 생성 (이름을 달리해서 10개 정도 미리 생성해 놓으면 자동으로 binding 됨)
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-standard
spec:
  capacity:
    storage: 20Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: standard
  hostPath:
    path: /home/insoo67.park/

3. kubeflow 설치  
  - wget이 설치 안되어 있으면 설치함 yum install wget  
  - wget https://github.com/kubeflow/kfctl/releases/download/v1.1.0/kfctl_v1.1.0-0-g9a3621e_linux.tar.gz  
    tar -xvzf [압축 해제할 압축 아카이브 이름]  
    tar -cvzf xxx.tar.gz *  
    https://raw.githubusercontent.com/kubeflow/manifests/v1.0-branch/kfdef/kfctl_k8s_istio.v1.0.2.yaml  
    https://raw.githubusercontent.com/kubeflow/manifests/v1.1-branch/kfdef/kfctl_k8s_istio.v1.1.0.yaml  
    https://raw.githubusercontent.com/kubeflow/manifests/v1.0-branch/kfdef/kfctl_istio_dex.v1.0.2.yaml  
    https://raw.githubusercontent.com/kubeflow/manifests/v1.1-branch/kfdef/kfctl_istio_dex.v1.1.0.yaml  
    
  - kubeflow 설치 시 istio ingress controller에서 tokenrequest 관련 에러 발생하면서 정상구동 안될 때  
    /etc/kubernetes/manifests/kube-apiserver.yaml에서  
    --feature-gates="TokenRequest=true" 값을 추가해 준다  -> not works
    아래 부분은 확인해 봐야 함 (해보니 kubelet가 정상적 구동이 안 되었음)  
    - --service-account-issuer=kubernetes.default.svc
    - --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
    --> Above works but belows do not works
    --service-account-issuer=api  
    --service-account-signing-key-file=/etc/kubernetes/pki/sa.key  
    --service-account-api-audiences=api  
    
  - kfctl 설치
  - kfctl apply -V -f <yaml 파일>
  - central Dashboard에 접근하기 위해서는 istio로 forward해야 한다
    1) ingress nginx 설치 (80 포트)
    2) 아래와 같이 port-forward 수행
    export NAMESPACE=istio-system
    kubectl port-forward -n ${NAMESPACE} svc/istio-ingressgateway 8080:80
    3) 접근 
    4) Jupyter notebook은 좀 구림. 그래서 구글 aiplatform은 jupyterlab을 사용함  
       주파터랩을 사용할려면 별도의 이미지를 말아야 하는데 (커스텀 이미지) 그냥 귀찮아서 기존에 만들어 놓은 이미지를
       가져오는 것도 방법임: kangwoo/tensorflow-2.1.0-notebook-cpu:1.0.0  
       커스텀 이미지 만드는 방법은 "https://www.kangwoo.kr/2020/03/08/kubeflow-jupyter-notebooks-%EC%BB%A4%EC%8A%A4%ED%85%80-%EC%9D%B4%EB%AF%B8%EC%A7%80/"를 참조하면 됨  
       - http://ip:port
       Depending on how you’ve configured Kubeflow, not all UIs work behind port-forwarding to the reverse proxy.
       For some web applications, you need to configure the base URL on which the app is serving.

       For example, if you deployed Kubeflow with an ingress serving at https://example.mydomain.com and configured an application to be served at the URL  
       https://example.mydomain.com/myapp, then the app may not work when served on https://localhost:8080/myapp because the paths do not match
       --- below is ingress.yaml ---
       apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: kubeflowingress
  namespace: istio-system
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: istio-ingressgateway
          servicePort: 80
    4) kubeflow 설치 위해서는 pv 5개가 설치되어 있어야 함 (3개는 10GB이상, 2개는 20GB 이상)
    apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-1
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: standard
  hostPath:
    path: /home/insoo67_park/data/1
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-2
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: standard
  hostPath:
    path: /home/insoo67_park/data/2
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-3
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: standard
  hostPath:
    path: /home/insoo67_park/data/3
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-4
spec:
  capacity:
    storage: 20Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: standard
  hostPath:
    path: /home/insoo67_park/data/4
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-5
spec:
  capacity:
    storage: 20Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: standard
  hostPath:
    path: /home/insoo67_park/data/5
    
    5) storageclass 설치 필요
    apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
 name: standard
 annotations:
  storageclass.kubernetes.io/is-default-class: "true"
provisioner: kubernetes.io/local
parameters:
 type: pd-standard
 
   6) ingress  설치 후에 ip를 직접 지정해 주어서 service만 다시 설치 해야함  
      (GCP VM 생성할 때에 10...로 가는 IP가 지정되어야 하는데<external IP를 internal IP로 구글에서 연결해 주는 것으로 보임) 디폴트로 아무 IP로 지정됨  
      apiVersion: v1
kind: Service
metadata:
  labels:
    helm.sh/chart: ingress-nginx-2.11.1
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/version: 0.34.1
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/component: controller
  name: ingress-nginx-controller
  namespace: ingress-nginx
spec:
  type: NodePort
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
    - name: https
      port: 443
      protocol: TCP
      targetPort: https
  externalIPs:
  - 10.128.0.22
  selector:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/component: controller
 
5. check
kubectl get pods --all-namespaces

6. Pull from docker registry
kubectl create secret docker-registry regcred --docker-server=https://example.com --docker-username=aws --docker-password="abcd1234" --docker-email="aws@example.com"

7 other command
kubectl run kubia --image=lejewk/kubia --port=8080 --generator=run/v1
--image=lejewk/kubia 는 docker hub에 등록된 이미지를 기반으로 하겠다는 의미 입니다.
--port=8080 은 앱이 포트 8080 에서 수신 대기하고 있음을 나타냅니다.
--generator=run/v1 은 레플리케이션 컨트롤러를 생성한다는 의미 입니다.
kubectl expose rc kubil --type=LoadBalancer --name kubia-http

kubectl get pods
kubectl get pods -o wide -A
kubectl get service
kubectl get nodes
kubectl get replicationcontrollers

kubectl scale rc kubia --replicas=3
레플리카 수 증가시키기

kubectl get rc
kubectl get pods -o wide

kubectl describe pod kubia-r6kfr

kubectl scale deployment kubernetes-dashboard --replicas=0 -n service

// stop pod
kubectl scale --replicas=0 deployment/podname


// Install dashboard
# kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-rc6/aio/deploy/recommended.yaml
# kubectl apply -f ./xx.yaml
// Removal dashboard
# kubectl --namespace kube-system delete pod, deployment,service kubernetes-dashboard --> didn't work
// below command works very well espically when some pod get terminating status forever
kubectl delete pods kubernetes-dashboard-5f5f847d57-ld9ns  --namespace=kubernetes-dashboard --grace-period=0 --force

// Show everything about kubernetes-dashboard
kubectl get pod, secret,sa,role,rolebinding,services,deployments --namespace=kubernetes-dashboard | grep dashboard

kubectl delete deployment kubernetes-dashboard --namespace=kube-system 
kubectl delete service kubernetes-dashboard  --namespace=kube-system 
kubectl delete role kubernetes-dashboard-minimal --namespace=kube-system 
kubectl delete rolebinding kubernetes-dashboard-minimal --namespace=kube-system
kubectl delete sa kubernetes-dashboard --namespace=kube-system 
kubectl delete secret kubernetes-dashboard-certs --namespace=kube-system
kubectl delete secret kubernetes-dashboard-key-holder --namespace=kube-system

kubectl delete secret <secretname>
-> if serviceaccount is not deleted associated secret, then secret is not deleted.
If you delete serviceaccount, then related secret will also be deleted.

dashboard 기동 방법
- Proxy를 이용하는 방법
kubectl proxy --port=8001 &
kubectl proxy --port=[접속 포트] --address=[Kubernetes Dashboard URL] --accept-hosts='^*$' &

-> login
http://127.0.0.1:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
--> Kubernetes Login 인증키 생성
---> kubeconfig
---> Token
     1) create serviceaccount : kubectl create -f ~/serviceaccount
     2) create binding: kubectl create -f ~/cluster-role-binding
     3) put created token to dashboard: 
        show token: kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
        put token into dashboard UI 

==== token information created ====
Name:         admin-user-token-nxxfb
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: 60a387bc-815b-45e1-94b5-14536df5a70e

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IjNRQ0V6Yi1sUkd0R1lpX2tOeUhPRldIZHQ2Nk5sN1p6R1dwNnVnb0NtbVUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLW54eGZiIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiI2MGEzODdiYy04MTViLTQ1ZTEtOTRiNS0xNDUzNmRmNWE3MGUiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06YWRtaW4tdXNlciJ9.vfbwzD85KV3IHwY4kEOd-boX1gA4aQCbwAjEXt0P14AzTNCewsZmktv4d8HUvQ_zvCCTiDjIAh2fjXXyRxm0M3jqInjwsN-hja4nPt6znmfBPhMRiXznkPMukazE0Gk_OkocsAyakuC7eDmcYIZZa9OSQpSGMV2gmIlVS4J9rYD77ICTLa5i85iOHbRLQ3Cec1pJWD8fw6hp0fsfhE1noq-TGV-Hbpr4hHRwTpYuj9-0CZoUVbhOhUVr04HdMIHJ7qUzVBXwreRA0Mp1EdPo2xNJ79tiigIuhUp2yGGdhjS3glEzdTM5Q6Kq0Pi705-cyw1QPfwqf3wYZhgqqDkGSg
=== end of token ====



- NodePort를 이용하는 방법
kubectl -n kubernetes-dashboard edit service kubernetes-dashboard
-- in the editor
type: ClusterIP --> NodePort change

- API Server를 이용하는방법
