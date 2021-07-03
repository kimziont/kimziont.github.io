---
title:  "[Django] 장고 개발환경 세팅하기"
toc: true
toc_sticky: true
author_profile: true
sidebar_main: true
header:
  teaser: /assets/images/django.png

categories:
  - django
tags:
  - django
last_modified_at: 2021-06-01
---  

## 1. Django 개발 환경

![](/assets/images/django_intro_2.png){: width="70%"}  

## 2. 가상 환경

프로젝트 별로 필요한 도구들의 버전이 다를 수 있습니다. 프로젝트 별로 다른 버전을 충돌없이 사용하기 위해, 각각의 가상환경을 만들면 됩니다.  

![](/assets/images/django_intro_1.png){: width="70%"}  


## 3. CLI WSL(Windows Subsystem for Linux)  
windows에서 리눅스 터미널을 사용할 수 있게 해주는 확장 프로그램  

### WINDOWS 기능 켜기/끄기  

Linux용 windows 하위 시스템 체크

### Microsoft store  

ubuntu 18/04 LTS 버전 설치  

### 우분투 설정하기  

사용자 이름(ziont0510)과 비밀번호 설정  

우분투 업데이트, 기본 패키지 설치  

```
sudo apt-get update
```  

```
sudo apt-get install -y make build-essential \
libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev \
wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev git python-pip
```  

디렉토리 생성 및 이동  

```
# CLI
mkdir jay_django
cd jay_django
code .
```  

## 4. 파이썬 관리 패키지 pyenv 설치  

```
# CLI
curl https://pyenv.run | bash
```  

```
# CLI
export PATH="/home/ziont0510/.pyenv/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv virtualenv-init -)"
 ```

```
# .bashrc에 추가
export PATH="/home/ziont0510/.pyenv/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv virtualenv-init -)"
```

```
# CLI
pyenv --version
```

## 5. pyenv를 이용해 파이썬 설치하기

```
# CLI
pyenv install 3.7.7
pyenv install 3.8.2
```

```
# CLI
pyenv versions
```

### 가상환경 구성하기

```
# CLI
pyenv virtualenv {파이썬 버전} {가상환경 이름}
pyenv virtualenv 3.7.7 django-envs

pyenv uninstall {가상 환경 이름} -> 삭제하고 싶을 때
```

```
# CLI
pyenv versions
```

![](/assets/images/django_intro_3.png){: width="70%"}  

```
# CLI
# Global 가상환경 적용하기
pyenv global {가상 환경 이름}
pyenv global 3.8.2

# Local 가상환경 적용하기
cd jay_django
pyenv local django_envs
```

### Django 설치하기

```
# CLI
pip install django==2.2

# 장고 버전 확인하기
django-admin --version
```  

