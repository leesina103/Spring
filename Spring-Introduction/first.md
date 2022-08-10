스프링 부트 스타터 사이트 start.spring.io에 들어가서 스프링 프로젝트 생성
자동으로 만들어줌

Project
Maven -> 과거
Gradle -> 요즘 추세, 많이씀 (추천) <br>

Language -> Java<br>

Spring Boot
SNAPSHOT -> 만들고 있는 버전, M1 -> 정식 release된 버전이 아님
정식 release 중에서 가장 높은 버전 선택하기<br>

Project Metadata
Group -> 기업 도메인명같은걸 적어줌 (처음은 상관없으니 hello라 함)
Artifact -> Build된 뒤 나오는 어떤 결과물<br>

Dependencies
스프링 기반으로 프로젝트를 생성을 할건데, 어떤 라이브러리를 땡겨 쓸거냐 하는 것
Spring Web, Thymeleaf (Template Engines) 선택<br>

GENERATE 누르면 다운 후 IntelliJ에서 build.gradle로 열기

열고 파일안에 src -> main, test가 있음
Maven, Gradle 어떤걸 써도 main, test로 나눠져 있음
test밑에는 test코드가 들어있음
-> 있는 이유는 test코드가 정말 중요하기 때문에
main의 resources파일은 java파일을 제외한 모든 파일은 다 여기 존재<br>

build.gradle의 dependencies에 있는 것들을 다운받아서 사용하는데, repositories의 mavenCentral에서 다운받아서 써라는 얘기<br>

.gitignore파일 -> git에는 소스코드를 제외한 결과물은 올라가면 안되는데 그걸 제외해주는 파일

환경설정의 compact middle packages 체크시에 폴더이름을 hello, hellospring에서 합쳐줌<br>

HelloSpringApplication에서 run시키면 port번호 8080(http)뜸
그럼 웹페이지에서 localhost:8080 들어가면 Whitelabel Error Page라고 뜸
중지버튼 누르면 없어짐<br>

라이브러리
spring-boot-starter-web이 필요하지만 의존관계에 있는 것들을 땡겨옴
오른쪽버튼에서 Gradle을 눌러보면 의존관계 확인가능<br>

현업에서는 sysout말고 로그를 이용함

spring-boot-starter-web
    spring-boot-starter-tomcat: 톰캣 (웹서버)
    spring-webmvc: 스프링 웹 MVC
spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View)
spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅
    spring-boot
        spring-core
    spring-boot-starter-logging
        logback, slf4j  

resources/static 폴더에서 index.html파일을 만들어주면 static은 정적인 것이니 변하지 않는 Welcome Page(홈)를 만들 수 있다.<br>

<!DOCTYPE HTML>
<html>
<head>
 <title>Hello</title>
 <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
Hello
<a href="/hello">hello</a>
</body>
</html>

치고 중지하고 키면 만들어짐<br>

Welcome Page는 index.html에서 찾고 없다면 index 템플릿에서 찾는다.

hello.hellospring 밑의 폴더 controller폴더를 만들고 hellocontroller파일 생성
@GetMapping("hello") -> "hello"가 들어오면 밑의 메소드를 호출해준다.
public String hello(Model model) {
    model.addAttribute("data", "hello!!");
    return "hello";

<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<p th:text="'안녕하세요. ' + ${data}" >안녕하세요. 손님</p>     -> ${data}가 위의 77라인의 attributeValue인 hello!!로 치환됨
</body>
</html>

결과 : localhost:8080/hello 로 들어가보면 <br>

동작 환경 그림<br>
![](../spring%20동작%20환경%20그림.png)
스프링 부트는 내장 톰캣 서버를 내장하고 있어서 받으면 helloController에 있는 return값이 hello니까 resources/templates폴더에서 hello.html로 찾아가서 실행해라는 의미 

컨트롤러에서 리턴 값으로 문자를 반환하면 뷰 리졸버( viewResolver )가 화면을 찾아서 처리한다.
스프링 부트 템플릿엔진 기본 viewName 매핑
resources:templates/ +{ViewName}+ .html

빌드하고 실행하기
https://www.inflearn.com/questions/397942   -> 나와 동일한 오류로 안됨

1. 터미널에 접속후 (실패)
cd '이신아의 삶'/'소프트 공부'/'스프링(Spring)' 으로 들어간 후
./gradlew build     -> 빌드 했는데 JAVA_HOME 환경변수 설정하라는 얘기뜸 자꾸
-> 터미널에서는 환경변수 세팅이 안되있어서 그럴 수 있다
cd build/libs 에 들어가면 18M .jar 파일이 만들어져 있음
java -jar hello-spring-0.0.1-SNAPSHOT.jar

or

2. cmd(명령 프롬프트)에서 실행 (성공)
cd Desktop\이신아의 삶\소프트 공부\스프링(Spring)
dir로 폴더 목록 확인
cd hello-spring\hello-spring에서 
gradlew build 실행
cd build/libs 에 들어가면 18M .jar 파일이 만들어져 있음
java -jar hello-spring-0.0.1-SNAPSHOT.jar   -> 여기까지 됬는데 안뜸(해결)
위의 오류홈페이지에서 환경변수 설정방법 3번을 바꿨더니 실행됨
ctrl + c 로 종료

잘안될 시 빌드할때
./gradlew clean build로 해보기
./gradlew clean 하면 build 폴더가 없어짐

터미널이나 cmd에서 실행하고 IntelliJ에서 동시 실행하면 안됨
8080 Port를 동시에 못띄우니까
Port 8080 was already in use. 에러 뜸

# 스프링 웹 개발 기초
크게 3가지가 있음
1. 정적 컨텐츠 -> 파일을 고대로 전달해 주는 것, 위에서 hello 띄우는 것 처럼 고정되있는 것
2. MVC(Model, View, Controller)와 템플릿 엔진 -> 서버에서 html을 동적으로 바꿔서 내리는 것
3. API -> JSON이라는 데이터 구조 포맷으로 클라이언트한테 데이터를 전달하는 방식

*정적 컨텐츠* <br>
resources/static 폴더에서 찾아서 실행
파일을 그대로 내려준다
hello-static.html 라는 이름으로 파일을 만들면 localhost:8080 뒤에 파일이름 작성하여
localhost:8080/hello-static.html 로 들어갈 수 있음

![](../정적%20컨텐츠.png)
주소로 들어가면 1. 첫번째로 컨트롤러에서 찾는다.

*MVC와 템플릿 엔진* <br>
Controller와 resources/template 폴더에서 찾아서 실행
view와 controller를 쪼개는게 일반적
view -> 화면과 관련된 일만
controller -> 서버뒷단에 관련된건 다 controller에서 처리
다 처리하고 화면에서 필요한 것들을 담아 model에게 넘겨주는 패턴을 사용

resources/template/hello-template.html -> view
<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello ' + ${name}">hello! empty</p>
</body>
</html>

view에 있는 hello! empty는 없어도 되지만, 파일 자체의 absolute path를 copy해서 인터넷에 쳐보면 파일을 그냥 여는 것. 
thymeleaf 템플릿의 장점은 html을 쓰고 서버없이 파일을 열어봐도 껍데기를 볼 수 있다( hello! empty)
근데 템플릿 엔진으로써 동작을 하면, 실제로 서버를 타고 돌면 hello + ${name} 값으로 바뀜
<p th:text="'hello ' + ${name}">hello! empty</p>

@Controller
public class HelloController {
 @GetMapping("hello-mvc")
 public String helloMvc(@RequestParam("name") String name, Model model) {
 model.addAttribute("name", name);
 return "hello-template";       -> hello-template으로 간다
 }
}

웹을 열때, localhost:8080/hello-mvc로 열면 warning이 뜸
Required request parameter 'name' for method parameter type String is not present
이유는 : parameter를 안넘겨줘서 그럼
해결법 : localhost:8080/hello-mvc?name=spring!!!!!
-> ?(물음표)뒤에 인자를 넘겨주면 됨

165라인에서 @REquestParam에서 required 기본값이 true여서 무조건 인자를 넘겨야함

![](../MVC,%20템플릿%20엔진%20이미지.png)
이번엔 helloController에 mapping이 되어있어서 그 메소드를 호출해줌, 넘긴 인자를 토대로
viewResolver라는 화면 관리자가 동작을 한다.
templates에서 return값인 hello-template와 동일한 이름을 찾아서 실행, html 변환 후 넘겨줌
정적은 변환하지 않았음

*API* <br>
정적 컨텐츠를 제외하면 MVC나 API 둘 중 하나라고 생각

@GetMapping("hello-string")
   @ResponseBody
   public String helloString(@RequestParam("name") String name) {
        return "hello" + name;  // name에 string을 넣으면 hello string
   }

@ResponseBody -> http는 헤더부와 body부가 있는데, body에 이 데이터를 직접 넣어주겠다는 의미
템플릿 엔진과의 차이는 view가 없이 저 문자 그대로 내려감
페이지 소스보기를 해보면 앞에서는 html태그가 있었는데, 저 문자 하나만 그대로 전달됨 

localhost:8080/hello-api?name=spring!!!!!
json방식으로 나옴 key : value 형식으로
@ResponseBody -> 기본이 json방식

![](../@ResponseBody%20사용%20원리.png)
원래는 viewResolver라는 애가 동작하는데,
@ResponseBody 라는 애가 들어오면, HttpMessageConverter가 동작해서

단순 문자면 -> StringConverter(기본 문자처리)
hello-spring처럼
name으로 문자로 들어오면 http응답에 넣어서 바로 주면 끝인데,

객체 형태면 -> JsonConverter
hello-api처럼
객체로 들어오면 기본 default값으로 데이터를 만들어서 http응답에 반환하는 것이 기본 방식 