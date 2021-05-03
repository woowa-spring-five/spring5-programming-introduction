# 김김 1주차 정리

## 스프링 컨테이너 (a.k.a IoC/DI 컨테이너)

- 빈 객체를 생성, 초기화, 보관, 제거 등 관리하는 주체를 스프링 컨테이너라고 한다.
- AnnotationConfigApplicationContext, GenericXmlApplicationContext, GenericGroovyApplicationContext등의 구현체를 통하여 스프링 컨테이너의 설정 정보를 가져올 수 있다
- 별도의 설정을 진행하지 않는다면, 빈 객체는 하 나의 인스턴스만을 생성(싱글톤)한다.
- @Component, @Bean, @Repository, @Controller 등등의 어노테이션들은 해당 클래스를 스프링 컨테이너에 빈 객체로 등록하도록 한다.

### 자주 쓰이는 스프링 빈 어노테이션 종류
#### @Component
- 스프링 빈에 등록하기 위한 가장 기본적인 어노테이션이다.
- 후술할 어노테이션들은 이 어노테이션을 포함한다. 따라서 `@Component`는 DTO, VO 용도의 클래스에 주로 사용된다.

#### @Service
- 비즈니스 로직을 수행하며 영속성 계층(Respository Layer)의 객체를 호출하는 클래스에 사용된다.
- 기능적으로 Component와 다를 것은 없다.

#### @Repository
- DB처리를 위한 DAO(Data Access Object)에 주로 사용된다.
- `Component`의 기능에 더불어, `Unchecked Exception`을 Spring의 `Runtime Exception`인 `DataAccessException`으로 처리할 수 있게 해준다. 데이터베이스를 바꾸면, 그곳에서 발생하는 예외의 종류도 바뀌고, 결국 상단 레이어의 코드가 예외를 잡을 수 없는 상황이 발생할 수 있다. 따라서 데이터베이스 계층에서 발생하는 런타임 에러를 DataAccessException으로 다시 뒤바꾸어 던져주기 때문에, 그 위의 계층의 코드가 바뀌지 않더라도 예외를 처리할 수 있게 된다.

#### @Bean
- 메소드 위에 선언할 수 있는 어노테이션으로, 메소드 단위로 스프링 빈으로 등록할 수 있다.

#### @Configuration
- 설정 정보로 인식된다.

#### @Controller
- 스프링 MVC 컨트롤러에서 사용한다.

## DI (의존성 주입)

- 의존관계에 있는 객체를 직접 생성하지 않고, 외부(프레임워크)로부터 전달받는 방식을 Dependency Injection이라 한다.
- 기본적으로 구현체에 의존하지 않음으로써 변경의 유연함을 얻기 위함에 목적이 있다.

### 의존성 주입 방법

#### 생성자(Constructor ) 주입
- 생성자 위에 Autowired 어노테이션을 붙인다.
- 생성하는 시점에 1번 주입받는다.
- 불변, 필수(final이 붙은 인스턴스 변수, 생성자에 할당 코드가 없으면 컴파일이 되지 않는다) 의존관계에 사용한다.
- 생성자가 클래스에 단 하나 있다면 `@Autowired`를 생략할 수 있다!

#### 수정자(Setter) 주입
- setter 메소드 위에 Autowired 어노테이션을 붙인다.
- 선택, 변경 가능성이 있는 경우 사용한다.

#### 필드(Field) 주입
- 인스턴스 변수의 선언 위에 @Autowired 어노테이션을 붙이는 방식이다.
- 외부에서 변경(setter를 이용한다든가)이 불가능해서 테스트하기 힘들다는 치명적 단점이 존재한다.
- 권장되지 않는 방식이다.
