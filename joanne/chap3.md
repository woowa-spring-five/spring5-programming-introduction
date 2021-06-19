# Chap3. 스프링 DI

## 의존 관계란?
의존 관계란, A, B라는 객체가 존재할 때, B 객체의 메서드를 A 객체가 사용하고 있다면 A는 B를 의존하고 있다고 표현할 수 있다.
즉, B 객체에 변경이 발생했을 때 그 영향이 A에게까지 미친다면 `A는 B를 의존한다.`라고 표현할 수 있다.

## 스프링에서의 DI
의존관계 주입이란, 의존 관계에 놓인 객체의 레퍼런스를 외부에서 주입받아 해당 객체와 레퍼런스를 전달받은 객체 간의 의존관계가 동적으로 만들어지는 것을 의미한다.

## 의존성 주입을 실현하는 방법
### 1. 생성자를 통한 주입
```java
public ClassA(ClassB classB) {
    this.classB = classB;
}

ClassA classA = new ClassA(new ClassB());
```

### 2. setter를 통한 주입
```java
public ClassA {
    private ClassB classB;
    
    public void setClassB(ClassB classB) {
        this.classB = classB;    
    }
}

ClassA classA = new ClassA();
classA.setClassB(new ClassB());
```
### 3. @Autowired 어노테이션을 이용하기
`@Autowired`가 붙은 대상에 대해 알맞은 빈을 자동으로 주입한다.
```java
// ClassB는 Bean으로 등록된 상태라고 가정한다.
public ClassA {
    @Autowired
    private ClassB classB;
}
```

## 주입 대상 객체를 모두 빈 객체로 설정해야하나?
주입할 객체가 꼭 스프링 빈이어야 할 필요는 없다. 하지만 `@Autowired`를 통해 필드 주입을 받을 경우에는 주입할 객체가 스프링에 빈으로 등록되어있어야 가능하다.

## 세 방법 중 가장 권장되는 방법은?
> Spring Team recommends: “Always use constructor based dependency injection in your beans. Always use assertions for mandatory dependencies”.


다른 주입 방법보다 생성자 주입을 권장한다. 그 이유는 무엇일까?

생성자 주입이 주는 장점은 다음과 같다.

1. 순환 참조를 방지할 수 있다.


```java
package com.example.playground.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class CarService {

    @Autowired
    private FuelService fuelService;

    public void sayCar() {
        fuelService.sayFuel();
    }
}
```

```java
package com.example.playground.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class FuelService {

    @Autowired
    private CarService carService;

    public void sayFuel() {
        carService.sayCar();
    }
}
```
```java
@SpringBootApplication
public class PlaygroundApplication implements CommandLineRunner {

    @Autowired
    private CarService carService;

    @Autowired
    private FuelService fuelService;

    public static void main(String[] args) {
        SpringApplication.run(PlaygroundApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        carService.sayCar();
        fuelService.sayFuel();
    }
}
``` 

다음과 같이 CarService와 FuelService가 서로를 순환참조하도록 구성하고, 프로그램을 실행시켜보자.
그럼 아무런 오류없이 실행되지만, 다음과 같은 결과를 얻을 수 있다.

```
java.lang.StackOverflowError: null
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
	at com.example.playground.service.CarService.sayCar(CarService.java:13) ~[main/:na]
	at com.example.playground.service.FuelService.sayFuel(FuelService.java:13) ~[main/:na]
... 무한반복
```

하지만 이 둘의 의존성 주입을 생성자를 통해 주입하면, Application 자체가 실행되지 않고 `BeanCurrentlyInCreationException`이 발생한다.
그리고 발생한 오류에 대한 설명은 다음과 같다.
```
***************************
APPLICATION FAILED TO START
***************************

Description:

The dependencies of some of the beans in the application context form a cycle:

   playgroundApplication (field private com.example.playground.service.CarService com.example.playground.PlaygroundApplication.carService)
┌─────┐
|  carService defined in file [/Users/minjeong/Desktop/woowacourse/study/playground/build/classes/java/main/com/example/playground/service/CarService.class]
↑     ↓
|  fuelService defined in file [/Users/minjeong/Desktop/woowacourse/study/playground/build/classes/java/main/com/example/playground/service/FuelService.class]
└─────┘

```

실행 결과에 차이가 발생하는 이유는, 생성자 주입 방법은 필드 주입이나 수정자 주입과 빈 주입하는 순서가 다르기 때문이다.
세터를 통한 주입은 주입 받으려는 빈의 생성자를 호출하여 빈을 찾거나 빈 팩토리에 등록한다. 
그 후 생성자 인자에 사용하는 빈을 찾거나 만든다. 
다음으로 주입하려는 빈 객체의 세터를 호출하여 주입한다.

필드를 통한 주입은 먼저 빈을 생성한 후, 어노테이션이 붙은 필드에 해당하는 빈을 찾아서 주입한다.
즉, 먼저 빈을 생성한 후 필드에 대해서 주입한다.

생성자 주입은 생성자로 객체를 생성하는 시점에 필요한 빈을 주입한다.
생성자의 인자에 사용되는 빈을 찾거나 빈 팩터리에서 만든다. 
그 후 찾은 인자 빈으로 주입하려는 빈의 생성자를 호출한다.
즉, 먼저 빈을 생성하지 않는다.

> 따라서, 순환 참조는 생성자 주입에서만 문제로 인식될 수 있다. 객체 생성 시점에서 빈을 주입하기 때문에 서로 참조하는 객체가 생성되지 않은 상태에서 빈을 참조하기에 오류가 발생한다.
그러므로, 생성자 주입을 통해 순환 참조를 사전에 방지해야한다.

그 외에도 생성자 주입을 사용하게 되면 독립적으로 단위 테스트에서 인스턴스화 할 수 있기 때문에 테스트가 용이하며,
한 개의 컴포넌트가 `@Autowired` 를 통해 수 많은 의존성을 가진 필드를 가질 수 있다. 생성자 주입을 사용하게 되면 생성시 여러 개의 주입 객체를 주입해주어야하므로 의존성이 명시적으로 드러나는 장점을 가진다.

필드 주입과 세터 주입은 해당 필드를 `final`로 선언할 수 없다. 따라서 불변성을 보장할 수 없다.

스프링 레퍼런스에서는 강제화되는 의존성의 경우에는 생성자 주입형태를 사용하고, 선택적인 경우에는 수정자 주입 형태를 사용하는 것을 권장한다. 
즉, 불변 객체나 null이 아님을 보장할 때에는 반드시 생성자 주입을 사용해야한다.
예를 들어, 불변이나 null이 아님이 보장되어야하는 필드를 `@Autowired`를 통해 주입한다면, 세터 주입과 같은 상황에서 해당 필드의 값을 변경할 수 있기 때문에 예상하지 못할 예외가 발생할 수 있다.
하지만 생성자 주입을 사용하면 이를 컴파일 시점에 방지할 수 있다.

### 참고 자료
* https://madplay.github.io/post/why-constructor-injection-is-better-than-field-injection

