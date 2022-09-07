# 좋은 객체 지향 설계의 5가지 원칙(SOLID)

* 클린코드로 유명한 로버트 마틴이 좋은 객체 지향 설계의 5가지 원칙을 정리
>• SRP: 단일 책임 원칙(single responsibility principle) <br>
• OCP: 개방-폐쇄 원칙 (Open/closed principle) <br>
• LSP: 리스코프 치환 원칙 (Liskov substitution principle) <br>
• ISP: 인터페이스 분리 원칙 (Interface segregation principle) <br>
• DIP: 의존관계 역전 원칙 (Dependency inversion principle) <br>

## SRP: 단일 책임 원칙(single responsibility principle)

>한 클래스는 하나의 책임만 가져야 한다

하나의 책임이라는 것은 모호하다. 클 수도, 작을 수도 있고, 문맥과 상황에 따라 다르다.

*중요한 기준은 변경이다. 변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것* <br>
ex) UI변경, 객체의 생성과 사용을 분리

## OCP: 개방-폐쇄 원칙 (Open/closed principle)

(가장 중요한 원칙)

>소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다
변경은 안되는데, 기능을 추가할 수 있다

다형성을 활용, 역할과 구현의 분리를 생각 -> 앞에서 본 다형성의 예제들을 생각해보면 됨 <br>
Memory에서 Jdbc로 바꾼건 생각해보면, 기존 코드를 변경한 것은 아니고, 새로 추가해서 만든 것 <br>
인터페이스를 구현한 새로운 클래스를 하나 만들어서 새로운 기능을 구현

* 문제점 <br>
MemberService 클라이언트가 구현 클래스를 직접 선택 <br>
MemberRepository m = new MemoryMemberRepository(); //기존 코드 <br>
MemberRepository m = new JdbcMemberRepository(); //변경 코드 <br>
구현 객체를 변경하려면 클라이언트 코드를 변경해야 한다. <br>

![](그림/개방-폐쇄%20원칙의%20문제점.png)
MemberService의 코드를 바꿔야 한다 -> 여기서 다형성을 사용했지만 OCP 원칙을 지킬 수 없다. <br>

이 문제를 어떻게 해결해야 하나? <br>
객체를 생성하고, 연관관계를 맺어주는 별도의 조립, 설정자가 필요하다 -> 스프링 컨테이너가 해결해줌

## LSP: 리스코프 치환 원칙 (Liskov substitution principle)

* 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀
수 있어야 한다
* 다형성에서 하위 클래스는 인터페이스 규약을 다 지켜야 한다는 것, 다형성을 지원하기 위한 원칙, 인터페이스를 구현한 구현체는 믿고 사용하려면, 이 원칙이 필요하다.
* 단순히 컴파일에 성공하는 것을 넘어서는 이야기
* 예) 자동차 인터페이스의 엑셀은 앞으로 가라는 기능, 뒤로 가게 구현하면 LSP 위반, 느리
더라도 앞으로 가야함 (엑셀을 뒤로 가게 구현하더라도, 컴파일에서는 오류가 안남)

## ISP: 인터페이스 분리 원칙 (Interface segregation principle)

* 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다 <br>
(자동차 인터페이스는 너무 크니까 분리. & 만약 정비쪽에 문제가 생기면 운전쪽은 바꾸지 않고 정비만 바꿔도 됨)
* 자동차 인터페이스 -> 운전 인터페이스, 정비 인터페이스로 분리
* 사용자 클라이언트 -> 운전자 클라이언트, 정비사 클라이언트로 분리
* 분리하면 정비 인터페이스 자체가 변해도 운전자 클라이언트에 영향을 주지 않음
* 인터페이스가 명확해지고, 대체 가능성이 높아진다.

## DIP: 의존관계 역전 원칙 (Dependency inversion principle)

> • 앞에서 이야기한 역할(Role)에 의존하게 해야 한다는 것과 같다. (DIP와 같다) <br>
• 프로그래머는 “추상화에 의존해야지, 구체화에 의존하면 안된다.” 의존성 주입은 이 원칙
을 따르는 방법 중 하나다.

-> 클라이언트 코드가 구현 클래스를 바라보지 말고, 인터페이스만 바라보라는 뜻 <br>
MemberService가 MemberRepository interface만 바라보고 Memory나 Jdbc는 몰라야 된다는 것

* 쉽게 이야기해서 구현 클래스에 의존하지 말고, 인터페이스에 의존하라는 뜻 <br>
(앞의 운전자-자동차, 공연 무대 그림과 같다. 배우도 대본을 보고 연습을 해야지, 특정 사람과 하면 안됨) 

• 객체 세상도 클라이언트가 인터페이스에 의존해야 유연하게 구현체를 변경할 수 있다! 구현체에 의존하게 되면 변경이 아주 어려워진다.


* 그런데 OCP에서 설명한 MemberService는 인터페이스(MemberRepository)에 의존하지만, 구현 클래스(MemoryMemberRepository)도 동시에 의존한다.
* MemberService 클라이언트가 구현 클래스를 직접 선택
* MemberRepository m = new MemoryMemberRepository(); -> 알기만 하면 의존하는 것
* DIP 위반


### 정리
* 객체 지향의 핵심은 다형성
* 다형성 만으로는 쉽게 부품을 갈아 끼우듯이 개발할 수 없다.
* 다형성 만으로는 구현 객체를 변경할 때 클라이언트(MemberService) 코드도 함께 변경된다. 
* 다형성 만으로는 OCP, DIP를 지킬 수 없다.
* 뭔가 더 필요하다