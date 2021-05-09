# CHAP04 의존 자동 주입


## @Autowired를 이용한 의존 자동 주입 
자동 주입 기능을 사용하면 스프링이 알아서 의존 객체를 찾아서 주입한다.

자동 주입 기능을 사용하기 위해서 의존을 주입할 대상에 @Autowired 애노테이션을 붙이면 된다.
예를들면 아래와 같다.
```java
public class ChangePasswordService {
    @Autowired
    private MemberDao memberDao;
}
```
@Autowired 애노테이션을 붙이면 설정 클래스(@Configuration이 붙은 클래스)에서 의존을 주입하지 않아도 된다.

스프링은 @Autowired가 붙어 있는 타입의 빈 객체를 찾아 필드에 할당한다.

@Autowired 애노테이션은 메서드에도 붙일 수 있다.


## 일치하는 빈이 없다면?
@Autowired 애노테이션을 적용한 대상에 일치하는 빈이 없다면?

당연히 예외가 발생하며 에러메시지를 출력한다.

반대로 @Autowired 애노테이션을 붙인 주입 대상에 일치하는 빈이 두 개 이상이라면?

마찬가지로 해당 타입의 빈이 두 개라는 사실을 알려주며 예외를 발생한다.

자동 주입을 위해서는 해당 타입을 가진 빈이 어떤 빈인지 정확하게 한정해야 하며, 단 하나만 주입해야 한다.


## @Qualifier 애노테이션을 이용한 의존 객체 선택
자동 주입 가능한 빈이 두 개 이상이면 자동 주입할 빈을 지정할 수 있는 방법이 필요하다.

실제로 최근에 진행한 지하철 노선도 미션에서 1,2단계에서 필요한 dao 객체가 각기 달랐는데, @Qualifier 애노테이션을
미리 알았었으면 좋았을 듯 싶다. 

@Qualifier 애노테이션은 두 위치에서 사용된다. 

첫 번째 위치는 @Bean 애노테이션을 붙인 빈 설정 메서드이다.

두 번째 위치는 @Autowired 애노테이션에서 자동 주입할 빈을 선택할 때 사용한다.

```java
@Configuration
public class AppCtx {
    
    @Bean
    public MemberPrinter printer() {
        return new MemberPrinter();
    }
    
    @Bean
    @Qualifier("mprinter")
    public MemberPrinter printer2() {
        return new MemberPrinter();
    }
    
    @Bean
    public MemberInfoPrinter2 infoPrinter() {
        MemberInfoPrinter2 infoPrinter = new MemberInfoPrinter2();
        return infoPrinter();
    }
}
```

동일한 타입의 빈이 두 개 이상 존재하는 경우 @Qualifier 애노테이션을 통해 이름을 다르게 부여해 주입받을 빈을 선택할 수 있다.

부모 클래스가 빈으로 등록되어 있고 부모 클래스를 주입하려는 경우 Exception이 발생한다.

이를 막기 위해 @Qualifier 애노테이션을 사용하거나 자식 클래스를 타입으로 지정한다.


## 자동 주입할 대상이 필수가 아닌 경우
1. @Autowired의 required 값을 false로 지정. 
```java
@Autowired(required = false)
```
2. 스프링 5부터는 의존 주입 대상에 Optional을 사용.
```java
@Autowired
public void setDateFormatter(Optional<DateTimeFormatter> formatter) {
    if (formatter.isPresenet()) {
        this.dateTimeFormatter = formatter.get();
    } else {
        this.dateTimeFormatter = null;
    }
}
```
이 경우 일치하는 빈이 없는 경우 값이 없는 Ooptional을 전달하고 익셉션은 발생하지 않는다.
3. @Nullable 애노테이션을 사용한다.
```java
@Autowired
public void setDateFormatter(@Nullable DateTimeFormatter formatter){
    this.dateTImeFormatter = formatter;
}
```