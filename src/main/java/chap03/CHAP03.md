# CHAP03 스프링 DI


## 의존이란?
DI는 '의존 주입(Dependency Injection)'. 이 때 의존은 객체 간 의존을 의미한다.

한 클래스가 다른 클래스의 메서드를 실행할 때 이를 '의존한다'고 표현한다. (변경에 따른 영향이 전파되는 관계)

의존 객체를 직접 생성하는 것은 유지보수 관점에서 문제점을 유발할 수 있다. 그렇기에 스프링은 DI를 이용해 의존 객체를 구할 수 있다.


## DI를 통한 의존 처리
DI는 의존하는 객체를 직접 생성하는 대신 의존 객체를 전달받는 방식을 사용한다.

의존 처리 방식에는 세 가지가 있다.
1. 생성자를 통한 의존성 주입(Constructor based Injection)
2. 필드 주입(Filed Injection)
3. 수정자 주입(Setter based Injection)


## DI와 의존 객체 변경의 유연함
DI를 사용하면 객체를 사용하는 클래스가 세 개여도 변경할 곳은 의존 주입 대상이 되는 객체를 생ㅅ어하는 코드 한 곳뿐이다.

따라서 의존 객체를 직접 생성했던 방식에 비해 변경할 코드가 한 곳으로 집중된다. (MemberDao 예시)


## @Autowired
스프링 설정 클래스 필드에 @Autowired 애노테이션을 붙이면 해당 타입의 빈을 찾아서 필드에 할당한다.

스프링 빈에 의존하는 다른 빈을 자동으로 주입하고 싶을 때 사용한다.

설정 클래스가 두 개 이상인 경우 파라미터로 설정 클래스를 추가로 전달하면 스프링 컨테이너를 생성할 수 있다.
```java
ex) ctx = new AnnotationConfigApplicationContext(AppCtx.class, AppCtx2.class);
```


## @Import
두 개 이상의 설정 파일을 사용하는 또 다른 방법. @Import 애노테이션은 함께 사용할 설정 클래스를 지정한다.
배열을 이용해 두 개 이상의 설정 클래스도 지정 가능하다.


@Import를 사용해서 포함한 설정 클래스가 다시 @Import를 사용할 수 있다.
이런 경우 설정 클래스를 변경해도 AnnotationConfigApplicationContext를 생성하는 코드는 최상위 설정 클래스 한 개만 사용하면 된다.
```java
import org.springframework.context.annotation.Import;

@Configuration
@Import(AppCtx2.class) // 스프링 컨테이너 생성시 AppCtx2 설정 클래스 지정할 필요 X
public class AppCtx {

    @Bean
    public MemberDao memberDao() {
        return new MemberDao();
    }

    @Bean
    public MemberPrinter memberPrinter() {
        return new MemberPrinter();
    }
}
```

## 주입 객체가 모두 빈 객체일 필요가 있나?
주입할 객체가 꼭 스프링 빈이어야 할 필요는 없다. 일반 객체로 생성해서 주입할 수 있다. 

객체를 스프링 빈으로 등록할 때와 등록하지 않을 떄의 차이는 **스프링 컨테이너가 객체를 관리하는지 여부**이다.

스프링 컨테이너가 제공하는 관리 기능이 필요 없고 getBean() 메서드로 구할 필요가 없다면 반드시 빈 객체로 등록할 필요는 없다.