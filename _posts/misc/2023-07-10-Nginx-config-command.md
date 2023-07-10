---
title: "Nginx configuration and command"
last_modified_at: 2023-07-10T14:38:10-05:00
categories:
  - Blog
tags:
  - Nginx
---

## 설정
- 설정파일: {Niginx Root}/conf/nginx.conf
```
server {
  listen      80;
  server_name localhost;

  #charset koi8-r;

  #access_log  logs/host.access.log  main;

  location / {
    root   C:/workspace/sts-4.9.0.RELEASE-workspace/housepuzzle_landing_web_html/html;
    index  index.html index.htm;
  }

  #error_page  404  /404.html;

  #redirect server error pages to the static page /50x.html
  #
  error_page  500 502 503 504  /50x.html;
  location = /50x.html {
    root html;
  }
}
```
<br/>
위와 같은 방식으로 location / root 부분을 pc 파일경로로 연결해 주면 http://localhost로 접속했을 때,  pc에 저장되어 있는 파일을 열 수 있다.  

#### CORS
- Cross-Orign Resourcing Sharing (CORS)은 추가적인 HTTP header를 사용해서 어플리케이션이 다른 origin의 리소스에 접근할 수 있도록 하는 메커니즘을 말함
- nginx에서 cors를 어떻게 설정하는지 기록이 필요함

#### 윈도우 nginx 명령어
- nginx.exe: nginx 실행  
- tasklist /fi "imagename eq nginx.exe" : nginx 실행여부 확인  
- nginx -s stop: 빠른 종료  
- nginx -s quit: 일반 종료  
- nginx -s reload : 재기동  
- nginx -s reopen : 로그파일 다시쓰기 시작  
