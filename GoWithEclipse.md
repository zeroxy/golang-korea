작성중!!


# 목적 #
  * 범용 Java 개발툴인 Eclipse 를 이용하여 Go 프로그래밍하기 위한 IDE 환경 설정 방법을 소개함
# 소개 #
  * goclipse는 Go language 용 IDE를 지원하는 Eclipse plugin 으로, Go language를 시작하는 초보자들이 쉽게 학습할 수 있는 간단한 환경을 제공해준다. golcipe project는 현재 진행형~ [goclipse 바로가기](http://code.google.com/p/goclipse/)
  * gocode는 Go language 의 자동 완성을 지원하는 프로그램으로, vim, emacs, Eclipse 등에서 활용 가능함.[gocode 바로가기](https://github.com/nsf/gocode)
# 미리하기 #
> Eclipse로 Go 프로그래밍 하기 위해서는, 당연히! Go와 Eclips가 모두 설치되어 있어야 함.
  * Java 1.6 또는 이후버전 (http://www.java.com/)
  * Go 설치하기 : [Go시작하기](GoInstall.md) 참조하여 컴파일 & 실행까지 확인할 것
  * Eclipse 설치하기 (3.6 or later ) : [Eclipse site](http://www.eclipse.org/downloads/) 에서 다운로드하여 설치할 것
# 설치하기 #
## goclipse 설치하기 ##
  * [원문보기](http://code.google.com/p/goclipse/wiki/InstallationInstructions)
  * 현재 [plan 9 컴파일러](http://plan9.bell-labs.com/sys/doc/compiler.html)만 지원, GCCGO는 추후 지원 예정
  * 개발 버전 업데이트 : http://goclipse.googlecode.com/svn/trunk/goclipse-update-site/
  * 참고: 만약 분류되지 않은, 다른 버전의 plug-in이 설치되어 있으면, 아래 Eclipse repository cache를 삭제 바람. eclipse/p2/org.eclipse.equinox.p2.repository/cache
  1. Eclipse 실행, Help > Install New Software... 다이얼로그에서 Work with 텍스트 박스에 아래 URL 입력 후 Add... 버튼 클릭  http://goclipse.googlecode.com/svn/trunk/goclipse-update-site/
  1. Name 텍스트 박스는 빈칸으로 두고 OK 클릭. (직접 입력해도 무관~)
  1. Install 다이얼로그 중안 박스 내 Category 체크 박스 선택 후 Next 클릭.
  1. 설치 내용 확인 후 Next 클릭.
  1. 라이센스 동의에 I accept the terms of the license agreements 클릭 후 완료.
  1. Eclipse 재시작 물음에 Yes 클릭.
  1. 이제 Go Project 시작을 위한 준비 완료. 메뉴 > File > New > Project... 클릭
  1. Go Project 선택 후 Next click.
  1. Project name 텍스트 란에 project 이름 입력 후 Finish 클릭.
  1. Go  프로젝트가 생성된 내용을 Project Explorer 창에서 볼 수 있음. 더 상세한 정보는 신규 프로젝트 생성 시 GettingStarted 또는 development tips 에서 참고 바람.
## gocode ##
  * 

# 따라하기 #


## Project 생성 ##


## 실행 하기 ##

## 참고사이트 ##
> windows에서 goclipe 실행하기