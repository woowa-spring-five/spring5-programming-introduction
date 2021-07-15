# 1주차 목표

## 2장까지 실습 <br>
[>>여기<<](https://github.com/RinSabbit/spring-starter-study)서 확인할 수 있습니당!!

## 스프링 DI 정리

### @Configuration

@Configuration 어노테이션은 해당 클래스를 **스프링 설정 클래스**로 지정한다. 메인에서 이렇게 사용된다.

```java

@Configuration
public class AppContext {

    @Bean
    public SomeBeanMethod() {
         return new Member();
    }
    
    
/// ...

public class Main {
    AnnotationConfigApplicationContext context =
        new AnnotationConfigApplicationContext(AppContext.class);
    // @Configuration 클래스를 여러 개 설정할 수 있다.
    // 또한 하나의 @Configuration 클래스에서 @Import 를 사용해 다른 @Configuration 클래스를 같이 사용할 수 있다.
}

```

### Bean 이란?

- 스프링이 생성하는 객체
- `@Bean` 어노테이션을 메서드에 붙이면 해당 메서드가 생성한 객체를 스프링이 관리하는 Bean 객체로 등록한다.
- 스프링은 기본적으로 @Bean 어노테이션에 대해 한 개의 빅 객체를 생성한다. 즉, `싱글톤` 객체인 것이다.


### 객체 컨테이너

스프링의 핵심 기능은 `객체를 생성하고 초기화` 하는 것이다. 이와 관련된 기능은 *ApplicationContext* 라는 인터페이스에 정의되어 있다. <br>
*ApplicationContext*는 Bean 객체의 생성, 초기화, 보관, 제거 등을 관리하고 있어 `컨테이너(Container)` 라고도 부른다.


### 스프링 DI

DI는 **Dependency Injection**의 약자로 `의존 주입` 을 의미한다. 여기서 의존은 *객체 간의* 의존을 의미한다.

- 한 클래스가 다른 클래스의 메서드를 실행할 때, 이를 **의존** 한다고 표현한다.
- DI는 의존하는 객체를 직접 생성하는 대신 의존 객체를 **전달**받는 방식을 사용한다.
- 왜 전달을 받나? 직접 생성하면 유지보수 관점에 문제점을 유발할 수 있기 때문이다. 즉, 변경의 유연함을 갖기 위해서 DI를 사용한다.


DI 방식은 다음과 같다.
1. 생성자 방식
2. Setter 메서드 방식

### 주입 대상 객체를 모두 Bean 객체로 설정해야 하나?

아니다. 주입할 객체가 꼭 스프링 Bean 일 필요는 없다. 빈으로 등록하지 않고 일반객체로 생성해서 주입할 수 있다. <br>
객체가 스프링 빈으로 등록할 때와 등록하지 않을 때의 차이는 `스프링 컨테이너가 객체를 관리하는지 여부` 이다. <br>
스프링 컨테이너는 **자동 주입, 라이프사이클 관리 등** 단순 객체 생성 외에 객체 관리를 위해 다양한 기능을 제공하는데, 빈으로 등록된 객체에만 이 기능을 적용한다. <br>
최근에는 의존 자동 주입 기능을 프로젝트 전반에 걸쳐 사용하는 추세이기에 의존 주입 대상은 스프링 Bean으로 등록하는 게 일반적이다.

### 나중에 더 자세히 배우게 될 것

- @Autowired 




