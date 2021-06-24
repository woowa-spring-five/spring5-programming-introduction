# CHAP02 스프링 시작하기

### @Bean
스프링은 객체를 생성하고 초기화하는 기능을 제공한다. 이 때 스프링이 생성하는 객체를 Bean 객체라고 부르는데,

메서드에 @Bean 애노테이션을 붙여 해당 메서드가 생성한 객체를 스프링이 관리하는 빈 객체로 등록한다.

@Bean 애노테이션을 붙인 메서드의 이름은 빈 객체를 구분할 때 사용한다.

***

- AnnotationConfigApplicationContext 클래스는 설정엣 정보를 읽어와 빈 객체를 생성하고 관리한다.


- AnnotationConfigApplicationContext 클래스는 ApplicationContext라는 인터페이스를 알맞게 구현한 클래스 중 하나이다.


- getBean() 메서드는 AnnotationConfigApplicationContext가 자바 설정을 읽어와 생성한 빈 객체를 검색할 때 사용된다. 


- getBean() 메서드의 **첫 번째 파라미터는 @Bean 애노테이션의 메서드 이름인 빈 객체의 이름**이며, **두 번째 파라미터는 검색할 빈 객체의 타입**이다.


- ApplicationContext는 빈 객체의 생성, 초기화, 보관, 제거 등을 관리하고 있다. 컨테이너라고 부른다.


- 별도 설정을 하지 않을 경우 스프링은 한 개의 빈 객체만을 생성한다. 이 때 빈 객체는 '싱글톤 범위를 갖는다'고 표현한다.


