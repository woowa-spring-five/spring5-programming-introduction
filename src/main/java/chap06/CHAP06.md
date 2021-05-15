# 빈 라이프사이클과 범위

## 컨테이너 초기화와 종료
스프링 컨테이너가 가지는 초기화와 종료라는 라이프사이클의 예시

1. 컨텍스트 객체를 생성하며 스프링 컨테이너를 초기화한다.

컨테이너는 설정 클래스에서 정보를 읽어와 알맞은 빈 객체를 생성하고, 각 빈을 연결하는 작업을 수행한다.

2. 컨테이너를 사용한다.

getBean()과 같은 메서드를 이용해 컨테이너에 보관된 빈 객체를 구한다.

3. 컨테이너 사용이 종료되면 컨테이너를 종료한다. 컨테이너 종료시 등록된 빈 객체들도 소멸된다.

위와 같은 라이프사이클을 볼 때 컨테이너의 라이프사이클에 따라 빈 객체도 생성 및 소멸한다.
```java
// 1. 컨테이너 초기화
AnnotationConfigApplicationContext ctx = 
    new AnnotationConfigApplicationContext(AppContext.class);

// 2. 컨테이너에서 빈 객체를 구해서 사용
Greeter g = ctx.getBean("greeter", Greeter.class);
String msg = g.greet("스프링");
System.out.println(msg);

// 3. 컨테이너 종료
ctx.close();
```

## 스프링 빈 객체의 라이프 사이클
빈 객체의 라이프 사이클

- 객체 생성 -> 의존 설정 -> 초기화 -> 소멸

컨테이너 초기화 시 빈 객체 생성 후 의존을 설정한다. 의존 자동 주입을 통한 의존 설정이 이 시점에 수행되므로 순환 참조가 발생하지 않도록 주의해야 하는 단계라고 생각한다.

## 빈 객체의 생성과 관리 범위
스프링 컨테이너는 빈 객체를 한 개만 생성한다. 따라서 동일한 이름을 갖는 빈 객체를 구하는 경우 동일한 빈 객체를 참조한다.

별도 설정을 하지 않는 경우 빈은 싱글톤 범위를 갖게 된다.

### 프로토타입 범위의 빈의 경우 빈 객체를 구할 때마다 매번 새로운 객체를 생성한다.
특정 빈을 프로토타입 범위로 지정하려면 아래와 같이 "prototype"을 갖는 @Scope 애노테이션을 @Bean 애노테이션과 함께 사용한다.

싱글톤 범위를 명시적으로 지정하고 싶다면? @Scope 애노테이션 값을 "singleton" 으로 지정한다.
```java
import org.springframework.context.annotation.Scope;

@Configuration
public class AppCtxWithPrototype {
    
    @Bean
    @Scope("prototype")
    public Client client() {
        ...
    }
    
}
```
