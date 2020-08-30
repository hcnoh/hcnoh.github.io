---
layout: post
use_math: true
title: "[Windows 10] 개발 환경 세팅"
date: 2019-06-09 19:03:05
tagline: "Windows 10에서 데이터 분석을 위한 개발 환경 세팅 방법 정리"
categories:
- Development Memo
tags:
- windows
- python
- tensorflow
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-06-09-windows-setting
---

새로 노트북을 구매하여 Windows 10 세팅을 진행하게 되었다. 다음에 혹시 참고하게 될 것 같아서 과정을 정리해둔다.

## Visual Studio Code 설치
Visual Studio Code (VSCODE)를 먼저 설치한다. 설치는 간단하게 [VSCODE 공식 홈페이지](https://code.visualstudio.com/)에서 수행할 수 있다.

![](/assets/img/2019-06-09-windows-setting/2019-06-09-windows-setting_2019-06-09-19-12-24.png)

우리는 윈도우에 해당하는 설치 파일을 받아야 하므로 왼쪽에 있는 다운로드 버튼을 눌러서 다운받으면 된다.

VSCODE는 마이크로소프트에서 개발한 오픈소스 IDE이며 Git과의 연동은 물론 다양한 Extension을 제공하기 때문에 사용이 간편하며 파이썬 개발을 위해서 사용하는 것을 추천한다.

## Git 설치
그 다음으로는 윈도우를 위한 Git 설치를 수행한다. Git for Windows라는 유틸이 있으며 [링크](https://gitforwindows.org/)에서 다운받을 수 있다. 특히 이 유틸은 Git Bash라는 기능을 제공하는데 Linux Bash에 대체하여 잘 사용해 볼 생각이다. (하지만 결국 윈도우의 명령 프롬프트를 사용하는 것으로 결정)

![](/assets/img/2019-06-09-windows-setting/2019-06-09-windows-setting_2019-06-09-19-19-36.png)

마찬가지로 간단하게 다운받을 수 있다. 첨언을 하자면 현재 세팅할때 중간에 나오는 옵션에서 `Use Git from Git Bash only` 옵션을 선택하였다.

Git for Windows의 설치는 다음의 링크를 참고하였다:
- [https://promobile.tistory.com/352](https://promobile.tistory.com/352)

설치 과정을 살펴보면 먼저 어떤 컴포넌트들을 설치할지 결정해야 한다. 그냥 Default로 설정하였다.

![](/assets/img/2019-06-09-windows-setting/2019-06-09-windows-setting_2019-06-20-11-03-26.png)

![](/assets/img/2019-06-09-windows-setting/2019-06-09-windows-setting_2019-06-20-11-04-15.png)

또한 앞으로 VSCODE를 사용할 것이기 때문에 위와 같은 옵션을 설정해주었다.

![](/assets/img/2019-06-09-windows-setting/2019-06-09-windows-setting_2019-06-20-11-05-03.png)

그 다음은 어떤 CLI에서 Git을 사용할 수 있게 할 것이냐에 관한 옵션이다. 첫 번째 옵션은 Git for Windows 설치시 함께 설치되는 Git Bash에서만 Git을 사용할 수 있게 해주는 옵션이며, 두 번째 옵션은 윈도우 명령 프롬프트에서도 사용할 수 있게 해주는 옵션이다. 여기서는 명령 프롬프트에서도 사용하기 위하여 두 번째 옵션을 선택하였다.

그 외 나머지 옵션은 모두 디폴트로 설정하여 진행하였다. 혹시 나중에 참고할 수도 있기 때문에 이미지를 첨부하였다.

![](/assets/img/2019-06-09-windows-setting/2019-06-09-windows-setting_2019-06-20-11-08-35.png)

![](/assets/img/2019-06-09-windows-setting/2019-06-09-windows-setting_2019-06-20-11-08-57.png)

![](/assets/img/2019-06-09-windows-setting/2019-06-09-windows-setting_2019-06-20-11-09-14.png)

![](/assets/img/2019-06-09-windows-setting/2019-06-09-windows-setting_2019-06-20-11-09-36.png)

## Python 설치
파이썬도 마찬가지로 공식 홈페이지에서 다운받을 수 있다. 현재는 파이썬 3.5.2 버전으로 설치를 진행하였다. 다운로드 페이지는 [링크](https://www.python.org/downloads/release/python-352/)에서 확인할 수 있다.

![](/assets/img/2019-06-09-windows-setting/2019-06-09-windows-setting_2019-06-09-19-26-23.png)

현재 노트북은 64비트 운영체제를 사용하기 때문에 `Windows x86-64 executable installer`를 다운받아서 설치하였다.

초기 설치화면에서 `Add Python 3.5 to PATH`를 꼭 선택하는 것을 추천한다. 따로 PATH를 추가해줘야하는 수고를 줄일 수 있다.

파이썬 설치는 다음의 링크를 참고하였다:
- [https://mainia.tistory.com/5678](https://mainia.tistory.com/5678)

## Python 패키지 설치
파이썬 설치가 다 끝난 이후에 Git Bash를 실행하여 파이썬 버전을 확인해보면 (PATH를 추가해줬기 때문에) 제대로 나오는 것을 확인할 수 있다.

```bash
$ python --version
Python 3.5.2
```

만약 이 명령이 제대로 수행되지 않는다면 PATH가 제대로 추가되지 않은 경우일 수 있으니 확인하여 추가하도록 해주자. Git Bash 상에서 PATH를 추가해주는 방법은 [링크](https://sonalsatpute.wordpress.com/2016/07/21/python-on-windows-git-bash/)에서 확인할 수 있다.

추가적으로 파이썬의 PATH가 어디로 설정되어 있는지는 아래의 명령을 통해서 확인할 수 있다.

```bash
$ where python
C:\Users\Hyungcheeol Noh\AppData\Local\Programs\Python\Python35\python.exe
```

이제 `pip`를 이용하여 패키지 설치를 해나갈 것이다. 먼저 `pip`를 업데이트한다.

```bash
$ pip install --upgrade pip
```

만약 이미 최신 버전이라면 다음과 같은 결과가 나올 것이다.

```bash
$ pip install --upgrade pip
Requirement already up-to-date: pip in c:\users\hyungcheeol noh\appdata\local\programs\python\python35\lib\site-packages (19.1.1)
```

설치가 끝난 이후에 버전을 확인해보면 최신 버전인 것을 알 수 있을 것이다.

```bash
$ pip --version
pip 19.1.1 from c:\users\hyungcheeol noh\appdata\local\programs\python\python35\lib\site-packages\pip (python 3.5)
```

현재 패키지 목록을 확인해보면 아무것도 없는 것을 확인할 수 있다.

```bash
$ pip freeze
```

하나하나 새로 설치하도록 해보자. 일단 현재 설치한 패키지의 목록은 아래와 같다.

```bash
$ pip install numpy
$ pip install pandas
$ pip install jupyter
$ pip install tensorflow
$ pip install scikit-learn
$ pip install matplotlib
$ pip install virtualenv
```

