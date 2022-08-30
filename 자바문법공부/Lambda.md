윤성우 Java , Chapter 26. 람다 p.658

람다의 사용
Lambda3.java
```
interface Printable {   // 추상 메소드가 하나인 인터페이스
    void print(String s);
}

class Lambda3 {
    public static void main(String[] args) {
        Printable prn = (s) -> {System.out.println(s);};    // 람다식
        prn.print("What is Lambda?");
    }
}
```

람다의 생성 원리
맨 처음, 익명 클래스로부터 시작.<br>

```
Printable prn = new Printable() {   // 익명 클래스
    public void print(String s) {
        System.out.println(s);
    }
}
```

없어도 괜찮은, 유추 가능한 부분 제외.
```
Printable prn =                     // 익명 클래스
    public void print(String s) {
        System.out.println(s);
    };
```

다 유추가능하지만, s가 불분명함
```
Printable prn =  { System.out.println(s); };
```

s는 매개변수라는 걸 표시.
```
Printable prn = (String s) -> { System.out.println(s); };
```

Printable 인터페이스의 추상 메소드를 관찰하여 매개변수 s가 String형 임을 알 수 있으니까, 줄이는 것을 허용
```
Printable prn = (s) -> { System.out.println(s); };
```

중괄호, 매개변수 형 생략, 매개변수 소괄호 생략 가능.
```
Printable prn = s -> System.out.println(s);
```

*매개변수가 둘이고 반환하는 람다식*

```
(a, b) -> { return a + b; }
(a, b) -> a + b;
```

*매개변수가 없는 람다식*

```
() -> { 
    Random rand = new Random();
    return rand.nextInt(50);
}
```