## Index

- [Gradle](#Gradle)
- [Gradle 을 통해 설치한 라이브러리](#Gradle-을-통해-설치한-라이브러리)
- [Get 방식처리](#Get-방식처리)
- [빌드하기](#빌드하기)
- [정적-컨텐츠](#정적-컨텐츠)
- [동적-컨텐츠](#동적-컨텐츠)
- [MVC와-템플릿-엔진](#MVC와-템플릿-엔진)
- [thymeleaf-템플릿-특징](#thymeleaf-템플릿-특징)
- [@RequestParam](#@RequestParam)
- [API](#API)
- [@ResponseBody](#@ResponseBody)
- [HttpMessageConverter](#HttpMessageConverter)
- [@afterEach 테스트 순서 보장](#@afterEach-테스트-순서-보장)
- [Optional<T> 사용을 통한 return 으로 null 방지](#optional-사용을-통한-return-으로-null-방지
#### Gradle

- Spring 에서 Gradle 은 자동으로 빌드를 도와주는 오픈소스 빌드 도구
- 의존 관계를 관리를 해준다
    - web 라이브러리 사용시 필요한 다른 라이브러리들을 다 땡겨와 준다
    - intelij 좌측하단 모서리에 gradle 창에서 dependencies 를 통해 의존 관계의 라이브러리 확인 가능

#### Gradle 을 통해 설치한 라이브러리
1. thymeleaf - html 만들어주는 템플릿 엔진
2. web - 웹 페이지
3. test - 자동으로 들어오는 test

### thymeleaf 템플릿 엔진 동작 확인
- controller - model - viewResolver

컨트롤러는 들어온 요청을 해당하는 모델에 할당하고
모델에서 할당된 작업을 완료 후 나온 결과를 뷰 리졸버에 넘겨주면
뷰 리졸버는 넘겨줄 페이지를 templates 폴더 내에서 찾아 데이터를 전송해준다.

#### Get 방식처리

localhost:8000/hello 에서 hello 를 Get 방식으로 넘겨줄 때에
컨트롤러는 GetMapping 모델 중 hello 라는 이름을 찾아서 맵핑해준다.
우리가 웹에 넘겨줄 데이터가 들어갈 변수명을 attributeName 변수에 담고
데이터를 attributeValue 에 입력해 준다.
return 값은 viewResolver 를 통해 templates/hello.html 를 찾아 넘겨줄 페이지이다.

```JAVA
@GetMapping("hello")
    public String hello(Model model) {
        model.addAttribute("data", "hello!!");
        return "hello";
    }
```

### 빌드하기

- 프로젝트 폴더에서 명령어 입력시 build 폴더가 생성되고 bui`ld/libs 내부에 .jar 파일이 생성된다.
- java -jar 파일명.jar 를 통해 실행

```CMD
// 빌드
./gradlew build
// 이동
cd build/libs
// 실행
java -jar 파일명.jar
```

- 오류 발생시 clean 을 통해 삭제
- clean build 를 통해 삭제 후 재생성

```CMD
// 제거
./gradlew clean
// 제거 후 빌드
./gradlew clean build
```

- jar 파일만 서버에 올려서 실행하면 서버 운영 가능

### 정적 컨텐츠

- 정적 컨텐츠는 아무런 과정 없이 요청이 들어올 시에 클라이언트에게 정적인 html 페이지만 던져준다.
- 스프링에서는 MVC 패턴에서 컨트롤러가 가지고 있는 모델 중 요청하는 페이지를 먼저 확인한 후 없는 경우에 resources/static 에서 해당 페이지 유무를 확인하는 과정을 거친다.


### 동적 컨텐츠
#### MVC와 템플릿 엔진
- 뷰는 페이지를 그리는데 집중 모델과 컨트롤러는 비지니스 로직, 내부적인 것을 처리하는데 집중해야 한다.

##### @RequestParam
- 필수적인 데이터를 Get 방식으로 요청
- hello-mvc 라는 url 에서 name 이라는 파라미터를 요청을 하는데 이때에 required 의 디폴트 값은 true 이므로 name 변수의 파라미터가 넘어오지 않을 경우 에러 발생으로 페이지 출력이 되지 않는다. false 로 변경시 파라미터 유무의 관계없이 페이지가 출력.
- localhost:8080/hello-mvc?name=spring!@!@ 과 같은 문법으로 파라미터 값 입력
- return 시 viewResolver 가 작동해서 뷰를 찾아주고 thymleaf 템플릿에 연결을 시켜준다.


```JAVA
@GetMapping("hello-mvc")
    public String helloMvc(@RequestParam(value = "name", required = false) String name, Model model) {
        model.addAttribute("name", name);
        return "hello-template";
    }
```

- name 위치에 name 이라는 파라미터에 넘겨준 값이 들어온다.
- [thymeleaf-템플릿-특징](#thymeleaf-템플릿-특징)의 tm: 문법을 통해 넘어온 변수에 맞게 출력
```HTML
<p th:text="'hello ' + ${name}">gogogogo ahahahah</p>
```


#### thymeleaf 템플릿 특징

- 서버를 열지 않고도 html 파일을 열면 그 내용을 볼 수 있음
- th: 타임리프 문법을 통해 서버 사이드에서 랜더링시 변수를 통해 들어오는 데이터가 있는 경우 서버에서 넘어온 데이터를 사용
- 서버에서 데이터가 넘어오지 않는 경우 th: 태그 내의 하드코딩 된 데이터와 변수 위치에 null 출력.
- th: 문법이 있는 경우 태그 자체의 텍스트는 프론트에서 작업시 Html을 통해 확인하는 경우에만 사용된다.

```HTML
// 서버에서 name 변수에 데이터가 올 경우 hello ~~~~
// 서베어서 name 변수에 데이터가 안 올 경우 hello null
// hello! empty 는 Html 파일 자체를 열었을 경우에만 출력
<p th:text="'hello ' + ${name}">hello! empty</p>

```

#### API
- 템플릿 엔진과 다르게 뷰를 거치지 않고 전송한 문자가 그대로 전송된다.
- 원래는 뷰 리졸버에게 처리를 하라며 던져 줬지만 [@ResponseBody](#@ResponseBody)가 있다면 http 데이터를 그대로 넘겨준다.

#### @ResponseBody
- Http 의 Body 에 문자 내용을 직접 반환
- 문자 데이터라면 http 형식으로 그냥 넘겨 준다.
- 객체 반환 시 json 데이터로 반환 해준다.
- viewResolver 대신 [HttpMessageConverter](#HttpMessageConverter) 가 동작

#### HttpMessageConverter
- 기존의 템플릿 방식에서는 요청이 들어오면 뷰 리졸버에게 던져 맞는 페이지를 넘겨 주었지만[@ResponseBody](#@ResponseBody) 가 있는 경우에는 뷰 리졸버가 아닌 HttpMessageConverter 가 동작하게 된다.
- HttpMessageConverter
    - StirngHttpMessageConverter
        - 단순 문자인 경우 작동하여 문자열을 넘겨준다.
    - MappingJackson2HttpMessageConverter
        - 객체인 경우 작동하여 json 형태로 넘겨준다.

### 웹 어플리케이션 계층 구조
- 컨트롤러: 웹 MVC 의 컨트롤러 역할
- 서비스: 핵심 비지니스 로직 구현
- 리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
- 도메인: 비지니스 도메인 객체, ex) 회원, 주문, 쿠폰 등 주로 데이터베이스에 저장하고 관리됨

### @afterEach 테스트 순서 보장
같은 클래스 내부에서 메서드의 순서가 보장이 되지 않는다.
테스트가 하나 끝나고 나면 데이터를 클리어 해줘야 한다.
@afterEach 를 통해 메서드가 끝날 때마다 클리어를 시켜준다.

### Optional<T> 사용을 통한 return 으로 null 방지
데이터 베이스에서 데이터를 가져올 때에 null 을 리턴 받을 경우 뜬금 없는 곳에서 null error 가
나타나게 되는 경우가 있다.

이를 통해 생기는 오류를 미연에 방지하고자 Optional<T> 의 사용을 언어적 차원에서 적극 권장한다.

Optional 으로 감싸줌으로 써 불필요한 자원의 낭비라고 생각될 수 있지만 이로 인해 사용되는 자원이 크지 않기에 걱정하지 않아도 된다.
