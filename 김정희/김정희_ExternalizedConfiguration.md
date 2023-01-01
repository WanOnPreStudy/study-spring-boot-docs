# **7.2. Externalized Configuration**

스프링은 설정을 외부화가 가능하여 동일한 어플리케이션코드를 다른 환경에서도 띄울 수 있다.

properties 파일, YAML파일, 환경 변수, 커맨드 라인 등을 통해 설정 할 수 있다.

@Value어노테이션을 통해 직접 bean에 주입 가능하다. @ConfigurationProperties을 통해 환경설정 프로퍼티를 java 오브젝트에 셋팅할 수 있다.

spring boot는 내부적으로 정의된 property source순서대로 적용된다.

1. 기본 properties 파일(특정 properties 설정 시에는 SpringApplication.setDefaultProperties)
2. `@PropertySource` 어노테이션을 붙인 @Configuration 클래스

   application context가 새로고침 되지 않으면 property파일이 Enviroment객체에 셋팅되지 않는다.

   [PropertySource (Spring Framework 5.3.22 API)](https://docs.spring.io/spring-framework/docs/5.3.22/javadoc-api/org/springframework/context/annotation/PropertySource.html)

3. application.properties파일같은 config data

(하위 목록 생략)

config data는 아래 순서 우선순위를 가진다.

1. 패키지 내의 Application properties 이나 yaml파일
2. 패키지 내의 `application-{profile}.properties` 이나 yaml파일
3. 패키지 밖의 Application properties 이나 yaml파일
4. 패키지 밖의 `application-{profile}.properties` 이나 yaml파일

## **7.2.1. Accessing Command Line Properties**

command 명령어로 셋팅한 property가 우선적으로 셋팅 된다. `SpringApplication.setAddCommandLineProperties(false)`을 통해 command line 셋팅을 끌 수 있다.

## **7.2.2. JSON Application Properties**

환경변수 및 시스템 프로퍼티가 제한되는 경우, spring boot는 json구조를 사용하도록 제공한다.

## **7.2.3. External Application Properties**

스프링 부트는 자동적으로 application.properties파일이나, application.yaml을 아래 위치에서 찾는다.

1. classpath
    1. root class path
    2. `/config`
2. 현재 디렉토리
    1. 현재 디렉토리
    2. 현재 디렉토리의 `/config` 하위 디렉토리

spring.config.name환경변수를 통해 application 설정 파일 명을 변경 할 수 있다.

```java
java -jar myproject.jar --spring.config.name=myproject
```

## **7.2.4. Encrypting Properties**

스프링 부트는 property값을 암호화 하는 기능은 제공하지 않는다. 하지만, 스프링 환경에 포함된 값을 수정하는 데 필요한 후크 포인트를 제공합니다. EnvironmentPostProcessor 인터페이스를 사용하면 응용 프로그램이 시작되기 전에 환경을 조작할 수 있다.

## **7.2.5. Working with YAML**

YAML파일은 구체적인 계층 구조의 설정 파일이다. SpringApplication 클래스는 클래스 경로에 SnakeYAML 라이브러리가 있을 때마다 속성의 대안으로 YAML을 자동으로 지원한다.

## **7.2.6. Configuring Random Values**

RandomValuePropertySource는 랜덤 값을 주입하는 데 유용하다. 랜던 값으로 integer, long, uuid, string 타입이 가능하다.

```yaml
my:
  secret: "${random.value}"
  number: "${random.int}"
  bignumber: "${random.long}"
  uuid: "${random.uuid}"
  number-less-than-ten: "${random.int(10)}"
  number-in-range: "${random.int[1024,65536]}"
```

int 값의 경우 최소, 최대값을 지정할 수 있다.

## **7.2.7. Configuring System Environment Properties**

스프링 부트는 환경 프로퍼티에 prefix를 설정 할 수 있다. 이 기능은 구성 요구 사항이 서로 다른 여러 Spring Boot 애플리케이션에서 시스템 환경을 공유하는 경우에 유용하다. SpringApplication에 직접 셋팅한다.

예를들어, input이라는 prefix를 사용한다면 remote.timeout는 prefix가 붙어 `input.remote.timeout` 시스템 환경변수가 된다.

## **7.2.8. Type-safe Configuration Properties**

`@Value("${property}")`  사용하여 구성 속성을 주입하는 것은 여러 property파일로 작업 중이거나 데이터가 계층적인 경우에 번거로울 수 있다.