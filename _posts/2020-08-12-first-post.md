---
title: "github.io 블로그 만들기"
excerpt: "GitHub Blog 서비스를 이용하여 블로그를 만드는 과정이다."
toc: true
toc_sticky: true
categories:
 - Blog
tags:
 - Blog
last_modified_at: 2020-08-12T01:12:00
---

GitHub Blog 서비스인 GitHub Pages를 이용하여 github.io 블로그를 만드는 과정을 정리하려고 한다.
개발환경은 MacOS Catalina이다.

# 1. Jekyll 설치
Jeykll 설치에 앞서, Ruby를 먼저 설치해야 한다.
그런데 MacOS Catalina에는 이미 Ruby가 설치되어 있어서, 바로 Jekyll을 설치하였다.  

터미널에서 아래와 같이 입력한다.
> gem install jekyll bundler

MacOS에서 Ruby설치는 아래 블로그를 참고하였다.  
<https://ogaeng.com/jekyll-blog-install/>

# 2. minimal-mistakes 테마 설치
Jekyll을 이용하여 바로 블로그를 만들수 있지만, 여러가지 테마를 적용할 수도 있다. 그래서 여러 블로거들이 추천하는 minial-mistakes라는 테마를 적용하였다.

먼저 아래 minimal-mistakes GitHub 사이트에 들어가서, 저장소를 Fork한다.  
<https://github.com/mmistakes/minimal-mistakes>

git clone을 하거나 압축파일로 다운로드 받을 수 있는데 Fork를 하는 이유는 미묘한 차이가 있기 때문이다.
Fork를 하여 내 저장소에 복사하게 되면 코드를 수정하고 pull request를 통해 원본 저장소에 기여할 수 있다.
하지만 git clone을 하거나 압축파일을 받아서 내 저장소에 업로드 할 경우 원본 저장소에 pull request 할 수 없다.
그리고 git clone을 할 경우에는 기존 커밋 내역이 남아있지만, 압축파일로 다운로드 받을경우에는 커밋 내역이 없다.  

Fork한 저장소의 이름을 username.github.io로 바꾼다. 여기서 username은 Github계정의 username이다.
Fork한 저장소를 내 로컬에 git clone을 하고, 터미널에서 로컬 저장소 경로로 이동하여 어래 명령어를 실행시킨다.

> bundle exec jekyll serve

그리고 브라우저에서 아래 주소로 접속하면 로컬상에 구성된 블로그가 뜨게된다.
> <http://127.0.0.1:4000/>

Fork한 저장소는 원본 저장소를 복사한 것이므로 원본 저장소와 별개로 운영된다.
나중에 원본 저장소에 업데이트가 있을 경우 원본저장소의 master 브랜치를 내 저장소의 master 브랜치에 동기화시킬 계획이다.
따라서 원본 저장소를 내 저장소에 upstream 이라는 이름으로 등록한다.
커맨드는 아래와 같다.

> git remote add upstream git@github.com:mmistakes/minimal-mistakes.git

내 저장소의 master 브랜치는 원본 저장소의 master 브랜치와 동기화 시키는 용도로 사용할 것이다.
그리고 develop 브랜치를 새로 만들어 블로그 업데이트에 관련된 것은 이 브랜치에 업로드 할 계획이다.
master 브랜치가 업데이트 될 때마다 develop 브랜치를 rebase 하여 업데이트를 적용할 계획이다.

# 3. 블로그 설정 및 포스팅
블로그 상세 설정 및 포스팅은 아래 블로그를 참고하였다.
리눅스 환경에서 블로그 만드는 과정이 처음부터 끝까지 아주 자세하게 나열되어 있다.

<https://devinlife.com/>
