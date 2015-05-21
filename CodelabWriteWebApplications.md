  * 원문: [Codelab: Writing Web Applications](http://golang.org/doc/codelab/wiki/)
  * 번역: Jooyung Han

# 코드랩: 웹 애플리케이션 작성하기 #



## 도입 ##

이 코드랩에서 다루는 내용:
  * load/save 메소드를 가진 자료구조 만들기
  * 웹 애플리케이션을 위한 `http` 패키지
  * HTML 템플릿을 처리하기 위한 `template` 패키지
  * 사용자 입력을 검증하기 위한 `regexp` 패키지
  * 클로저

아래 내용은 알고 있다고 가정
  * 프로그래밍 경험이 있음
  * 기본적인 웹 기술(HTTP, HTML)
  * 유닉스 커맨드라인에서 작업하기

## 시작 ##

리눅스, OS X, FreeBSD 중 하나가 설치된 컴퓨터가 있어야 한다. 없다면 [VirtualBox](http://www.virtualbox.org/) 등을 이용하여 리눅스 버추얼 머신을 설치하거나 VPS([Virtual Private Server](http://www.google.com/search?q=virtual+private+server), [가상서버호스팅](http://www.google.com/search?q=가상서버호스팅)) 서비스를 이용해도 된다.

Go를 설치한다. ([Go 설치하기](GoInstall.md) 참고)

이번 코드랩을 위한 디렉토리를 만들고 그리로 이동한다.

<pre>
$ mkdir ~/gowiki<br>
$ cd ~/gowiki<br>
</pre>

`wiki.go`라는 파일을 만들고, 편안한 편집기로 열어서 다음 내용을 입력한다.
```
package main

import (
	"fmt"
	"io/ioutil"
	"os"
)
```

Go 표준 라이브러리에서 `fmt`, `ioutil`, `os` 패키지를 가져온다. 나중에 기능을 추가하면서 필요한 패키지를 `import`에 추가해 나갈 것이다.

## 자료구조 ##

먼저 자료구조를 정의한다. 위키(wiki)는 서로 연결된 페이지(page)들로 구성된다. 각 페이지는 제목(title)과 본문(body)으로 구성된다. `Page`라는 이름으로 제목과 본문 필드를 가진 구조체(struct)를 정의하자.

```
type Page struct {
	Title	string
	Body	[]byte
}
```

`[]byte`라는 타입은 "`byte` 슬라이스"를 의미한다.(슬라이스는 [Go언어를 잘 사용하는 방법](EffectiveGo.md) 참고) `Body`를 `string`이 아닌 `[]byte`타입으로 정의한 이유는 우리가 사용하게될 `io`라이브러리가 `[]byte` 타입으로 되어있기 때문이다.

`Page` 구조체는 페이지 정보가 메모리에 어떻게 저장될지를 나타낸다. 그런데 파일에 저장하려면 어떻게 할까? `Page`에 `save` 메소드를 만들어서 이 문제를 해결해보자.

```
func (p *Page) save() os.Error {
	filename := p.Title + ".txt"
	return ioutil.WriteFile(filename, p.Body, 0600)
}
```

메소드의 시그니처가 의미하는 바는 "이것은 `Page` 포인터인 `p`를 수신자로 하는 `save` 메소드이다. 파라미터는 없고, `os.Error` 타입의 값을 반환한다." 이다.

이 메소드는 `Page`의 `Body`를 텍스트 파일로 저장한다. 편의상 `Title`을 파일명으로 사용한다.

`save` 메소드는 `os.Error` 값을 반환하는 이유는 `WriteFile` 함수(바이트 슬라이스를 파일에 저장하는 표준 라이브러리 함수)의 반환 타입이기 때문이다. `save` 메소드는 에러 값을 반환하여 파일 저장에 문제가 생겼을 때 애플리케이션이 이를 처리할 수 있도록 한다. 아무 문제가 없다면 `Page.save()`는 `nil`(포인터, 인터페이스 타입의 0 값)을 반환한다.

`WriteFile`에 파라미터로 넘기는 8진수 상수값 `0600`은 생성되는 파일의 권한이 현재 사용자에게 '읽기쓰기가능'으로 설정하라는 의미이다. (유닉스의 `open(2)` man 페이지 참고)

페이지를 읽어들이는 메소드도 필요하다.
```
func loadPage(title string) *Page {
	filename := title + ".txt"
	body, _ := ioutil.ReadFile(filename)
	return &Page{Title: title, Body: body}
}
```

`loadPage`함수는 `title`로 파일명을 만든 뒤 해당 파일의 내용을 읽어들여 새로운 `Page`를 생성, 그 포인터를 판환한다.

함수들은 여러 값을 반환할 수 있다. 표준 라이브러리 함수인 `io.ReadFile`은 `[]byte`와 `os.Error`를 반환한다. `loadPage`에서는 아직 에러를 처리하지 않으므로 "무시 식별자"인 밑줄(`_`) 기호를 써서 반환된 에러 값을 날려버린다. (실제로는 값을 어디에도 대입하지 않는다)

그런데  `ReadFile`에서 에러가 발생하면 어떻게 될까? 해당 파일이 없는 경우처럼. 그런 에러를 무시하면 안된다.  `*Page`와 `os.Error`를 반환하도록 함수를 수정해보자.

```
func loadPage(title string) (*Page, os.Error) {
	filename := title + ".txt"
	body, err := ioutil.ReadFile(filename)
	if err != nil {
		return nil, err
	}
	return &Page{Title: title, Body: body}, nil
}
```

이 함수를 호출하는 쪽에서는 이제 두번째 파라미터를 체크할 수 있다. `nil`이면 페이지를 성공적으로 읽어들인 것이고 `nil`이 아니라면 `os.Error` 값일테니 호출한 쪽에서 이를 처리하면 된다. (더 자세한 내용은 [`os` 패키지 문서](http://golang.org/pkg/os/#Error)를 참고해라)

지금까지 저장하고 불러올 수 있는 간단한 자료구조를 만들었다. 이를 테스트하기 위한 `main` 함수를 만들 차례다.

```
func main() {
	p1 := &Page{Title: "TestPage", Body: []byte("This is a sample Page.")}
	p1.save()
	p2, _ := loadPage("TestPage")
	fmt.Println(string(p2.Body))
}
```

이 코드를 컴파일하여 실행하면 `TestPage.txt`라는 파일이 만들어진다. 파일의 내용은 `p1` 페이지의 내용이며, 이를 다시 읽어들여 `p2`를 만들고 `Body`부분을 화면에 출력한다.

다음처럼 컴파일하고 실행하면 된다.

<pre>
$ 8g wiki.go<br>
$ 8l wiki.8<br>
$ ./8.out<br>
This is a sample page.<br>
</pre>

(`8g`와 `8l` 명령어는 `GOARCH=386`시스템의 경우이다. `amd64` 시스템에서는 각각을 `6g`와 `6l`로 바꾸면 된다.)

[지금까지 작성한 코드 보기](http://golang.org/doc/codelab/wiki/part1.go)

## `http` 패키지 소개 (쉬어가기) ##

다음 코드는 완전히 동작하는 간단한 웹서버를 구현한 것이다.

```
package main

import (
	"fmt"
	"http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hi there, I love %s!", r.URL.Path[1:])
}

func main() {
	http.HandleFunc("/", handler)
	http.ListenAndServe(":8080", nil)
}
```

`main`함수는 `http.HandleFunc`를 호출하여 `http` 패키지가 웹루트("/")로 접근하는 모든 요청을 `handler`에서 처리하도록 지정한다.

그런 다음 `http.ListenAndServer`를 호출하여 모든 인터페이스로부터 8080번 포트의 요청을 기다리게 만든다(`":8080"`)
(당장은 두번째 파라미터인 `nil`을 신경쓰지 말자.) 이 함수는 프로그램이 종료될 때까지 블록 된다.

`handler` 함수는 `http.HandlerFunc` 타입이다. `http.ReponseWriter`와 `http.Request`, 두 개의 인자를 받는다.

`http.ResponseWriter`는 HTTP 서버의 응답을 만들어주며, 여기에 내용을 쓰는 것으로 HTTP 클라이언트에 데이터를 보내게 된다.

`http.Request`는 클라이언트의 HTTP 요청을 나타내는 자료 구조이다. `r.URL.Path`는 요청 URL에서 경로(path) 부분을 의미한다. 그 뒤에 붙은 `[1:]`는 "`Path`의 1번 위치부터 끝까지의 글자들로 슬라이스를 생성하라"는 의미이다. 이렇게 하여 경로의 맨 첫 글자인 "`/`"를 제외시킬 수 있다.

이 프로그램을 실행시키고 다음 URL로 접속하면,

<pre>
http://localhost:8080/monkeys<br>
</pre>

프로그램은 다음 내용을 포함한 페이지를 보여줄 것이다.

<pre>
Hi there, I love monkeys!<br>
</pre>

## `http`를 사용하여 위키페이지 보여주기 ##

`http` 패키지를 사용하려면 임포트(import)를 해야만 한다.

```
import (
 	"fmt"
	"http"
	"io/ioutil"
	"os"
)
```

위키페이지를 보여주기 위한 핸들러를 만들어보자.

```
const lenPath = len("/view/")

func viewHandler(w http.ResponseWriter, r *http.Request) {
	title := r.URL.Path[lenPath:]
	p, _ := loadPage(title)
	fmt.Fprintf(w, "<h1>%s</h1><div>%s</div>", p.Title, p.Body)
}
```

우선 `r.URL.Path`에서 페이지 제목을 추출한다. 앞서 살펴본 것처럼 `r.URL.Path`는 요청 URL의 경로부분에 해당한다. 전역 상수인 `lenPath`는 요청 경로의 앞 부분에 해당하는 `"/view/"`의 길이이다. `Path`에 `[lenPath:]`를 적용하여 슬라이스를 만들면서 문자열의 처음 여섯 글자를 제외시켰다. 경로 앞에 포함시킬 `"/view/"`는 페이지 제목에는 포함되지 않기 때문이다.

이 함수는 페이지 데이터를 읽어들여 간단한 HTML 문자열로 변환하여 `w`, 즉 `http.ResponseWriter`에 내용을 쓴다.

여기서도 `_`를 사용하여 `loadPage`의 반환 값 중 `os.Error`를 무시하고 있다. 예제를 단순하게 만들기 위해 그런 것이지 실제로는 나쁜 코딩 습관이다. 여기에 대해 뒤에서 다시 언급한다.

핸들러 함수를 사용하기 위해 `main` 함수에서는 `viewHandler`가 `/view/` 경로로 들어오는 요청을 처리하도록 `http`를 초기화한다.

```
func main() {
	http.HandleFunc("/view/", viewHandler)
	http.ListenAndServe(":8080", nil)
}
```

[지금까지 작성한 코드 보기](http://golang.org/doc/codelab/wiki/part2.go)

`test.txt`라는 이름으로 테스트 페이지 데이터를 만든 다음, 프로그램을 컴파일하고 위키 페이지를 보여주는지 확인해보자.

<pre>
$ echo "Hello world" > test.txt<br>
$ 8g wiki.go<br>
$ 8l wiki.8<br>
$ ./8.out<br>
</pre>

웹 서버가 돌아가면, `http://localhost:8080/view/test`로 접속하였을 때 제목이 "test"이고 내용은 "Hello world"인 페이지가 나타나야 한다.

## 페이지 편집 ##

페이지를 편집할 수 없다면 그건 위키가 아니다. 이제 추가로 두 개의 핸들러를 만들어보자. 하나는 '페이지 편집' 폼을 보여주는 `editHandler`이고, 다른 하나는 폼으로 입력된 데이터를 저장하는 `saveHandler`이다.

우선 `main()`에 추가부터 하자.

```
func main() {
	http.HandleFunc("/view/", viewHandler)
	http.HandleFunc("/edit/", editHandler)
	http.HandleFunc("/save/", saveHandler)
	http.ListenAndServe(":8080", nil)
}
```

`editHandler` 함수는 페이지를 읽어 와서 (기존 파일이 없다면 비어있는 새로운 `Page` 구조체를 생성하여) HTML 입력 폼을 보여준다.

```
func editHandler(w http.ResponseWriter, r *http.Request) {
	title := r.URL.Path[lenPath:]
	p, err := loadPage(title)
	if err != nil {
		p = &Page{Title: title}
	}
	fmt.Fprintf(w, "<h1>Editing %s</h1>"+
		"<form action=\"/save/%s\" method=\"POST\">"+
		"<textarea name=\"body\">%s</textarea><br>"+
		"<input type=\"submit\" value=\"Save\">"+
		"</form>",
		p.Title, p.Title, p.Body)
}
```

이 함수는 잘 동작할 것이다. 하지만 HTML을 하드코딩한 것이 보기 흉하다. 당연히 더 나은 방법이 있다.


## `template` 패키지 ##

`template` 패키지는 Go 표준 라이브러리에 들어있다. `template`을 이용하면 HTML을 다른 파일에 분리할 수 있게 되어, 동작에 관한 Go 코드를 고치지 않고도 편집용 페이지의 모양을 바꿀 수 있다.

`template` 패키지를 임포트 목록에 추가한다.

```
import (
	"http"
	"io/ioutil"
	"os"
	"template"
)
```

HTML 입력 폼을 담은 템플릿 파일을 만든다. `edit.html`라고 파일을 새로 만들어 다음 내용을 입력한다.

```
<h1>Editing {Title}</h1>

<form action="/save/{Title}" method="POST">
<div><textarea name="body" rows="20" cols="80">{Body|html}</textarea></div>
<div><input type="submit" value="Save"></div>
</form>
```

`editHandler`를 수정하여 기존의 하드코딩된 HTML 대신에 새로 만든 템플릿을 이용하게 만든다.

```
func editHandler(w http.ResponseWriter, r *http.Request) {
	title := r.URL.Path[lenPath:]
	p, err := loadPage(title)
	if err != nil {
		p = &Page{Title: title}
	}
	t, _ := template.ParseFile("edit.html", nil)
	t.Execute(w, p)
}
```

`template.ParseFile` 함수는 `edit.html` 파일의 내용을 읽어서 `*template.Template` 타입으로 반환한다.

`t.Execute` 메소드는 템플릿 내용 중의 `{Title}`과 `{Body}`를 모두 찾아서 `p.Title`과 `p.Body` 값으로 바꿔치기하며, 최종 HTML을 `http.ResponseWriter`에 출력한다.

앞서 템플릿을 만들면서 `{Body|html}`을 사용했었다. `|html` 부분은 템플릿 엔진이 `Body`값을 처리하면서 `html` 형식처리기를 이용하라는 의미이다. 이렇게 하면 HTML문자들을 이스케이프(escape)한다. (즉, `>`를 `&gt;`로 바꾼다.) HTML 이스케이프를 해야 사용자가 입력한 데이터가 HTML을 엉망으로 만드는 것을 막을 수 있다.

이제 `fmt.Sprintf`를 사용하는 코드가 없어졌으므로 `import` 목록에서 `"fmt"`를 제거할 수 있다.

템플릿을 사용하는 김에 `viewHandler`에서 사용하기 위한 `view.html`도 만들자.

```
<h1>{Title}</h1>

<p>[<a href="/edit/{Title}">edit</a>]</p>

<div>{Body}</div>
```

`viewHandler`도 수정한다.

```
func viewHandler(w http.ResponseWriter, r *http.Request) {
	title := r.URL.Path[lenPath:]
	p, _ := loadPage(title)
	t, _ := template.ParseFile("view.html", nil)
	t.Execute(w, p)
}
```

각 핸들러에서 템플릿 관련 코드가 거의 똑같다. 템플릿 처리 코드를 따로 묶어서 함수로 만들면 중복을 제거할 수 있다.

```
func viewHandler(w http.ResponseWriter, r *http.Request) {
	title := r.URL.Path[lenPath:]
	p, _ := loadPage(title)
	renderTemplate(w, "view", p)
}

func editHandler(w http.ResponseWriter, r *http.Request) {
	title := r.URL.Path[lenPath:]
	p, err := loadPage(title)
	if err != nil {
		p = &Page{Title: title}
	}
	renderTemplate(w, "edit", p)
}

func renderTemplate(w http.ResponseWriter, tmpl string, p *Page) {
	t, _ := template.ParseFile(tmpl+".html", nil)
	t.Execute(w, p)
}
```

이제 핸들러 코드가 더 짧고 간단해졌다.

## 만들어지지 않은 페이지 처리 ##

`/view/APageThatDoesntExist`로 접속하면 어떻게 될까? 지금 상태로는 프로그램이 죽는다. `loadPage`에서 에러를 반환하는데 그것을 무시했기 때문이다. 만약 페이지가 아직 없다면 페이지 내용을 입력할 수 있도록 편집 페이지로 리다이렉트해야 한다.

```
func viewHandler(w http.ResponseWriter, r *http.Request) {
        title := r.URL.Path[lenPath:]
        p, err := loadPage(title)
        if err != nil {
                http.Redirect(w, r, "/edit/" + title, http.StatusFound)
                return
        }
        renderTemplate(w, "view", p)
}
```

`http.Redirect` 함수는 HTTP 응답에 `http.StatusFound`(302) 상태코드와 `Location` 헤더를 추가한다.

<a href='Hidden comment: 
원문의 코드가 글의 진행 상 맞지 않는 부분이 있어서 수정
'></a>

## 페이지 저장 ##

`saveHandler` 함수는 전송된 폼 데이터를 처리한다.

```
func saveHandler(w http.ResponseWriter, r *http.Request) {
	title := r.URL.Path[lenPath:]
	body := r.FormValue("body")
	p := &Page{Title: title, Body: []byte(body)}
	p.save()
	http.Redirect(w, r, "/view/"+title, http.StatusFound)
}
```

페이지 제목(URL로 전달됨)과 폼의 유일한 필드인 `body`를 새 `Page`에 저장한다. `save()` 메소드를 호출하여 파일에 기록하고, 클라이언트는 `/view/` 페이지로 리다이렉트된다.

`FormValue`가 반환한 값은 `string` 타입이다. `Page` 구조체를 만드려면 `[]byte`로 변경해야 한다. `[]byte(body)`와 같이 변환하면 된다.

## 에러 처리 ##

지금까지 작성한 프로그램에는 에러를 그냥 무시한 곳이 몇 군데 있다. 이것은 좋지 못한 습관이다. 하나의 에러가 프로그램 전체를 죽게 만들기도 하기 때문이다. 에러를 처리하여 사용자에게 에러 메시지를 보여주는 것이 더 나은 방법이다. 그러면 문제가 생겼을 때 서버는 계속 동작할 수 있고 사용자는 문제를 알아차릴 수 있게 된다.

먼저 `renderTemplate`의 에러를 처리해보자.

```
func renderTemplate(w http.ResponseWriter, tmpl string, p *Page) {
	t, err := template.ParseFile(tmpl+".html", nil)
	if err != nil {
		http.Error(w, err.String(), http.StatusInternalServerError)
		return
	}
	err = t.Execute(w, p)
	if err != nil {
		http.Error(w, err.String(), http.StatusInternalServerError)
	}
}
```

`http.Error` 함수는 지정된 HTTP 응답 코드(이 예제에서는 "Internal Server Error")와 에러 메시지를 전송한다. 이 함수를 분리하기로 한 결정이 벌써 효과를 발휘한다.

이제 `saveHandler`를 수정해보자.

```
func saveHandler(w http.ResponseWriter, r *http.Request) {
	title, err := getTitle(w, r)
	if err != nil {
		return
	}
	body := r.FormValue("body")
	p := &Page{Title: title, Body: []byte(body)}
	err = p.save()
	if err != nil {
		http.Error(w, err.String(), http.StatusInternalServerError)
		return
	}
	http.Redirect(w, r, "/view/"+title, http.StatusFound)
}
```

`p.save()`에서 에러가 발생하면 그것이 사용자에게 보고될 것이다.

## 템플릿을 캐쉬에 저장하기 ##

우리 코드에는 그다지 효율적이지 않은 부분이 있다. 바로 `renderTemplate`에서 `ParseFile`을 매번 호출하는 것이다. 이 보다는 프로그램을 시작할 때 각 템플릿에 대해 `ParseFile`을 한번씩만 호출한 뒤, 결과값이 `*Template`값을 자료구조로 저장해뒀다가 나중에 사용하는 것이 더 나을 것이다.

그러기 위해 `templates`라는 맵(map) 타입의 전역 변수를 만들어 여기에 `*Template` 값을 저장한다. 맵의 키(key)는 `string` 타입이며 템플릿의 이름을 사용한다.

```
var templates = make(map[string]*template.Template)
```

이제 `init` 함수를 만든다. 이 함수는 프로그램 초기화 단계에서 `main`에 앞서 호출될 것이다. `template.MustParseFile` 함수는 `ParseFile` 함수의 래퍼함수인데 에러 코드를 반환하는 대신 문제가 생기면 패닉(panic)을 일으킨다. 여기서는 패닉이 적당하다. 템플릿을 읽어들이지 못했을 때는 그냥 프로그램을 종료하는 것 말고는 달리 방법이 없기 때문이다.

```
func init() {
	for _, tmpl := range []string{"edit", "view"} {
		templates[tmpl] = template.MustParseFile(tmpl+".html", nil)
	}
}
```

`for` 반복문에 사용된 `range`는 템플릿 이름을 담고 있는 배열을 순회하기 위한 것이다. 나중에 템플릿을 더 추가하고 싶으면 배열에 템플릿 이름만 추가하면 된다.

이제 `renderTemplate`을 수정하여 `templates`에 저장해놓은 `Template` 중에서 적당한 것을 찾아 `Execute`를 호출하도록 만들자.

```
func renderTemplate(w http.ResponseWriter, tmpl string, p *Page) {
	err := templates[tmpl].Execute(w, p)
	if err != nil {
		http.Error(w, err.String(), http.StatusInternalServerError)
	}
}
```

## 유효성 검증 ##

이미 알아차렸을지 모르겠지만 이 프로그램은 심각한 보안상의 결함을 가지고 있다. 바로 사용자가 임의의 경로로 파일을 읽고 쓰고 할 수 있다는 점이다. 이 문제를 피하려면 정규표현식으로 제목 부분을 검증하는 함수를 작성하면 된다.

우선 `"regexp"`를 `import` 목록에 추가한다. 그런 다음 검증에 사용할 정규표현식을 저장하는 전역변수를 생성한다.

```
var titleValidator = regexp.MustCompile("^[a-zA-Z0-9]+$")
```

`regexp.MustCompile` 함수는 정규표현식을 파싱하고 컴파일하여 `regexp.Regexp` 값을 반환한다. `MustCompile`은 `template.MustParseFile`과 마찬가지로 문제가 생겼을 때 패닉을 일으킨다는 점에서 `Compile` 함수와 구별된다. (`Compile`함수는 `os.Error` 를 두번째 인자로 반환한다.)

새로 만들게 될 함수는 요청 URL에서 제목 문자열을 추출한 다음 그것을 `titleValidator`의 표현식으로 검증한다.

```
func getTitle(w http.ResponseWriter, r *http.Request) (title string, err os.Error) {
	title = r.URL.Path[lenPath:]
	if !titleValidator.MatchString(title) {
		http.NotFound(w, r)
		err = os.NewError("Invalid Page Title")
	}
	return
}
```

제목이 유효하면 에러 값으로는  `nil`을 반환한다. 유효하지 않다면 HTTP 연결로 "404 Not Found" 에러를 전송하고 핸들러에게는 에러를 반환한다.

각 핸들러는 `getTitle` 함수를 호출하도록 수정한다.

```
func viewHandler(w http.ResponseWriter, r *http.Request) {
	title, err := getTitle(w, r)
	if err != nil {
		return
	}
	p, err := loadPage(title)
	if err != nil {
		http.Redirect(w, r, "/edit/"+title, http.StatusFound)
		return
	}
	renderTemplate(w, "view", p)
}

func editHandler(w http.ResponseWriter, r *http.Request) {
	title, err := getTitle(w, r)
	if err != nil {
		return
	}
	p, err := loadPage(title)
	if err != nil {
		p = &Page{Title: title}
	}
	renderTemplate(w, "edit", p)
}

func saveHandler(w http.ResponseWriter, r *http.Request) {
	title, err := getTitle(w, r)
	if err != nil {
		return
	}
	body := r.FormValue("body")
	p := &Page{Title: title, Body: []byte(body)}
	err = p.save()
	if err != nil {
		http.Error(w, err.String(), http.StatusInternalServerError)
		return
	}
	http.Redirect(w, r, "/view/"+title, http.StatusFound)
}
```


## 함수 리터럴과 클로저 적용 ##

각 핸들러가 에러 조건을 처리하는 부분에서 같은 코드가 반복된다. 유효성을 검증하고 에러를 확인하는 함수로 핸들러를 감쌀 수 있다면 어떨까? Go의 함수 리터럴(literal)이라는 강력한 추상화 방법을 이용하면 가능하다.

우선 각 핸들러 함수를 수정하여 제목을 인자로 받게 만든다.

```
func viewHandler(w http.ResponseWriter, r *http.Request, title string)
func editHandler(w http.ResponseWriter, r *http.Request, title string)
func saveHandler(w http.ResponseWriter, r *http.Request, title string)
```

이제 위의 함수들과 같은 타입의 _함수를 인자로 받아서_ `Http.HandlerFunc` 타입의 함수를 반환하는 래퍼 함수를 정의한다. (반환된 함수는 `Http.HandlerFunc` 타입을 필요로 하는 곳에 사용할 수 있다.)

```
func makeHandler(fn func (http.ResponseWriter, *http.Request, string)) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		// 여기에서 요청 URL로부터 페이지 제목을 추출한 다음
		// 'fn' 함수를 호출한다.
	}
}
```

반환하는 함수는 클로저(closure)라고 불린다. 함수 정의 바깥에 있는 값들을 모두 포함(enclose)하기 때문이다. 이 경우에는 `fn` 변수(`makeHandler` 함수의 유일한 인자)가 클로저에 포함된다. `fn`은 저장, 편집, 보기 핸들러 중 하나가 될 것이다.

이제 `getTitle` 함수의 코드를 여기에 옮긴다. (약간 수정했다)

```
func makeHandler(fn func(http.ResponseWriter, *http.Request, string)) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		title := r.URL.Path[lenPath:]
		if !titleValidator.MatchString(title) {
			http.NotFound(w, r)
			return
		}
		fn(w, r, title)
	}
}
```

`makeHandler`가 반환하는 클로저는 `http.ResponseWriter`와 `http.Request`를 인자로 받는 함수(즉 `http.HandlerFunc`)이다. 클로저는 `title`을 요청 경로로부터 추출하고, `titleValidator` 정규표현식으로 유효성을 검증한다. `title`이 유효하지 않으면 `http.NotFound` 함수를 호출하여 `ResponseWriter`에 에러 메시지를 전달한다. `title`이 유효하면 `ResponseWriter`와 `Request`, 그리고 `title` 세 값을 인자로 하여 자신이 포함하고 있는 `fn` 함수를 호출한다.

이제 `main`에서 핸들러 함수들을 `makeHandler` 함수로 한 번 감싼 다음 `http` 패키지에 등록한다.

```
func main() {
	http.HandleFunc("/view/", makeHandler(viewHandler))
	http.HandleFunc("/edit/", makeHandler(editHandler))
	http.HandleFunc("/save/", makeHandler(saveHandler))
	http.ListenAndServe(":8080", nil)
}
```

마지막으로 핸들러 함수들에서 `getTitle` 함수 호출 부분을 제거한다. 더 간단해졌다.

```
func viewHandler(w http.ResponseWriter, r *http.Request, title string) {
	p, err := loadPage(title)
	if err != nil {
		http.Redirect(w, r, "/edit/"+title, http.StatusFound)
		return
	}
	renderTemplate(w, "view", p)
}

func editHandler(w http.ResponseWriter, r *http.Request, title string) {
	p, err := loadPage(title)
	if err != nil {
		p = &Page{Title: title}
	}
	renderTemplate(w, "edit", p)
}

func saveHandler(w http.ResponseWriter, r *http.Request, title string) {
	body := r.FormValue("body")
	p := &Page{Title: title, Body: []byte(body)}
	err := p.save()
	if err != nil {
		http.Error(w, err.String(), http.StatusInternalServerError)
		return
	}
	http.Redirect(w, r, "/view/"+title, http.StatusFound)
}
```

## 실제로 해 보자! ##


[최종 코드 보기](http://golang.org/doc/codelab/wiki/final.go)

다시 컴파일하여 앱을 실행해보자.

<pre>
$ 8g wiki.go<br>
$ 8l wiki.8<br>
$ ./8.out<br>
</pre>

http://localhost:8080/view/ANewPage 주소를 웹브라우저로 열어보면 페이지 편집 화면이 나와야 한다. 양식에 아무 내용이나 입력한 다음 'Save'를 클릭하면 새로 만들어진 페이지가 나와야 한다.

## 추가 작업 ##

여러분 스스로 해볼만한 간단한 작업들이다.

  * 템플릿을 `tmpl/` 아래에 저장하고, 페이지 데이터는 `data/` 아래에 저장한다.
  * 웹의 루트(/)로 접속하면 `/view/FrontPage`로 리다이렉트되도록 하는 핸들러를 추가한다.
  * 페이지 템플릿을 유효한 HTML로 만들고 CSS도 추가하여 멋지게 모양을 낸다.
  * 페이지 간 링크 기능을 구현한다. 페이지 내용에 `[PageName]`이 나타나면 `<a href="/view/PageName">PageName</a>`로 변환하는 식이다. (힌트: `regexp.ReplaceAllFunc` 함수를 사용해 보라)