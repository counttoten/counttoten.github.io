---
title: "스프링의 IoC와 DI"
date: 2024-08-19 22:30:50 +0900
categories: [Development, Spring]
tags: [TIL2024, Spring, Java]
---
# IoC 컨테이너, DI

> **참고 자료**
> 
> 아래의 블로그 글들을 읽으며 작성하였습니다.
> - [[Spring] Spring의 IoC(Inversion of Control)과 Bean](https://chanhuiseok.github.io/posts/spring-4/)
> - [[스프링-기본] IoC, DI, 컨테이너](https://m.blog.naver.com/aozp73/222998032178)
> - [[Spring DI/IoC] 스프링의 의존성 주입 (1) - 의존성 주입 방법](https://velog.io/@ohzzi/Spring-DIIoC-스프링의-의존성-주입)

## 1. IoC (Inversion of Control; 제어의 역전)

### IoC란?

IoC란 프로그램의 제어 흐름을 개발자가 아닌 프레임워크나 컨테이너가 담당하는 프로그래밍 원칙이다.

객체의 생성 및 생명 주기를 개발자가 아닌 프레임워크가 관리하도록 하여 객체 간의 결합도를 줄이고 유연한 코드를 작성할 수 있게 한다.

- 기존 방식
    - 객체와 그 의존성들을 클래스 내부에서 직접 생성하여 사용한다.
    - 객체 간 결합도가 높아지며, 유연성이 떨어진다.
    
    ```java
    public class OwnerController {
    	private OwnerRepository ownerRepository = new OwnerRepository();
    }
    // OwnerController가 필요한 OwnerRepository 객체를 직접 생성한다.
    ```
    
- 스프링에서는
    - 객체를 생성 후 그 의존성들을 외부에서 주입받는다.
    - 의존성 객체를 클래스 내부에서 직접 만드는 것이 아니라 제어권을 스프링에게 위임하여 스프링이 만들어 놓은 객체를 주입받는다.
    - 객체 간의 결합도를 낮춘다.
    
    ```java
    public class OwnerController (OwnerRepository clinicService, VisitRepository visits) {
    	this.owners = clinicService;
    	this.visits = visits;
    }
    // OwnerController의 생성자에서 OwnerRepository를 인자로 받고, owners에 담고 있다. (생성자 주입 방식 (추후 설명))
    // 이는 객체를 직접 생성하지 않고, 외부의 객체를 받고 있는 것이다.
    ```
    
    ⇒ 스프링이 모든 의존성 객체를 실행 시에 다 만들어주고 필요한 곳에 주입 시켜 줌으로써 객체들은 Singleton 패턴을 따르게 된다.
    

### ➕ 프레임워크 vs 라이브러리

프레임워크는 IoC가 적용된 대표적인 기술이다. **프레임워크는 내가 작성한 코드를 제어하고 대신 실행**한다. 

예를 들면 스프링 프레임워크에서는 Controller, Service 같은 객체들의 동작을 우리가 직접 구현하기는 하지만, 해당 객체들이 어느 시점에서 호출될 지는 개발자가 제어하지 않는다. 스프링 프레임워크가 요구하는 대로 객체를 생성하면, 프레임워크가 해당 객체들을 가져다가 생성하고 호출하고 소멸시킨다.

이 과정에서 개발자는 제어권을 프레임워크에 넘기게 되므로 제어의 역전이 발생한다고 할 수 있다.

**내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그건 라이브러리다**. 개발자는 특정 기능을 수행하기 위해 필요한 시점에 라이브러리를 호출하여 사용한다. 즉, 언제 어떤 라이브러리를 호출하여 어떤 방식으로 사용할지는 전적으로 개발자의 결정에 달려 있기 때문에 프레임워크와 완전 다르다고 볼 수 있다.

## 2. DI (Dependency Injection; 의존성 주입)

DI(의존성 주입)란 객체를 직접 생성하는 것이 아니라 외부에서 생성한 후 주입해주는 방식이다. 앞서 말한 IoC가 제어의 역전이라는 큰 개념이라면, DI는 그 개념을 구체적으로 실현하는 방법으로, IoC의 일종이라 볼 수 있다.

의존성이란 한 객체가 다른 객체에 의존하는 관계를 말한다.
클래스 A가 B를 사용하면, A는 B에 의존한다고 말할 수 있다. 이렇게 의존 관계를 가지는 객체들은 의존성 주입을 통해 결합도를 낮추는 것이 중요하다. A와 B의 결합도가 강하면 강할수록 B를 변경할 때마다 A에 영향을 크게 미칠 수 있기 때문이다.

### 의존성 주입 방법

1. 생성자 주입 (Constructor Injection)
    - 스프링에서 권장하는 방식이다.
    - 객체가 불변 상태를 유지할 수 있고, 테스트하기 용이하다.
    
    ```java
    public class A {
    	private B b;
    
    	public A(B b) {
    		this.b = b;
    	}
    }
    ```
    
2. Setter 주입 (Setter Injection)
   - 객체가 생성된 후에도 의존성을 주입할 수 있다.
   - 의존성을 선택적으로 주입해도 될 때 사용하기 용이하다.
    
    ```java
    public class A {
        private B b;
        
        public void setB(B b) {
            this.b = b;
        }
    }
    ```
    
3. 인터페이스 주입 (Interface Injection)
    - 어떤 의존성을 주입할 것인지를 인터페이스에 명시한다.
    - 의존성을 주입받는 클래스는 해당 인터페이스의 구현체로 만든다.
    - 잘 사용되지 않는다.
    
    ```java
    public interface BInjection {
        void inject(B b);
    }
    
    public A implements BInjection {
        private B b;
        
        @Override
        public void inject(B b) {
            this.b = b;
        }
    }
    ```
    

## 3. Spring DI Container & Bean

객체를 생성하고, 관리하면서 의존관계를 연결해 주는 것을 IoC 컨테이너 혹은 DI 컨테이너라고 한다.

*IoC는 매우 느슨하게 정의된 용어이기에 스프링을 IoC 컨테이너라고 하기에는 스프링이 제공하는 기능의 특징을 명확하게 설명하지 못 한다. 따라서 스프링이 제공하는 IoC 방식의 핵심을 짚어주는 의존관계 주입이라는 좀 더 명확한 의도가 명확히 드러나는 이름을 사용하기 시작했다. 그래서 주로 IoC 컨테이너라고 불리던 스프링이 지금은 의존관계 주입 컨테이너 또는 DI 컨테이너라고 더 많이 불리고 있다고 한다.*

*나는 일단 Spring DI Container라고 표기하겠다.*

### 스프링 DI 컨테이너와 Bean
스프링 DI 컨테이너가 관리하는 객체들을 Bean이라고 부른다. 스프링 DI 컨테이너는 Bean들의 의존성을 관리하고, 객체를 만들어 주며, Bean으로 등록한다.

스프링 DI 컨테이너로는 둘 중 하나를 사용한다.

- `BeanFactory` 혹은 `ApplicationContext`
    - ApplicationContext는 BeanFactory를 상속받는다.
    - `BeanFactory`는 Lazy-loading 방식을 사용하여, 빈에 대한 요청이 들어올 때에 빈 객체의 인스턴스를 생성하고 등록하지만, `ApplicationContext`는 Pre-loading 방식으로 컨텍스트가 로딩될 때 모든 빈에 대해 인스턴스를 생성하고 등록해둔다는 차이가 있다. 스프링에서는 공식적으로 대부분의 경우에 `BeanFactory` 대신 `ApplicationContext`를 사용하기를 권장하고 있다.

### 스프링에서의 Bean 의존성 주입
> 스프링에서의 의존성 주입은 반드시 Bean으로 등록된 객체들끼리만 가능하다. 
{: .prompt-callout}

**예시**

```java
// 주입받는 객체
@Controller
public class OwnerController {
	// 생성자에서 의존성 주입이 일어나고 있다.
	public OwnerController(OwnerRepository clinicService, VisitRepository visits) {
		this.owners = clinicService;
		this.visits = visits;
	}
	...
}

// 주입되는 객체
public interface OwnerRepository extends Repository<Owner, Integer> {
	...
}
```

- `OwnerController`, `OwnerRepository` 모두 Bean이어야 한다.

### 특정 객체를 Bean으로 등록하는 방법

**Component Scanning**

- `@Component` 어노테이션을 활용하여 개발자가 직접 작성한 Class를 Bean으로 등록해준다.
- 스프링에는 `@Component` 어노테이션이 붙어 있는 클래스를 모두 찾아, 그 클래스의 인스턴스를 만들고, Bean으로 등록해 주는 작업을 하는 처리기가 존재한다.
    - `@ComponentScan`가 딸려있는 `@SpringBootApplication` 어노테이션이 프로젝트 최상단에 붙어있기에 프로젝트가 실행될 때 하위 모든 패키지에 대해 Component Scanning이 실행된다.

**컴포넌트 스캔의 대상**
- `@Controller` 어노테이션의 정의를 보면 `@Component` 어노테이션이 붙어있는 것을 볼 수 있다. 따라서 위의 `OwnerController`는 Component Scanning 때 `@Component` 어노테이션을 활용한 것으로 간주되어 Bean으로 등록 되는 것이다.

사실 컴포넌트 스캔은 `@Component` 뿐만 아니라 다음 어노테이션 또한 스캔 대상에 포함한다.
- `@Component` : 컴포넌트 스캔에서 사용
- `@Controller` : 스프링 MVC 컨트롤러에서 사용
- `@Service` : 스프링 비즈니스 로직에서 사용
- `@Repository` : 스프링 데이터 접근 계층에서 사용
- `@Configuration` : 스프링 설정 정보에서 사용