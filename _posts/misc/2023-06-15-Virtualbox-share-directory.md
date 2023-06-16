---
title: "How to share directory between windows and ubuntu in virtual box"
last_modified_at: 2023-06-15T13:00:02-05:00
categories:
  - Blog
tags:
  - Virtual box
---

작업을 하다보면 Virtual box 내의 VM에서 Host OS인 윈도우와 파일을 파일을 공유해야만 하는 경우가 상당히 많이 발생한다.  
이 작업은 버추얼박스의 공유폴더 기능을 이용해서 가능한데, 그 내용을 상세하게 기록하고자 한다.  

## 버추얼박스 설치 및 가상머신 만들기
1. 버추얼박스 설치  
   - [다운로드 링크](https://www.virtualbox.org/wiki/Downloads)를 통해서 다운로드 및 설치가 가능하다.
   - 윈도우즈의 경우 설치 화면에서 하라는 데로 따라서 클릭하면 된다.
   - 좀 헛갈리면 [다른 사람의 글](https://kimhjin.tistory.com/58)을 참조하자.

2. VirtualBox 환경설청
   - [다른 사람의 글](https://kimhjin.tistory.com/58)을 참조하자.  


## 버추얼박스 공유폴더 (Guest OS: Windows)  
1. 버추얼박스가 설치된 호스트 PC에 공유하는 폴더를 생성한다.

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/23c77016-7be6-487f-8770-030f9d04947d)

2. 버추얼박스에서 Windows VM의 [설정]-[공유폴더] 탭으로 이동 후, 화면에 따라 진행한다.

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/8dff4158-9d6c-43dc-835c-69cab573d3f3)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/f0454a80-575c-43b3-9113-338b01c4e7e7)

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/672859c7-8448-4f31-976b-95ea44bd759a)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/27834909-82fa-40d6-96ce-a0208f3ca308)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/fd3262f2-5a4d-4bbe-a79d-71630966b897)  

3. 버추얼박스 Windows VM 시작 후 Windows VM 파일 탐색기로 이동하면 [버추얼박스 공유폴더]가 네트워크 드라이브로 연결되어 있고, 해당 폴더에서 읽기/쓰기가 가능하다

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/f9a6ae74-d25f-4e1a-a84a-ecf477ff49a0)  

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/0646f3f6-bae4-4497-adc1-b9340279d4d0)  

## 버추얼박스 공유폴더 (Guest OS: Linux) 
1. Windows일 경우의 1, 2번을 진행한다  
2. Linux VM을 실행 후에 아래와 같이 설정하여 공유폴더로 사용할 디렉터리를 마운트 한다. (아래 그림은 CentOS인 경우)

![image](https://github.com/lucky-sugar-park/lucky-sugar-park.github.io/assets/135287235/32901028-b827-4896-8b17-1ff0b9286b3f)  

