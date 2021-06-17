# Chap2. 스프링 시작하기

## 의존 전이
의존한 아티팩트가 또다시 의존하고 있는 다른 아티팩트가 있다면 그 아티팩트도 함께 다운로드한다. 
즉. 의존 대상이 다시 의존하는 대상까지도 의존 대상에 포함하기에 이를 `의존 전이`라고 한다.

## @Configuration
`@Configuration` 어노테이션은 해당 클래스를 스프링 설정 클래스로 지정하는 역할을 한다.

스프링은 객체를 생성하고, 초기화하는 기능을 제공하는데 `@Bean`어노테이션을 이용해 객체를 생성하고 초기화할 수 있다.
스프링이 생성하는 객체를 빈 객체라고 부르는데, `@Bean` 어노테이션을 통해 해당 메서드가 생성한 객체를 스프링이 관리하는 빈 객체로 등록할 수 있다.

## 스프링은 객체 컨테이너
스프링의 핵심 기능은 객체를 생성하고 초기화하는 것이다. `AnnotationConfigApplicationContext` 클래스는 자바 클래스에서 정보를 읽어와 객체 생성과 초기화를 수행하는 역할을 한다. 이 클래스는
`BeanFactory` 클래스의 일종인데, `BeanFactory` 인터페이스는 객체 생성과 검색에 대한 기능을 정의한다. 즉, `@Bean` 어노테이션을 이용해 스프링에 등록한 빈
은 BeanFactory에 정의된 getBean() 메서드를 통해 생성된 빈인지 검색할 수 있는 기능을 제공한다. 객체 검색 이외에도 싱글톤/프로토타입 빈인지 확인하는 기능도 제공한다.

여기서 `ApplicationContext`도 등장하는데, `ApplicationContext`란 빈 팩토리를 확장한 IoC 컨테이너이다. 빈 등록 및 관리는 빈 팩토리와 동일하지만 스프링이 제공하는
각종 부가 서비스를 추가로 제공한다.