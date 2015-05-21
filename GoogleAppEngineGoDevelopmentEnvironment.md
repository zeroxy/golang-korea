  * original: http://code.google.com/intl/ko-KR/appengine/docs/go/gettingstarted/devenvironment.html
  * translated by: Jooyung Han
  * parent: [App Engine 시작하기](GoogleAppEngineGoGettingStarted.md)


# 개발 환경 #

Google App Engine을 위한 Go 애플리케이션은 App Engine Go SDK를 이용하여 개발하여 업로드하면 됩니다.

Go SDK는 App Engine 환경을 시뮬레이션해주는 웹 서버 애플리케이션을 포함하며, 로컬 버전의 데이터스토어, 구글 계정도 포함하고 있습니다. 그리고 App Engine API를 이용하여 URL에서 데이터를 가져오거나 여러분의 컴퓨터에서 바로 이메일을 보낼 수도 있습니다. Go SDK는 Python SDK의 개발 도구에서 조금 변형된 형태이며, Python 2.5가 설치된 인텔 기반의 Mac OS X나 리눅스 컴퓨터에서 동작합니다.

필요하다면 [Python 웹사이트](http://www.python.org/)에서 여러분의 플랫폼에 맞는 Python 2.5를 받아서 설치하세요. Mac OS X 10.5 Leopard 에는 이미 Python 2.5가 설치되어 있습니다. 그보다 최신의 OS X에는 더 최신 버전의 Python이 설치되어 있으니, 만약 Python 도구와 관련된 문제가 생긴다면 가급적 Python 2.5를 설치해주세요.

[Go SDK 받기](http://code.google.com/appengine/downloads.html). 해당 페이지의 안내에 따라 SDK를 받아서 설치하세요.

이 튜토리얼에서 여러분은 SDK의 명령어 두 개를 사용하게 됩니다.
  * [dev\_appsesrver.py](http://code.google.com/appengine/docs/python/tools/devserver.html), 개발용 웹 서버
  * [appcfg.py](http://code.google.com/appengine/docs/python/tools/uploadinganapp.html), 애플리케이션을 App Engine 에 올리기

압축 파일을 풀었을 때 `google_appengine` 디렉토리에 위 명령어들이 있습니다.

개발과 배포를 간단하게 하기 위해 PATH 환경변수에 위 디렉토리를 추가하는 것도 좋습니다. `$HOME/.profile` 이나 `$HOME/.bashrc` 등을 수정하시면 됩니다.

```
export PATH=/path/to/google_appengine:$PATH
```

## 다음은... ##

여러분이 만드는 App Engine 애플리케이션을 세상에 공개하기 전에 로컬 환경에서 개발하고 테스트해 볼 수 있습니다. 코드를 작성해볼까요?

[Hello, World!](GoogleAppEngineGoHelloWorld.md)로 계속.