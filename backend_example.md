회원 관리 예제 - 백엔드 개발

비즈니스 요구사항 정리
데이터: 회원ID, 이름
기능: 회원 등록, 조회
아직 데이터 저장소가 선정되지 않음(가상의 시나리오)

컨트롤러: 웹 MVC의 컨트롤러 역할
서비스: 핵심 비즈니스 로직 구현 (ex. 회원은 중복이 안됨)
리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
도메인: 비즈니스 도메인 객체, 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨

아직 데이터 저장소가 선정되지 않아서, 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계
데이터 저장소는 RDB, NoSQL 등등 다양한 저장소를 고민중인 상황으로 가정
개발을 진행하기 위해서 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용

domain/Member -> 멤버 저장
repository/ -> 회원 객체를 저장하는 저장소

*회원 도메인과 리포지토리 만들기* <br>
회원 객체, (domain/member)
-> id는 시스템이 정해주는 것
회원 리포지토리 인터페이스, (repository/interface MemberRepository)
-> Optional은 id로 찾을 때 null일 수도 있는데, null을 그대로 반환하지 않고 Optional로 감싸서 반환하는 것
& findById, findByName -> id, name으로 회원 찾는 것
findAll() -> 저장된 모든 리스트 반환
회원 리포지토리 메모리 구현체(repository/class MemoryMemberRepository)
-> static Map -> save할 곳 , sequence : 0,1,2 key값을 생성
findByName의 원리 : 루프를 다 돌면서 member에서 인자로 넘어온 name과 같은지 확인, 같은 경우에만 필터링이 되고, 찾으면 반환. 없으면 Optional에 null이 포함되서 반환

*회원 리프지토리 테스트 케이스 작성* <br>
위에서 작성한 것들을 동작하는지 확인
개발한 기능을 실행해서 테스트 할 때, 자바의 main 메서드를 통해서 실행하거나, 웹 애플리케이션의 컨트롤러를 통해서 해당 기능을 실행한다. 
이러한 방법은 준비하고 실행하는데 오래 걸리고, 반복 실행하기 어렵고 여러 테스트를 한번에 실행하기 어렵다는 단점이 있다.
자바는 JUnit이라는 프레임워크로 테스트를 실행해서 이러한 문제를 해결한다.

Test의 좋은 점은 class를 실행해서(test) 모든 메소드 동작확인가능
가져다 쓸게 아니므로, public 사용 안해도 됨

@Test -> 이 어노테이션을 붙이면 밑의 메소드 실행, 테스트
모든 test는 순서와 상관없이 메소드별로 따로 동작하도록 설계해야 함.
테스트는 각각 독립적으로 실행되어야 한다. 테스트 순서에 의존관계가 있는 것은 좋은 테스트가 아니다
순서는 랜덤으로 실행됨.
findAll이 먼저 실행되면, findByName에서 이미 저장된 다른 객체가 나와버려서 error 뜸
(해결) -> 그래서 @AfterEach를 통해 test하나가 끝나면 data를 clear해줘야 함
@AfterEach 를 사용하면 각 테스트가 종료될 때 마다 이 기능을 실행한다. 여기서는 메모리 DB에 저장된 데이터를 삭제한다.

*회원 서비스 개발* <br>
validateDuplicateMember는 같은 이름이 있는 중복 회원을 안받게 해주는 메소드
위 함수의 설명으로는 
원래 findByName은 Optional로 받는데, 받자마자 확인하는 것
findByName을 하고 그 결과는 Optional멈버니까, 바로 Optioanl멤버.ifPresent하고
이미 있으면 중복이름이 있다는 뜻이니 예외처리.

*회원 서비스 테스트* <br>
test할 때는 처음에 given / when / then 문법으로 하기

join에서 회원가입도 중요하지만, 중복 예외 처리를 하는지도 체크해줘야 함

회원가입()의 setName에서도 spring으로 하면, DB에 메모리가 쌓여서,
중복_회원_예외()에서 member1에서부터 같은 이름으로 걸리기 때문에,
@AfterEach로 함수테스트 할 때마다 DB를 초기화 시켜주기

중복_회원_예외()의 when문에서 에러 메시지를 확인할때 try ~ catch문으로 사용해도 되지만,
assertThrows라는 try ~ catch문을 대신하는 문법을 사용.
return값으로 에러메시지를 받아서 assertThat으로 동일한지 확인

@BeforeEach : 각 테스트 실행 전에 만들어짐, 호출됨
(코드부분에서 헷갈리는 부분 있음 -> 왜 2개가 각각 생성되고 다른 리포지토리인지, 그럼 처음 코드대로 쓰면 2개의 리포지토리 중 어떤걸 사용하는 것인지?)
처음
new MemberService();
new MemoryMemberRepository(); 로 사용하면

MemberService()에서도 new MemoryMemberRepository()를 만들어서 2개가 생성된다. 하지만 우리는 같은 리포지토리로 테스트를 해야하기 때문에, 
MemberService()의 선언부에서 인자를 전달받는 생성자를 만들어서 

memberRepository = new MemoryMemberRepository();
memberService = new MemberService(memberRepository);

이런 형식으로 바꾼다. 

같은 리포지토리가 사용되는걸 MemberService 입장에서 보면 외부에서 MemberRepository를 넣어준다.
이걸 DI(Dependency Injection)이라 한다.

*스프링 빈과 의존관계* <br>

스프일 빈을 등록하는 2가지 방법
1. 컴포넌트 스캔과 자동 의존관계 설정

@Controller라는 어노테이션이 있으면, 
class MemberController 객체를 생성해서 스프링 컨테이너에 넣어두고 관리를 한다. 
스프링 컨테이너에서 스프링 빈이 관리된다.

![](MVC,%20템플릿%20엔진%20이미지.png)
위 사진에서 스프링이 @Controller라는 어노테이션이 있으니까 스프링 컨테이너 안의 녹색 helloController 스프링 빈을 관리한다.

MemberController에서 
MemberService memberService = new MemberService(); 를 가져다 쓸 수도 있다만, 
스프링이 관리를 하게되면, 스프링 컨테이너에 등록을 하고 거기서 받아쓰도록 바꾸어야 함.
여러 컨트롤러에서도 MemberService를 가져다 쓸 수 있는데, 별 기능이 없어서 여러 객체를 생성할 필요가 없다. 하나만 생성해놓고 같이 쓰면 되는 것.

@AutoWired -> 스프링 컨테이너에서 가져다 씀
생성자를 만들고 @AutoWired를 붙였을 때, error가 뜨는 이유는 
MemberService가 스프링 빈으로 등록되어 있지 않아서 가져다 쓸 수 없는 것.

MemberService를 자바 class기 때문에 스프링이 알 방법이 없다.
스프링에게 알려주기 위해서 @Service를 추가해주기

MemoryMemberRepository로 가서 @Repository 추가
(다 이름에 맞게 추가해줌)

그럼 스프링이 처음 뜰 때, Repository, Service, Controller를 다 가지고 올라온다.

![](스프링%20빈%20등록%20그림.png)
Controller와 Service를 연결시켜줄 때 AutoWired사용
Controller의 생성자에다가 AutoWired를 쓰면, 생성이 될 때, 스프링 빈에 등록되어 있는 MemberService객체를 가져다 넣어줌
이것이 DI(Dependency Injection)이다. 의존관계를 주입해줌.

또, memberService는 memberRepository가 필요해서 연결시켜주기 위해 Service의 생성자에 AutoWired사용

컴포넌트 스캔이라 하는 이유는
원래는 @Service 말고 @Component를 써야 하는데, @Service안에 @Component가 포함되어서 가능
@Controller, @Service, @Repository 다 동일

우리가 HelloSpringApplication을 실행했기 때문에 그걸 포함한 hello.hellospring이라는 package에 속하는 하위 파일들만 다 찾아서 어노테이션이 붙은 것들은 다 등록시킨다.
그 이외의 파일들에서 아무리 @Service같은 어노테이션을 붙여도 등록되지 않음.

스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 싱글톤으로 등록한다(유일하게 하나만 등록해서 공유한다)
따라서 같은 스프링 빈이면 모두 같은 인스턴스다.
설정으로 싱글톤이 아니게 설정할 수 있지만, 특별한 경우를 제외하면 대부분 싱글톤을 사용한다

-요약-
스프링이 올라올때, Component관련된 어노테이션이 있으면 그것들을 다 객체를 생성해서 스프링 컨테이너에 등록을 해놓는다.
AutoWierd는 화살표, 연관관계를 연결해줌

2. 자바 코드로 직접 스프링 빈 등록하기
새로운 SpringConfig 라는 Class를 하나 만들고 
@Bean 어노테이션을 통해서 스프링 빈을 등록한다는걸 알려주기
