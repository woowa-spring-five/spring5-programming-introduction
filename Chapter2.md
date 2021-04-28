# Chapter 2 (스프링 시작하기)

## 1. 스프링 프로젝트 시작하기

- AnnotationConfigApplicationContext 클래스 : 자바 설정에서 정보를 읽어와 빈 객체를 생성하고 관리
 
   -> AppContext 클래스를 생성자 파라미터로 전달
   
   -> AppContext에서 정의한 @Bean 설정 정보를 읽어와 객체 생성하고 초기화
   
   -> getBean("@Bean으로 설정한 메소드명", 반환값에 해당하는 클래스명.class)을 통해 bean 객체를 검색

## 2. 스프링은 객체 컨테이너
 
- AnnotationConfigApplicationContext는 ApplicationContext 인터페이스를 구현한 클래스
 
   -> 자바 클래스에서 정보를 읽어와 객체 생성과 초기화를 수행
   
```
AnnotationConfigApplicationContext -> GenericApplicationContext ->
AbstractApplicationContext -> DefaultResourceLoader -> ResourceLoader

IntelliJ를 통해 본 계층도는 책이랑 달랐다. 메이븐과 그레이들의 차이인가?
```
- ApplicationContext : 빈 객체의 생성, 초기화, 보관, 제거 등을 관리하므로 Container라고도 부른다.

#### 2.1 싱글톤 객체
- 별도 설정을 하지 않을 경우 스프링은 한 개의 bean 객체만을 생성
- 이 때 빈 객체는 'singleton 범위를 가진다' 라고 표현한다.