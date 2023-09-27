---
title: "Docker와 K8S 설치방법과 관련 명령어 모음"
last_modified_at: 2023-09-27T11:07:02-05:00
categories:
  - Blog
tags:
  - Kubernetes
  - Docker
---

##### ubuntu ssh root login ####
vi /etc/ssh/sshd_config
PermRootLogin yes
service ssh restart

##### k8s 정보 ####
1. master
   10.0.2.5, 10.0.3.15, 192.168.56.115
2. worker1
   10.0.2.7, 10.0.3.15, 192.168.56.116
3. worker2
   10.0.2.8, 10.0.3.15, 192.168.52.117 => jenkins
4. worker3
   10.0.2.9, 10.0.3.15, 192.168.52.118 => no used

// ip 주소확인
ifconfig, ip addr, hostname -i


#### microk8s 명령어 ####
microk8s kubectl ~~

#### github token #### 
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

#### hostname  변경하는 방법 ####
hostnamectl set-hostname abc

apt-get proxy
##########
설치 시에 프락시 설정을 하면 /etc/apt/apt.conf.d/90curtin-apiproxy에 기록됨
주석을 제거하거나 살리면 됨
Acquire::http::Proxy "http://username:password@70.10.15.10:8080/";
Acquire::https::Proxy "https://username:password@70.10.15.10:8081/";

#######
root 계정 비밀번호 지정
sudo passwd root
비번 입력
위와 같이 해야 su -가 먹는다.

ubuntu proxy 설정 (특정유저)
vi ~/.bashrc
export http_proxy=70.10.15.10:8080
export https_proxy=70.10.15.10:8082
export ftp_proxy=70.10.15.10:8080
export no_proxy=70.10.15.10:8080

source ~/.bashrc

모든 유저
vi /etc/environment
http_proxy="http://70.10.15.10:8080/"
https_proxy="http://70.10.15.10:8082"
ftp_proxy="http://70.10.15.10:8083/"
no_proxy="70.10.15.10:8080"

apt를 위한 proxy 설정 (advanced packaging tool)
vi /etc/apt/apt.conf.d/apt.conf
Acquire::http::Proxy "http://username:password@70.10.15.10:8080/";
Acquire::https::Proxy "https://username:password@70.10.15.10:8081/";

#######
httpie 설치
tree 설치
ifconfig 설치 (net-tools)
docker 설치

/etc/netplan/00~
network:
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: no
      addresses: [192.168.56.101/24]
      gateway4: 192.168.56.1
      nameservers:
        addresses: [203.241.132.85,204.241.135.135]
  version: 2

win+r > 실행 > \\59.29.225.161

win+r > 실행 > cmd > ipconfig

id: worker1
pw: ubuntu

우분투 > 바탕화면 > 우클릭 > open terminal
# su -
pw: ubuntu
# ip a
# ping google.com

### putty 설정

- category > terminal > keyboard > the backspace key > control-H
- category > terminal > bell > none
- category > window > 20000
- category > window > appearance > 16 
- category > session > hostname: 192.168.137.101
- category > session > saved session > k8s > save
- login as: root
- password: ubuntu

### chroot 실습

# chroot --version
# mkdir -p ~/newroot/{bin,lib,lib64}
# chroot ~/newroot /bin/bash
# ldd /bin/bash

# mkdir newroot/lib/x86_64-linux-gnu

# cp /lib/x86_64-linux-gnu/libtinfo.so.5 ~/newroot/lib/x86_64-linux-gnu
# cp /lib/x86_64-linux-gnu/libdl.so.2 ~/newroot/lib/x86_64-linux-gnu
# cp /lib/x86_64-linux-gnu/libc.so.6 ~/newroot/lib/x86_64-linux-gnu
# cp /lib64/ld-linux-x86-64.so.2 ~/newroot/lib64
# cp /bin/bash ~/newroot/bin
# ls newroot/lib/x86_64-linux-gnu/
# ls newroot/lib64/

# chroot ~/newroot /bin/bash
# pwd
# ls
# exit

# ldd /bin/ls

# cp /lib/x86_64-linux-gnu/libselinux.so.1 ~/newroot/lib/x86_64-linux-gnu
# cp /lib/x86_64-linux-gnu/libc.so.6 ~/newroot/lib/x86_64-linux-gnu
# cp /lib/x86_64-linux-gnu/libpcre.so.3 ~/newroot/lib/x86_64-linux-gnu
# cp /lib/x86_64-linux-gnu/libdl.so.2 ~/newroot/lib/x86_64-linux-gnu
# cp /lib64/ld-linux-x86-64.so.2 ~/newroot/lib64
# cp /lib/x86_64-linux-gnu/libpthread.so.0 ~/newroot/lib/x86_64-linux-gnu/
# cp /bin/ls ~/newroot/bin
# chroot ~/newroot /bin/bash
# pwd
# ls
# exit

# apt install -y tree
# tree newroot

# rwx rwx rwx
   u    g    o
r: read ch
w: write
x: execute

(term1)# chroot ~/newroot /bin/bash
(term2)# id: root, pw: ubuntu
(term1)# echo $$
-> 18795
(term2)# ps -ef | grep 18795


### namespaces 실습

# unshare --fork --pid --mount-proc=/proc /bin/sh
# ps
# exit

==================================================

(term1)# mkdir /tmp/mnt_ns
(term1)# unshare -m /bin/bash
(term1)# ll /proc/$$/ns/mnt
(term1)# mount -n -t tmpfs tmpfs /tmp/mnt_ns
(term1)# df

(term2)# unshare -m /bin/bash
(term2)# ll /proc/$$/ns/mnt
(term2)# df

(term1)# exit
(term2)# exit

==================================================

(term1)# uname -n
(term1)# unshare -u /bin/bash
(term1)# ll /proc/$$/ns/uts
(term1)# hostname happy.life.com
(term1)# uname -n

(term2)# ll /proc/$$/ns/uts
(term2)# uname -n

(term1)# exit

=================================================

(term1)# unshare -i /bin/bash
(term1)# ll /proc/$$/ns/ipc
(term1)# ipcmk -M 100
(term1)# ipcs -m

(term2)# ll /proc/$$/ns/ipc   
(term2)# ipcmk -M 100
(term2)# ipcs -m

(term1)# exit

=================================================

# su - worker1
# whoami
# unshare --map-root-user --user whoami
# unshare --map-root-user --user /bin/bash
# id

=================================================

# ip netns add guestnet
# ip netns exec guestnet ip link
# ip netns exec guestnet ip link set lo up
# ip netns exec guestnet ip link

# ip link add host type veth peer name guest
# ip link
# ip link set guest netns guestnet
# ip link
# ip netns exec guestnet ip link

# ip link set host up
# ip address add 2.2.2.1/24 dev host
# ip address

# ip netns exec guestnet ip link set guest up
# ip netns exec guestnet ip address add 2.2.2.2/24 dev guest
# ip netns exec guestnet ip add show dev guest

# ping 2.2.2.2
# ip netns exec guestnet ping 2.2.2.1
================================================

### cgroup 실습

# apt-get update
# apt-get install -y gcc
# nano a.c
i를 입력 > insert 확인
void main(){
	for(;;);
}
ctrl+x > y > enter
# gcc a.c
# ls 
# cp a.out b.out
# ./a.out &
# ./b.out &

# cd /sys/fs/cgroup/cpu 
# mkdir limit_50_percent
# cd limit_50_percent

# ls -l tasks cpu.shares
# echo $(pgrep a.out) > /sys/fs/cgroup/cpu/limit_50_percent/tasks
# echo 512 > /sys/fs/cgroup/cpu/limit_50_percent/cpu.shares

(term2)# top

# echo 256 > /sys/fs/cgroup/cpu/limit_50_percent/cpu.shares

(win)scp c:\a.out worker1@192.168.137.101:/home/worker1

# kill -9 $(pgrep a.out)
# kill -9 $(pgrep b.out)

=====================================================

### overlayFS

# cd ~
# mkdir overlayfs; cd overlayfs
# mkdir container image1 image2 work merge
# echo Hello, a  > image1/a
# echo Hello, b  > image1/b
# echo Hello, c  > image2/c
# ls image1
# ls image2
# mount -t overlay overlay -o lowerdir=image2:image1,upperdir=container,workdir=work merge
# df
# tree


# rm ./merge/a
# touch ./merge/d
# tree . -I work

# reboot

=====================================================

### docker installation
1) # apt-get update

2) # apt-get install -y  apt-transport-https ca-certificates curl software-properties-common
https를 사용해서 레포지토리를 사용할 수 있도록 필요한 패키지를 설치한다. 

3) # curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
Docker 공식 리포지토리에서 패키지를 다운로드 받았을 때 위변조 확인을 위한 GPG 키를 추가한다.

4) # apt-key fingerprint     
Docker.com 의 GPG 키가 등록됐는지 확인한다. 

5) # add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
Docker 공식 저장소를 리포지토리로 등록한다.

6) # grep docker /etc/apt/sources.list
deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable
저장소 등록정보에 기록됐는지 확인한다. 

7) # apt-get update    
리포지토리 정보를 갱신

8) # apt-get install -y docker-ce 
docker container engine 을 설치한다.

# docker version

# docker info
# docker system df

# docker container ps -a
# docker container create -it --name testos centos

# docker container ps -a
# docker container ls -a
=> # docker ps -a
# docker container ps
# docker container ls
=> # docker ps

# docker network ls
# docker image ls -a
# docker images

# docker (container) inspect testos
# docker run -it centos echo hello world
# docker ps -a

# docker create -it --name yumi00 centos
# docker ps -a

# docker start yumi00
# docker ps 
# docker stop yumi00
# docker ps -a
# docker start -ai yumi00
# pwd
# ps
# whoami
# hostname
# ip address
# exit
# docker ps -a
# docker start -ai yumi00
# ctrl + p + q
# docker ps -a

# docker stop yumi00
# docker ps -a

# docker run -it --name yumi01 centos
# ps -e
# hostname
# ctrl+p+q

# docker run -it --name yumi02 centos /bin/date
# docker ps -a

# docker run -d --name test1 centos(x)
# docker ps -a
# docker run -itd --name test2 centos(o)
# docker ps -a

# docker run -d --name yumi03 centos /bin/ping localhost
# docker ps -a
# docker logs yumi03

# docker attach yumi03 
ctrl+c
# docker ps -a
# docker start yumi03
# docker ps 
# docker logs yumi03

# docker exec -it yumi03 /bin/bash
(container)# ps -e
(container)# exit
# docker ps -a
# docker logs yumi03

# docker run -it --name yumi04 centos
(container)# ctrl+p+q
# docker ps
# docker attach yumi04
(container)# ps -e
(container)# ctrl+p+q

# docker run -itd --name yumi05 centos
# echo "hello world" > hostfile
# docker cp hostfile yumi05:/containerfile
# docker exec yumi05  ls -l /containerfile
# docker cp yumi05:/containerfile containerfile
# ls -al

# docker ps -a
# docker rm yumi05
# docker rm testos
# docker rm -f yumi05
# docker ps -a

# docker run -it --name test01 centos
(container)# useradd containeruser
(container)# ctrl+p+q
# docker diff test01

=================================================

### docker volume

# docker run -itd --name test00 centos
# docker ps
# docker inspect test00
# cd /var/lib/docker/overlay2/6bd6d12e9a3c81a201a1100bb734123c535080522a5ecd1d961a799b6bb1c132/diff
# touch containerfile
# cd
# docker exec test00 ls -l containerfile
# cd

container layer(rw)
----------------
image layer(ro)

# mkdir /tmp/hostdir
# mkdir /tmp/readonly
# echo writable > /tmp/hostdir/file1
# echo readonly > /tmp/readonly/file2
# cat /tmp/hostdir/file1
# cat /tmp/readonly/file2

# docker rm -f test00
# docker run -it -v /tmp/hostdir:/container_dir -v /tmp/readonly:/read_dir:ro --name test00 centos
(container)# cat /container_dir/file1
(container)# cat /read_dir/file2

(container)# echo hello > /container_dir/file1
(container)# echo hello > /read_dir/file2
(container)# ctrl+p+q
# docker inspect test00 | grep -A 20 "Mounts"
# cat /tmp/hostdir/file1
# cat /tmp/readonly/file2

# docker run -it -v /volume --name test02 centos
(container)# ls
(container)# df
(container)# cp /etc/passwd /volume
(container)# ls /volume
(container)# ctrl+p+q
# docker inspect test02 | grep -A 10 "Mounts"
# ls /var/lib/docker/volumes/e670abc03a5d9522cf493c456237fbf41607ab46c1e20fbebfe6c47fd67a9c7f/_data

# docker volume ls

# docker volume create persistvol
# docker volume ls
# docker inspect persistvol
# docker info

# docker run -it --name=vol_container -v persistvol:/container centos
(container)# touch /container/{a,b,c}
(container)# ls /container
(container)# ctrl+p+q

# docker run -it --name data_container --volumes-from vol_container centos
(container)# ls container
(container)# ctrl+p+q
# docker inspect data_container | grep -A 10 "Mounts"

=========================================================

### docker network

# docker inspect yumi01 | grep -i ipa
# docker inspect test2 | grep -i ipa
# ip a
# docker network ls

# brctl show docker0
# apt install bridge-utils
# brctl show docker0

# docker attach data_container

# docker rm -f $(docker ps -aq)
# docker ps -a
# docker run -d --name netbr00 --network=bridge -p 8080:80 nginx
# iptables -t nat -nL
# docker inspect netbr00

# docker ps
# docker images
# docker inspect netbr00 | grep -i ipa
# curl 172.17.0.2
(교육 장비의 web browser) 192.168.137.101:8080

=======================================================

### docker image

# docker image ls
# docker images
# docker search ubuntu
# docker search nginx
# docker pull centos:7
# docker pull httpd
# docker pull mariadb
# docker images
# docker inspect centos:latest
# docker inspect nginx

# docker images
# docker ps
# docker image rm nginx
# docker image rm -f nginx
# docker images
# docker pull nginx
# docker images

# docker image rm centos:7
# docker images

# docker rmi [mariadb_id]
# docker images

# docker image prune -a
# docker images

# docker search tensorflow

(web browser) hub.docker.com
sign up(회원가입) -> login
create repository -> repository name: 20220511web
# docker login

# docker tag nginx [id/repository:version]
# docker tag nginx ccamm1/20220511web:v1
# docker images
# docker push ccamm1/20220511web:v1

# docker images
# docker rmi ccamm1/20220511web:v1
# docker images
# docker search ccamm1
# docker pull ccamm1/20220420web:v1
# docker images

# docker tag ccamm1/20220803web:v1 ccamm1/20220707web
# docker images
# docker push ccamm1/20220707web:latest
(web browser)repository 확인
# docker search ccamm1/20220707web
# docker logout

# docker system df
# docker system prune -a
# docker ps -a
# docker images
# docker network ls

# docker ps -a
# docker images
# docker rename netbr00 web00
# docker ps -a

# docker commit -a "Yu Mi" -m "customized by yumi" web00 ccamm1/20220420web:v2
# docker images
# docker inspect ccamm1/20220420web:v2 | grep -e Author -e Comment
# docker inspect ccamm1/20220420web:v2 | grep yumi

========================================================

### docker image build

# git clone https://github.com/boulde/SDS.git
# ls -al
# tar -xvzf SDS/labfile_0509.tgz
# ls -al
# cd labfile
# ls -al
# cd dockerfile_dir
# ls -al

# cat Dockerfile
# docker build -t testimage .
# docker images

# cat Dockerfile.1
# docker build -t web-nginx:2.0 -f Dockerfile.1 .
# docker images

# cat Dockerfile.2
# docker build -t echo00 -f Dockerfile.2 .
# docker images

# cat Dockerfile.3
# docker build -t ping00 -f Dockerfile.3 .
# docker images
# docker run -it ping00
# docker run -it ping00 -c 5

# cat Dockerfile.4
# docker build -t param00 -f Dockerfile.4 .
# docker images
# docker run -it param00
(container)# echo $myName : $myOrder : $myNumber
(container)# env
(container)# exit
# docker inspect param00 | grep -A 10 "Labels"

# cat Dockerfile.5
# docker build -t file00 -f Dockerfile.5 .
# docker run -it file00
(container)# ls -al /test_dir
(container)# exit

# cat Dockerfile.6
# docker build -t port00 -f Dockerfile.6 .
# docker rm -f web00
# docker run -d -p 8080:443 port00
# docker run -d -P port00
# docker ps -a

# docker image history web-nginx:2.0

# docker rm -f $(docker ps -aq)
# docker ps -a
# docker rmi -f $(docker images -q)
# docker images
# docker volume prune
# docker volume ls
# docker network ls
# ip a
# cd ../../

====================================================

### kubernetes installation

2. kubelet 의 적절한 동작을 위해서 swap을 사용하지 않는다.

# swapon && cat /etc/fstab 
# swapoff -a && sed -i '/swap/s/^/#/' /etc/fstab
# cat /etc/fstab

3. SELinux, firewall 을 해제한다.

# setenforce 0
# ufw disable

4. Linux 노드의 iptables가 bridged traffic을 정확하게 확인하고 제어 할 수 있도록 br_netfilter 모듈을 load하고 관련된 네트워크 파라미터를 설정 

# cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

# cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

# sudo sysctl --system


5.  kubeadm, kubelet, kubectl 을 패키지로 설치  

1) Update the apt package index and install packages needed to use the Kubernetes apt repository:

# sudo apt-get update
# sudo apt-get install -y apt-transport-https ca-certificates curl

2) Download the Google Cloud public signing key:

# sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

3) Add the Kubernetes apt repository:

# echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

4) Update apt package index, install kubelet, kubeadm and kubectl, and pin their version:

# sudo apt-get update
# sudo apt-get install -y kubelet=1.21.0-00 kubeadm=1.21.0-00 kubectl=1.21.0-00            

# kubectl version
# kubeadm version
# kubelet --version

# sudo apt-mark hold kubelet kubeadm kubectl


6. cgroupfs를 컨테이너 런타임과 kubelet에 의해서 제어할 수 있도록 구성한다. 

# cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF


# sudo systemctl enable docker
# sudo systemctl daemon-reload
# sudo systemctl restart docker

# poweroff

###### node cloning
virtualbox관리자 > 설정 > 시스템 > 프로세서 > cpu: 2개
master > 우클릭 > 복제 >
	- 이름: worker1 / worker2
	- 경로: 변경없음
	- MAC주소 정책: 모든 네트워크 어댑터의 새 MAC생성
	- 추가옵션: 선택안함
	- 다음
	- 완전한 복제
	- 복제

master
- hostname: master
- ip: 192.168.137.101
worker1
- hostname: worker1
- ip: 192.168.137.102
worker2
- hostname: worker2
- ip: 192.168.137.103

ip주소 변경
- 바탕화면 우클릭 > open terminal
- su - => ubuntu
- nano /etc/netplan/00-installer-config.yaml
- 101 > 102
- ctrl+x > y > enter
- netplan apply
- ip a

hostname변경: 
# hostnamectl set-hostname [변경이름]
ex) hostnamectl set-hostname worker1
# hostname



7. (master)# kubeadm init

8. token 저장
cat > token.sh
kubeadm join 192.168.56.101:6443 --token 0pbjyo.0fzxwnq9bk3d03gs \
        --discovery-token-ca-cert-hash sha256:8377e53ce86923bd1c9c2ab44c04b20767dc1ea7bfe5c409ac1d118dbe6bd9d6

kubeadm join 192.168.137.101:6443 --token 0pfy9g.w8nkaqmv9s4q8186 \
        --discovery-token-ca-cert-hash sha256:a3a9f6a0d644daa12d895dd66ccdcaf074eb49eff02f9efd1977461c74781187

9. [마스터 노드에서만 실행] root 사용자가 쿠버네티스 클러스터의 API에 접근할 수 있도록 인증하기 위해서 kubeconfig 파일의 위치를 KUBECONFIG 쉘 변수에 설정한다.

# nano ~/.bashrc
export KUBECONFIG=/etc/kubernetes/admin.conf    맨 아래 줄에 추가한다.
ctrl+x > y > enter
# source ~/.bashrc 
# echo $KUBECONFIG 
/etc/kubernetes/admin.conf


10. [마스터 노드에서만 실행] Pod가 서로 통신 할 수 있도록 CNI (Container Network Interface) 기반 Pod 네트워크 추가 기능 구성한다.
   --> 네트워크가 설치되기 전에 클러스터 DNS (CoreDNS)가 시작되지 않는다.

# wget https://docs.projectcalico.org/manifests/calico.yaml 
# kubectl apply -f calico.yaml

# watch -n 1 kubectl get pods -n kube-system
ctrl + c
# kubectl get node

### node join

# cd ~
# scp token.sh 192.168.137.102:/root/token.sh
# scp token.sh 192.168.137.103:/root/token.sh
(worker1)# chmod +x token.sh
(worker1)# ./token.sh
(worker2)# chmod +x token.sh
(worker2)# ./token.sh

# kubectl get node
# kubectl cluster-info

# kubectl api-versions
# kubectl explain pods
# kubectl api-resources

kubernetes.io > documentation > reference > command line tool(kubectl) > kubectl commands
ex) # kubectl run nginx --image=nginx --dry-run=client -o yaml > test.yaml

kubernetes.io > documentation > search: cheat 
# nano ~/.bashrc
export KUBECONFIG=/etc/kubernetes/admin.conf
source <(kubectl completion bash)
alias k=kubectl
complete -F __start_kubectl k
ctrl+x > y > enter
# source ~/.bashrc
# k get pod -n kube-system

# kubectl get namespace
# kubectl get pods
# kubectl get pod -n kube-system
# k get po

# $KUBECONFIG
# cat /etc/kubernetes/admin.conf
# kubectl config view

# kubectl config current-context
# kubectl config get-contexts kubernetes-admin@kubernetes
# kubectl config set-context kubernetes-admin@kubernetes --namespace=kube-system 
# kubectl config get-contexts kubernetes-admin@kubernetes
# kubectl get pods
# kubectl config set-context kubernetes-admin@kubernetes --namespace=""

==============================================================

### pod

# cd labfile/pod/
# ls -al
# cat app.yaml
# kubectl apply -f app.yaml
###putty로 모니터링 창 생성
# watch -n 1 kubectl get pods -o wide

# kubectl describe pod app

# cd pod-resources/
# kubectl create namespace cpu-example
# watch -n 1 kubectl get pod -o wide -n cpu-example
# kubectl apply -f cpu-request-limit.yaml
# kubectl apply -f cpu-request-limit-2.yaml

# kubectl create namespace mem-example
# watch -n 1 kubectl get pods -o wide -n mem-example
# kubectl apply -f memory-request-limit.yaml
# kubectl apply -f memory-request-limit-2.yaml

# kubectl delete namespace cpu-example
# kubectl get pod -n cpu-example
# kubectl delete namespace mem-example
# kubectl get namespace

# watch -n 1 kubectl get pods -o wide
# cd ../
# cat init-containers.yaml
# kubectl apply -f init-containers.yaml
# kubectl describe pod init-demo
# curl 172.16.235.130

# kubectl get pod -n kube-system -o wide
# ls /etc/kubernetes/manifests
(worker1)# mkdir /etc/static.d 
(master)# scp app.yaml 192.168.137.102:/etc/static.d
> yes
> ubuntu
(worker1)# nano /var/lib/kubelet/config.yaml
#####staticPodPath: /etc/kubernetes/manifests
staticPodPath: /etc/static.d
ctrl+x > y > enter
(worker1)# systemctl restart kubelet
(master)# kubectl delete pod --all

(worker1)# nano /var/lib/kubelet/config.yaml
staticPodPath: /etc/kubernetes/manifests
ctrl+x > y > enter
(worker1)# systemctl restart kubelet

================================================

### label & selector

# cd ../label
# cat label01.yaml
# kubectl apply -f label01.yaml -f label02.yaml -f label03.yaml -f label04.yaml
# kubectl get pod --show-labels
# kubectl get pod -l environment=production,release=stable --show-labels
# kubectl get pod -l "app=nodejs,environment notin (develop)"
# kubectl get pod -l "app=nodejs,environment notin (develop)" --show-labels
# kubectl get pod -l "release" --show-labels
# kubectl get pod -l '!release' --show-labels

# kubectl label node worker1 disk=ssd
# kubectl get node --show-labels
# kubectl get node worker1 --show-labels
# kubectl get node worker1 --show-labels | grep disk
# kubectl describe node worker1
# kubectl describe node worker1 | grep disk
# cat nodeselector.yaml
# kubectl apply -f nodeselector.yaml
# kubectl label node worker1 disk-

# kubectl apply -f annotation.yaml
# kubectl get pod annotation-nodejs -o jsonpath='{.metadata.annotations}{"\n"}'
# apt install -y jq
# kubectl get pod annotation-nodejs -o jsonpath='{.metadata.annotations}{"\n"}' | jq
# kubectl describe pod annotation-nodejs



=============================================================

### controller

# kubectl delete pod --all
# watch -n 1 kubectl get rs,pod -o wide
# cd ../controller/replicaset
# kubectl apply -f rs-nginx.yaml
# kubectl delete pod replicaset-nginx-77ns9
# kubectl scale rs replicaset-nginx --replicas=4
# kubectl scale rs replicaset-nginx --replicas=3

# cd ../deployment/
# cat deploy-nginx.yaml
# kubectl delete rs replicaset-nginx
# watch -n 1 kubectl get deploy,rs,pod -o wide
# kubectl apply -f deploy-nginx.yaml
# kubectl scale deploy deploy-nginx --replicas=5
# kubectl scale deploy deploy-nginx --replicas=3

# kubectl set image deployment/deploy-nginx deploy-nginx=nginx:1.17.0
# kubectl get deployment deploy-nginx -o jsonpath='{.spec.template.spec.containers[0].image}{"\n"}'
# kubectl describe deployment deploy-nginx | grep -i image

# kubectl edit deployment deploy-nginx
- image: nginx:1.17.0 찾기
- i(insert) > 1.17.0 => 1.18.0 > esc > :wq!

# nano deploy-nginx.yaml
- image: nginx:1.19.0
- annotations:
	kubernetes.io/change-cause: version 1.19.0
- ctrl+x > y > enter
# cat deploy-nginx.yaml
# kubectl apply -f deploy-nginx.yaml
# kubectl get deployment deploy-nginx -o jsonpath='{.spec.template.spec.containers[0].image}{"\n"}'
# kubectl describe deployment deploy-nginx | grep -i image

# kubectl set image deployment/deploy-nginx deploy-nginx=nginx:1.20.0
# kubectl rollout pause deployment deploy-nginx
# kubectl set image deployment deploy-nginx deploy-nginx=nginx:1.20.1
# kubectl rollout resume deployment deploy-nginx

# kubectl delete rs deploy-nginx-[오타버전의문자열]5b79..

# kubectl rollout history deployment deploy-nginx
# kubectl rollout history deployment deploy-nginx --revision=2
# kubectl set image deployment deploy-nginx deploy-nginx=nginx:1.21.0 --record
# kubectl rollout history deployment deploy-nginx
# kubectl rollout undo deployment deploy-nginx
# kubectl rollout history deployment deploy-nginx
# kubectl rollout undo deployment deploy-nginx --to-revision=2
# kubectl rollout history deployment deploy-nginx

# cd ../daemonset/
# kubectl delete deployment deploy-nginx
# watch -n 1 kubectl get ds,pod -o wide
# kubectl apply -f daemonset-app.yaml

# kubectl set image daemonset daemonset-app daemonset-app=takytaky/app:v2

# kubectl patch daemonset daemonset-app -p '{"spec": {"updateStrategy": {"type": "OnDelete"}}}'
# kubectl get daemonset daemonset-app -o jsonpath='{.spec.updateStrategy.type}{"\n"}'
# kubectl rollout history daemonset daemonset-app 
# kubectl rollout undo daemonset daemonset-app
# kubectl delete pod --all

===============================================================

### service

# cd ../../service/
# kubectl delete daemonset daemonset-app
# watch -n 1 kubectl get svc,deploy,pod -o wide
# cat clusterip-hostname.yaml
# cat hostname-server.yaml
# kubectl apply -f clusterip-hostname.yaml -f hostname-server.yaml
# kubectl describe svc clusterip-hostname-service
# curl 10.111.37.130:80
# for i in $(seq 10); do curl -s 10.111.37.130:80 | grep Hello ; sleep 1 ; done

# kubectl delete svc clusterip-hostname-service
# kubectl apply -f nodeport-hostname.yaml
# curl -s 10.111.171.4:80 | grep Hello
(window)# curl -s 192.168.137.101:30080 | findstr Hello
(web browser) 192.168.137.101:30080

# kubectl delete svc nodeport-hostname-service
# kubectl create namespace metallb-system
# kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/metallb.yaml
# cat metallb-ip-config.yaml
# kubectl apply -f metallb-ip-config.yaml
# kubectl apply -f loadbalancer-hostname.yaml
(web browser) 192.168.137.200

================================================

### application deployment strategies

# cd ../controller/deployment/deploy_blue_green/
# kubectl delete deployment hostname-server
# kubectl delete svc loadbalancer-hostname-service
# kubectl apply -f blue-green-svc.yaml -f deploy-blue.yaml -f deploy-green.yaml
(web browser) 192.168.137.101:30880
# kubectl edit svc blue-green-svc
i(insert) > deploy-blue => deploy-green > esc > :wq!
(web browser) 192.168.137.101:30880

# cd ../deploy_canary/
# kubectl delete svc blue-green-svc
# kubectl delete deployment --all
# kubectl apply -f blue-canary-svc.yaml -f deploy-blue.yaml -f deploy-canary.yaml
(cmd)# curl -s 192.168.137.101:30880 | findstr Deployment
# kubectl edit svc blue-canary-svc
i(insert) > app: webapp => release: beta > esc > :wq!
(cmd)# curl -s 192.168.137.101:30880 | findstr Deployment
# kubectl edit svc blue-canary-svc
i(insert) > release: beta => release: stable > esc > :wq!
(cmd)# curl -s 192.168.137.101:30880 | findstr Deployment

=======================================================

### volume

# cd ../../../volume/
# kubectl delete svc blue-canary-svc
# kubectl delete deployment --all
# cat hostpath.yaml
# kubectl apply -f hostpath.yaml
# kubectl exec -it hostpath-pod -- /bin/bash
(pod)# cp app.js /poddir/hostpath_test
(pod)# ls poddir
(pod)# exit
# kubectl delete pod hostpath-pod

# kubectl apply -f hostpath.yaml
# kubectl exec -it hostpath-pod -- /bin/bash
(pod)# ls poddir
(worker1)# ls /mnt
(pod)# exit

# kubectl delete pod hostpath-pod
# watch -n 1 kubectl get pod,pv,pvc -o wide
# kubectl apply -f pv.yaml
# kubectl apply -f pvc.yaml
# kubectl apply -f pod-pvc.yaml
(worker1)# tail -f /mnt/k8s-pv/app.log
(worker2)# tail -f /mnt/k8s-pv/app.log

# kubectl delete pod volume-pod
# kubectl delete pvc pvc
# kubectl delete pv pv

# cd ../scheduling
# kubectl taint node worker1 dedicated=webapp:NoSchedule
# kubectl describe node worker1 | grep -i taint
# kubectl apply -f webapp.yaml
# kubectl delete pod webapp
# kubectl apply -f webapp-toleration.yaml
# kubectl taint node worker1 dedicated-
# kubectl describe node worker1 | grep -i taint

# kubectl create deployment webapp --image=nginx:1.21.0 --replicas=4
# kubectl get nodes
# kubectl cordon worker1
# kubectl get nodes
# kubectl drain worker1 -> error
# kubectl drain worker1 --ignore-daemonsets=true --force
# kubectl uncordon worker1
# kubectl get nodes

# cd ../hpa
# kubectl apply -f components.yaml
# kubectl top node
# kubectl top pod
# kubectl apply -f deploy-hpa.yaml
# kubectl apply -f autoscaling.yaml
# watch -n 1 kubectl get hpa,pod -o wide
# kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"






