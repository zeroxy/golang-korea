  * original: http://code.google.com/intl/ko-KR/appengine/docs/go/gettingstarted/uploading.html
  * translated by: Jooyung Han
  * parent: [App Engine 시작하기](GoogleAppEngineGoGettingStarted.md)

# 애플리케이션 업로드하기 #

App Engine 애플리케이션을 만들고 관리하는 것은 관리자 콘솔에서 할 수 있습니다. 여러분이 만든 애플리케이션의 id를 등록한 다음 SDK에 포함된 `appcfg.py`라는 명령행 도구를 이용하여 여러분의 웹사이트에 애플리케이션을 업로드하면 됩니다.

**주의:** 애플리케이션 id는 등록하고 나서도 삭제할 수 있습니다. 하지만 한번 삭제된 id를 다시 등록할 수는 없습니다. 지금 id를 등록하고 싶지 않다면 다음 단계를 건너띄기 바랍니다.

## 애플리케이션 등록하기 ##

App Engine 애플리케이션은 다음 주소의 관리자 콘솔에서 생성하고 관리할 수 있습니다.

> http://appengine.google.com/

App Engine에 여러분의 구글 계정으로 로그인하세요. [아직 구글 계정이 없다면](https://www.google.com/accounts/) 이메일 주소와 암호를 이용하여 새로 만들면 됩니다.

새 애플리케이션을 생성하려면 "Create Application" 버튼을 클릭합니다. 설명에 따라 애플리케이션 id를 등록합니다. id는 유일한 문자열이어야 합니다. 여러분이 무료로 제공되는 appspot.com 도메인을 이용하기로 한다면 애플리케이션의 전체 URL은 `http://application-id.appspot.com/'이 됩니다. 나중에 여러분의 애플리케이션만을 위한 도메인을 마련하게 되면 그것을 이용할 수도 있습니다.

`app.yaml` 설정파일을 편집하여 `application:` 값을 여러분이 등록한 애플리케이션 id로 수정하세요.

## 애플리케이션 업로드하기 ##

완성한 애플리케이션을 Google App Engine에 업로드하려면 아래 명령을 실행하세요.

```
appcfg.py update myapp/
```

사용자명과 암호를 물어보면 구글 계정 정보를 입력합니다.

절차가 모두 순조롭게 진행되고나면 여러분의 애플리케이션이 App Engine에 배포된 것입니다. 컴파일 오류가 나온다면 수정한 다음 `appcfg.py`를 다시 실행합니다. 컴파일이 성공해야만 애플리케이션이 배포됩니다.

> `http://application-id.appspot.com`

## 축하합니다! ##

튜토리얼을 모두 마쳤습니다. 더 많은 정보를 보기 위해서는 [App Engine 문서](http://code.google.com/appengine/docs/go/)를 참고하세요.