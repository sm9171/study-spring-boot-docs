# 7. 핵심기능

이 섹션에서는 Spring Boot에 대해 자세히 알아봅니다. 여기에서 사용하고 맞춤화할 수 있는 주요 기능에 대해 알아볼 수 있습니다.
아직 그렇게 하지 않았다면 "Getting Started" 및 "Developing with Spring Boot" 섹션을 읽어 기본 사항에 대해 좋은 기초를 다질 수 있습니다.

## 7.1 스프링어플리케이션

SpringApplication 클래스는 main() 메서드에서 시작되는 Spring 애플리케이션을 부트스트랩하는 편리한 방법을 제공합니다.
많은 상황에서 다음 예제와 같이 정적 SpringApplication.run 메서드에 위임할 수 있습니다.

```java

@SpringBootApplication
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }

}
```

애플리케이션이 시작되면 다음 출력과 유사한 내용이 표시되어야 합니다.

```text
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::      (v2.6.11-SNAPSHOT)

2022-08-17 19:56:59.151  INFO 1279 --- [           main] o.s.b.d.f.s.MyApplication                : Starting MyApplication using Java 1.8.0_345 on myhost with PID 1279 (/opt/apps/myapp.jar started by myuser in /opt/apps/)
2022-08-17 19:56:59.156  INFO 1279 --- [           main] o.s.b.d.f.s.MyApplication                : No active profile set, falling back to 1 default profile: "default"
2022-08-17 19:57:01.554  INFO 1279 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2022-08-17 19:57:01.573  INFO 1279 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2022-08-17 19:57:01.574  INFO 1279 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.65]
2022-08-17 19:57:01.735  INFO 1279 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2022-08-17 19:57:01.736  INFO 1279 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 2476 ms
2022-08-17 19:57:02.947  INFO 1279 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2022-08-17 19:57:02.970  INFO 1279 --- [           main] o.s.b.d.f.s.MyApplication                : Started MyApplication in 4.437 seconds (JVM running for 5.207)
```

기본적으로 응용 프로그램을 시작한 사용자와 같은 일부 관련 시작 세부 정보를 포함하여 INFO 로깅 메시지가 표시됩니다. INFO 이외의 로그 수준이 필요한 경우 로그 수준에 설명된 대로 설정할 수 있습니다.
애플리케이션 버전은 기본 애플리케이션 클래스 패키지의 구현 버전을 사용하여 결정됩니다.
시작 정보 로깅은 spring.main.log-startup-info를 false로 설정하여 끌 수 있습니다. 이렇게 하면 애플리케이션의 활성 프로필 로깅도 해제됩니다.

> Tip
>
> 시작하는 동안 추가 로깅을 추가하려면 SpringApplication의 하위 클래스에서 logStartupInfo(boolean)를 재정의할 수 있습니다.

### 7.1.1. 시작 실패

애플리케이션 시작에 실패하면 등록된 `FailureAnalyzer`가 문제를 해결하기 위한 전용 오류 메시지와 구체적인 조치를 제공할 기회를 얻습니다.
예를 들어 포트 8080에서 웹 애플리케이션을 시작하고 해당 포트가 이미 사용 중인 경우 다음 메시지와 유사한 내용이 표시됩니다.

```text
***************************
APPLICATION FAILED TO START
***************************

Description:

Embedded servlet container failed to start. Port 8080 was already in use.

Action:

Identify and stop the process that is listening on port 8080 or configure this application to listen on another port.
```

> Note
>
> Spring Boot는 수많은 FailureAnalyzer 구현을 제공하며 직접 추가할 수 있습니다.

오류 분석기가 예외를 처리할 수 없는 경우에도 전체 조건 보고서를 표시하여 무엇이 잘못되었는지 더 잘 이해할 수 있습니다.
이렇게 하려면 디버그 속성을 활성화하거나 `org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener`에 대한
DEBUG 로깅을 활성화해야 합니다

예를 들어 java -jar를 사용하여 애플리케이션을 실행하는 경우 다음과 같이 디버그 속성을 활성화할 수 있습니다.

```shell
java -jar myproject-0.0.1-SNAPSHOT.jar --debug
```

### 7.1.2. 지연 초기화

SpringApplication을 사용하면 애플리케이션을 느리게 초기화할 수 있습니다. 지연 초기화가 활성화되면 응용 프로그램 시작 중에가 아니라 필요할 때 Bean이 생성됩니다.
결과적으로 지연 초기화를 활성화하면 애플리케이션을 시작하는 데 걸리는 시간을 줄일 수 있습니다. 웹 애플리케이션에서 지연 초기화를 활성화하면 HTTP 요청이 수신될 때까지 많은 웹 관련 Bean이 초기화되지
않습니다.

지연 초기화의 단점은 응용 프로그램의 문제 발견을 지연시킬 수 있다는 것입니다. 잘못 구성된 빈이 느리게 초기화되면 시작 중에 더 이상 오류가 발생하지 않으며 빈이 초기화될 때만 문제가 분명해집니다.
JVM이 시작 중에 초기화되는 빈뿐만 아니라 애플리케이션의 모든 빈을 수용할 수 있는 충분한 메모리를 갖도록 주의를 기울여야 합니다.
이러한 이유로 지연 초기화는 기본적으로 활성화되어 있지 않으며 지연 초기화를 활성화하기 전에 JVM의 힙 크기를 미세 조정하는 것이 좋습니다.

지연 초기화는 SpringApplicationBuilder의 lazyInitialization 메서드 또는 SpringApplication의 setLazyInitialization 메서드를 사용하여 프로그래밍
방식으로 활성화할 수 있습니다.
또는 다음 예제와 같이 `spring.main.lazy-initialization` 속성을 사용하여 활성화할 수 있습니다.

```properties
spring.main.lazy-initialization=true
```

```yaml
spring:
  main:
    lazy-initialization: true
```

> Tip
>
> 나머지 응용 프로그램에 대해 지연 초기화를 사용하는 동안 특정 빈에 대한 지연 초기화를 비활성화하려면 @Lazy(false) 주석을 사용하여 명시적으로 지연 속성을 false로 설정할 수 있습니다.

### 7.1.3. 배너 사용자 지정

시작할 때 인쇄되는 배너는 클래스 경로에 `banner.txt` 파일을 추가하거나 `spring.banner.location` 속성을 해당 파일의 위치로 설정하여 변경할 수 있습니다.
파일에 UTF-8 이외의 인코딩이 있는 경우 `spring.banner.charset`을 설정할 수 있습니다. 텍스트 파일 외에 `banner.gif`, `banner.jpg` 또는 `banner.png` 이미지
파일을 클래스 경로에 추가하거나 `spring.banner.image.location` 속성을 설정할 수도 있습니다.
이미지는 ASCII 아트 표현으로 변환되어 텍스트 배너 위에 인쇄됩니다.

`banner.txt` 파일 내에서 환경에서 사용 가능한 모든 키와 다음과 같은 표시자를 사용할 수 있습니다

테이블 4. 배너 변수

| 변수 명                                                                           | 설명                                                                                        |
|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| ${application.version}                                                         | MANIFEST.MF에 선언된 애플리케이션의 버전 번호입니다. 예를 들어 Implementation-Version: 1.0은 1.0으로 인쇄됩니다.        |
| ${application.formatted-version}                                               | MANIFEST.MF에 선언되고 표시 형식이 지정된 애플리케이션의 버전 번호(대괄호로 둘러싸이고 접두어 v가 붙음). 예를 들어(v1.0)             |
| ${spring-boot.version}                                                         | 사용 중인 Spring Boot 버전입니다. 예를 들어 2.6.11-SNAPSHOT.                                           |
| ${spring-boot.formatted-version}                                               | 사용 중인 Spring Boot 버전으로, 표시용으로 형식이 지정되어 있습니다(대괄호로 둘러싸이고 접두어는 v임). 예를 들어(v2.6.11-SNAPSHOT). |
| ${Ansi.NAME} (or ${AnsiColor.NAME}, ${AnsiBackground.NAME}, ${AnsiStyle.NAME}) | 여기서 NAME은 ANSI 이스케이프 코드의 이름입니다. 자세한 내용은 AnsiPropertySource를 참조하십시오.                       |
| ${application.title}                                                           | MANIFEST.MF에 선언된 애플리케이션의 제목입니다. 예를 들어 구현-제목: MyApp은 MyApp으로 인쇄됩니다.                        |

> Tip
>
> 프로그래밍 방식으로 배너를 생성하려는 경우 SpringApplication.setBanner(… ) 메서드를 사용할 수 있습니다. `org.springframework.boot.Banner` 인터페이스를 사용하고
> 고유한 printBanner() 메소드를 구현하십시오.

또한 `spring.main.banner-mode` 속성을 사용하여 배너를 `System.out(콘솔)`에 인쇄해야 하는지, 구성된 `로거(log)`로 보내야 하는지 또는 전혀 생성하지 않아야 하는지(off)를
결정할 수 있습니다

인쇄된 배너는 `springBootBanner`라는 이름으로 싱글톤 빈으로 등록됩니다.

> Note
>
> `${application.version}` 및 `${application.formatted-version}` 속성은 Spring Boot 시작 관리자를 사용하는 경우에만 사용할 수 있습니다.
> 압축을 푼 jar을 실행하고 `java -cp <classpath> <mainclass>`로 시작하는 경우 값이 확인되지 않습니다.
>
> 이것이 우리가 항상 `java org.springframework.boot.loader.JarLauncher`를 사용하여 압축을 푼 jar를 시작하도록 권장하는 이유입니다.
> 이렇게 하면 클래스 경로를 빌드하고 앱을 시작하기 전에 `application.*` 배너 변수가 초기화됩니다.

### 7.1.4. 스프링 어플리케이션 커스터마이징

SpringApplication 기본값이 마음에 들지 않으면 대신 로컬 인스턴스를 생성하고 사용자 정의할 수 있습니다.
예를 들어 배너를 끄려면 다음과 같이 작성할 수 있습니다.

```java

@SpringBootApplication
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(MyApplication.class);
        application.setBannerMode(Banner.Mode.OFF);
        application.run(args);
    }

}
```

> Note
>
> SpringApplication에 전달된 생성자 인수는 Spring Bean의 구성 소스입니다. 대부분의 경우 이들은 @Configuration 클래스에 대한 참조이지만 @Component 클래스에 대한 직접
> 참조일 수도 있습니다.

application.properties 파일을 사용하여 SpringApplication을 구성하는 것도 가능합니다. 자세한 내용은 외부화된 구성을 참조하세요.

구성 옵션의 전체 목록은 SpringApplication Javadoc을 참조하세요.

### 7.1.5. Fluent Builder API

`ApplicationContext` 계층(부모/자식 관계가 있는 여러 컨텍스트)을 빌드해야 하거나 "fluent" 빌더 API 사용을 선호하는 경우 `SpringApplicationBuilder`를 사용할 수
있습니다.

`SpringApplicationBuilder`를 사용하면 여러 메서드 호출을 함께 연결할 수 있으며 다음 예제와 같이 계층 구조를 만들 수 있는 부모 및 자식 메서드가 포함됩니다.

```java
new SpringApplicationBuilder()
        .sources(Parent.class)
        .child(Application.class)
        .bannerMode(Banner.Mode.OFF)
        .run(args);
```

> Note
>
> ApplicationContext 계층을 생성할 때 몇 가지 제한 사항이 있습니다.
> 예를 들어 웹 구성 요소는 하위 컨텍스트 내에 포함되어야 하며 동일한 환경이 상위 및 하위 컨텍스트 모두에 사용됩니다. 자세한 내용은 SpringApplicationBuilder Javadoc를 참조하세요.

### 7.1.6 어플리케이션 가용성

플랫폼에 배포된 경우 애플리케이션은 Kubernetes Probe와 같은 인프라를 사용하여 플랫폼에 가용성에 대한 정보를 제공할 수 있습니다.
Spring Boot에는 일반적으로 사용되는 "liveness" 및 "readiness" 가용성 상태에 대한 기본 지원이 포함되어 있습니다.
Spring Boot의 "actuator" 지원을 사용하는 경우 이러한 상태는 상태 엔드포인트 그룹으로 노출됩니다.

또한 ApplicationAvailability 인터페이스를 자신의 빈에 주입하여 가용성 상태를 얻을 수도 있습니다.

### Liveness 상태

애플리케이션의 "Liveness" 상태는 내부 상태가 애플리케이션이 올바르게 작동하도록 허용하는지 또는 현재 실패하는 경우 자체적으로 복구할 수 있는지 여부를 알려줍니다.
중단된 "Liveness" 상태는 응용 프로그램이 복구할 수 없는 상태에 있으며 인프라가 응용 프로그램을 다시 시작해야 함을 의미합니다.

> Note
>
> 일반적으로 "Liveness" 상태는 상태 확인과 같은 외부 확인을 기반으로 해서는 안 됩니다.
> 만약 그렇다면 외부 시스템(데이터베이스, 웹 API, 외부 캐시)의 실패로 인해 플랫폼 전체에서 대량 재시작 및 계단식 오류가 발생합니다.

Spring Boot 애플리케이션의 내부 상태는 대부분 Spring ApplicationContext로 표현됩니다. 애플리케이션 컨텍스트가 성공적으로 시작된 경우 Spring Boot는 애플리케이션이 유효한 상태에
있다고 가정합니다.
애플리케이션은 컨텍스트가 새로 고쳐지는 즉시 활성 상태로 간주됩니다. Spring Boot 애플리케이션 수명 주기 및 관련 애플리케이션 이벤트를 참조하세요.

### Readiness 상태

애플리케이션의 "Readiness" 상태는 애플리케이션이 트래픽을 처리할 준비가 되었는지 여부를 알려줍니다. 실패한 "Readiness" 상태는 플랫폼에 현재 애플리케이션으로 트래픽을 라우팅하지 않아야 함을
알려줍니다.
이것은 일반적으로 시작하는 동안, CommandLineRunner 및 ApplicationRunner 구성 요소가 처리되는 동안 또는 응용 프로그램이 추가 트래픽을 처리하기에는 너무 바쁘다고 판단하는 경우 언제든지
발생합니다.

애플리케이션 및 명령줄 실행기가 호출되는 즉시 애플리케이션이 준비된 것으로 간주됩니다. Spring Boot 애플리케이션 수명 주기 및 관련 애플리케이션 이벤트를 참조하세요.

> Tip
>
> 시작 중에 실행될 것으로 예상되는 작업은 @PostConstruct와 같은 Spring 구성 요소 수명 주기 콜백을 사용하는 대신 CommandLineRunner 및 ApplicationRunner 구성 요소에
> 의해 실행되어야 합니다.

### 애플리케이션 가용성 상태 관리

애플리케이션 구성요소는 ApplicationAvailability 인터페이스를 삽입하고 그에 대한 메서드를 호출하여 언제든지 현재 가용성 상태를 검색할 수 있습니다.
더 자주 애플리케이션은 상태 업데이트를 수신하거나 애플리케이션의 상태를 업데이트하려고 합니다.

예를 들어 Kubernetes "exec Probe"가 이 파일을 볼 수 있도록 애플리케이션의 "Readiness" 상태를 파일로 내보낼 수 있습니다.

```java

@Component
public class MyReadinessStateExporter {

    @EventListener
    public void onStateChange(AvailabilityChangeEvent<ReadinessState> event) {
        switch (event.getState()) {
            case ACCEPTING_TRAFFIC:
                // create file /tmp/healthy
                break;
            case REFUSING_TRAFFIC:
                // remove file /tmp/healthy
                break;
        }
    }

}
```

애플리케이션이 중단되어 복구할 수 없는 경우 애플리케이션의 상태를 업데이트할 수도 있습니다.

```java

@Component
public class MyLocalCacheVerifier {

    private final ApplicationEventPublisher eventPublisher;

    public MyLocalCacheVerifier(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }

    public void checkLocalCache() {
        try {
            // ...
        } catch (CacheCompletelyBrokenException ex) {
            AvailabilityChangeEvent.publish(this.eventPublisher, ex, LivenessState.BROKEN);
        }
    }

}
```

Spring Boot는 액추에이터 상태 엔드포인트와 함께 "Liveness" 및 "Readness"를 위한 Kubernetes HTTP 프로브를 제공합니다.
전용 섹션에서 Kubernetes에 Spring Boot 애플리케이션을 배포하는 방법에 대한 추가 지침을 얻을 수 있습니다.

### 7.1.7. 애플리케이션 이벤트 및 리스너

ContextRefreshedEvent와 같은 일반적인 Spring Framework 이벤트 외에도 SpringApplication은 몇 가지 추가 애플리케이션 이벤트를 보냅니다.

> Note
>
> 일부 이벤트는 ApplicationContext가 생성되기 전에 실제로 트리거되므로 @Bean으로 리스너를 등록할 수 없습니다.
> SpringApplication.addListeners(… ) 메서드 또는 SpringApplicationBuilder.listeners(… ) 메서드로 등록할 수 있습니다.
>
> 애플리케이션이 생성된 방식에 관계없이 해당 리스너가 자동으로 등록되도록 하려면 META-INF를 추가할 수 있습니다.
>
> `org.springframework.context.ApplicationListener=com.example.project.MyListener`

애플리케이션 이벤트는 애플리케이션이 실행될 때 다음 순서로 전송됩니다.

1. `ApplicationStartingEvent`는 실행 시작 시 전송되지만 리스너 및 이니셜라이저 등록을 제외한 모든 처리 전에 전송됩니다.
2. `ApplicationEnvironmentPreparedEvent`는 컨텍스트에서 사용할 환경이 알려져 있지만 컨텍스트가 생성되기 전에 전송됩니다.
3. `ApplicationContextInitializedEvent`는 ApplicationContext가 준비되고 ApplicationContextInitializers가 호출되었지만 빈 정의가 로드되기 전에
   전송됩니다.
4. `ApplicationPreparedEvent`는 새로 고침이 시작되기 직전에 bean 정의가 로드된 후에 전송됩니다.
5. `ApplicationStartedEvent`는 컨텍스트가 새로 고쳐진 후 애플리케이션 및 명령줄 실행기가 호출되기 전에 전송됩니다.
6. `LivenessState.CORRECT`와 함께 바로 다음에 `AvailabilityChangeEvent`가 전송되어 애플리케이션이 라이브로 간주되었음을 나타냅니다.
7. `ApplicationReadyEvent`는 응용 프로그램 및 명령줄 실행기가 호출된 후에 전송됩니다.
8. Application이 요청을 처리할 준비가 되었음을 나타내기 위해 `ReadinessState.ACCEPTING_TRAFFIC`과 함께 바로 뒤에 `AvailabilityChangeEvent`가 전송됩니다.
9. 시작 시 예외가 있는 경우 `ApplicationFailedEvent`가 전송됩니다

위의 목록에는 SpringApplication에 연결된 SpringApplicationEvents만 포함됩니다. 이 외에도 ApplicationPreparedEvent 이후 및
ApplicationStartedEvent 이전에 다음 이벤트도 게시됩니다

- WebServer가 준비되면 WebServerInitializedEvent가 전송됩니다. ServletWebServerInitializedEvent 및
  ReactiveWebServerInitializedEvent는 각각 서블릿 및 반응형 변형입니다.
- ApplicationContext가 새로 고쳐지면 ContextRefreshedEvent가 전송됩니다.

> Tip
>
> 응용 프로그램 이벤트를 사용할 필요가 없는 경우가 많지만 이벤트가 존재한다는 사실을 알고 있으면 편리합니다. 내부적으로 Spring Boot는 이벤트를 사용하여 다양한 작업을 처리합니다.

> Note
>
> 이벤트 리스너는 기본적으로 동일한 스레드에서 실행되므로 잠재적으로 시간이 오래 걸리는 작업을 실행하면 안 됩니다. 대신 응용 프로그램 및 명령줄 실행기를 사용하는 것이 좋습니다.

애플리케이션 이벤트는 Spring Framework의 이벤트 게시 메커니즘을 사용하여 전송됩니다. 이 메커니즘의 일부는 하위 컨텍스트의 리스너에 게시된 이벤트가 모든 상위 컨텍스트의 리스너에도 게시되도록 합니다.
그 결과 애플리케이션이 SpringApplication 인스턴스의 계층 구조를 사용하는 경우 리스너는 동일한 유형의 애플리케이션 이벤트의 여러 인스턴스를 수신할 수 있습니다.

리스너가 컨텍스트에 대한 이벤트와 하위 컨텍스트에 대한 이벤트를 구분할 수 있도록 하려면 애플리케이션 컨텍스트가 삽입되도록 요청한 다음 삽입된 컨텍스트를 이벤트 컨텍스트와 비교해야 합니다.
컨텍스트는 ApplicationContextAware를 구현하거나 수신기가 빈인 경우 @Autowired를 사용하여 주입할 수 있습니다.

### 7.1.8. 웹 환경

SpringApplication은 당신을 대신하여 올바른 유형의 ApplicationContext를 생성하려고 시도합니다. WebApplicationType을 결정하는 데 사용되는 알고리즘은 다음과 같습니다.

- Spring MVC가 있으면 `AnnotationConfigServletWebServerApplicationContext`가 사용됩니다.
- Spring MVC가 없고 Spring WebFlux가 있는 경우 `AnnotationConfigReactiveWebServerApplicationContext`가 사용됩니다.
- 그렇지 않으면 `AnnotationConfigApplicationContext`가 사용됩니다.

즉, 동일한 애플리케이션에서 Spring MVC와 Spring WebFlux의 새 WebClient를 사용하는 경우 Spring MVC가 기본적으로 사용됩니다.
`setWebApplicationType(WebApplicationType)`을 호출하여 쉽게 재정의할 수 있습니다.

`setApplicationContextClass(… )`를 호출하여 사용되는 `ApplicationContext` 유형을 완전히 제어할 수도 있습니다.

> Tip
>
> JUnit 테스트 내에서 SpringApplication을 사용할 때 `setWebApplicationType(WebApplicationType.NONE)`을 호출하는 것이 종종 바람직합니다.

### 7.1.9. 어플리케이션 인자값 접근

`SpringApplication.run(… )`에 전달된 애플리케이션 인수에 액세스해야 하는 경우 `org.springframework.boot.ApplicationArguments` 빈을 주입할 수 있습니다.
ApplicationArguments 인터페이스는 다음 예제와 같이 원시 String[] 인수와 구문 분석된 옵션 및 비옵션 인수 모두에 대한 액세스를 제공합니다.

```java

@Component
public class MyBean {

    public MyBean(ApplicationArguments args) {
        boolean debug = args.containsOption("debug");
        List<String> files = args.getNonOptionArgs();
        if (debug) {
            System.out.println(files);
        }
        // if run with "--debug logfile.txt" prints ["logfile.txt"]
    }

}
```

> Tip
>
> Spring Boot는 또한 CommandLinePropertySource를 Spring 환경에 등록합니다. 이렇게 하면 @Value 어노테이션을 사용하여 단일 애플리케이션 인자값를 주입할 수도 있습니다.

### 7.1.10. ApplicationRunner 또는 CommandLineRunner 사용

SpringApplication이 시작된 후 일부 특정 코드를 실행해야 하는 경우 ApplicationRunner 또는 CommandLineRunner 인터페이스를 구현할 수 있습니다.
두 인터페이스 모두 동일한 방식으로 작동하며 SpringApplication.run(… )이 완료되기 직전에 호출되는 단일 실행 메서드를 제공합니다.

> Note
>
> 이 계약은 애플리케이션 시작 후 트래픽 수락을 시작하기 전에 실행해야 하는 작업에 매우 적합합니다.

`CommandLineRunner` 인터페이스는 응용 프로그램 인수에 대한 액세스를 문자열 배열로 제공하는 반면 `ApplicationRunner`는 앞에서 설명한 ApplicationArguments 인터페이스를
사용합니다.
다음 예제는 run 메소드가 있는 CommandLineRunner를 보여줍니다.

```java

@Component
public class MyCommandLineRunner implements CommandLineRunner {

    @Override
    public void run(String... args) {
        // Do something...
    }

}
```

특정 순서로 호출해야 하는 여러 CommandLineRunner 또는 ApplicationRunner 빈이 정의된 경우 `org.springframework.core.Ordered` 인터페이스를 추가로
구현하거나 `org.springframework.core.annotation.Order` 어노테이션을 사용할 수 있습니다.

### 7.1.11. 어플리케이션 종료

각 SpringApplication은 종료 시 ApplicationContext가 정상적으로 닫히도록 JVM에 종료 후크를 등록합니다.
모든 표준 Spring 라이프사이클 콜백(예: `DisposableBean` 인터페이스 또는 `@PreDestroy` 어노테이션)을 사용할 수 있습니다.

또한 Bean은 `SpringApplication.exit()`가 호출될 때 특정 종료 코드를 반환하려는 경우 `org.springframework.boot.ExitCodeGenerator` 인터페이스를 구현할 수
있습니다.
그런 다음 이 종료 코드를 System.exit()에 전달하여 다음 예제와 같이 상태 코드로 반환할 수 있습니다.

```java

@SpringBootApplication
public class MyApplication {

    @Bean
    public ExitCodeGenerator exitCodeGenerator() {
        return () -> 42;
    }

    public static void main(String[] args) {
        System.exit(SpringApplication.exit(SpringApplication.run(MyApplication.class, args)));
    }

}
```

또한 `ExitCodeGenerator` 인터페이스는 예외에 의해 구현될 수 있습니다. 이러한 예외가 발생하면 Spring Boot는 구현된 getExitCode() 메서드에서 제공하는 종료 코드를 반환합니다.

### 7.1.12. 관리 기능

`spring.application.admin.enabled` 속성을 지정하여 애플리케이션의 관리 관련 기능을 활성화할 수 있습니다.
이는 플랫폼 MBeanServer에서 SpringApplicationAdminMXBean을 노출합니다. 이 기능을 사용하여 Spring Boot 애플리케이션을 원격으로 관리할 수 있습니다. 이 기능은 모든 서비스
래퍼 구현에도 유용할 수 있습니다.

> Tip
>
> 애플리케이션이 실행 중인 HTTP 포트를 알고 싶다면 local.server.port 키를 사용하여 속성을 가져옵니다.

### 7.1.13. 애플리케이션 시작 추적

애플리케이션 시작 중에 SpringApplication 및 ApplicationContext는 애플리케이션 수명 주기, Bean 수명 주기 또는 애플리케이션 이벤트 처리와 관련된 많은 작업을 수행합니다.
ApplicationStartup을 사용하면 Spring Framework를 통해 StartupStep 객체로 애플리케이션 시작 시퀀스를 추적할 수 있습니다. 이 데이터는 프로파일링 목적으로 수집하거나 애플리케이션
시작 프로세스를 더 잘 이해하기 위해 수집할 수 있습니다.

`SpringApplication` 인스턴스를 설정할 때 `ApplicationStartup` 구현을 선택할 수 있습니다. 예를 들어 `BufferingApplicationStartup`을 사용하려면 다음과 같이
작성할 수 있습니다.

```java

@SpringBootApplication
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(MyApplication.class);
        application.setApplicationStartup(new BufferingApplicationStartup(2048));
        application.run(args);
    }

}
```

첫 번째 사용 가능한 구현인 `FlightRecorderApplicationStartup`은 Spring Framework에서 제공됩니다.
Java Flight Recorder 세션에 Spring 관련 시작 이벤트를 추가하고 애플리케이션을 프로파일링하고 Spring 컨텍스트 수명 주기를 JVM 이벤트(예: 할당, GC, 클래스 로딩 등)와 연관시키기
위한 것입니다.
일단 구성되면 Flight Recorder를 활성화한 상태에서 애플리케이션을 실행하여 데이터를 기록할 수 있습니다.

```shell
java -XX:StartFlightRecording:filename=recording.jfr,duration=10s -jar demo.jar
```

Spring Boot는 BufferingApplicationStartup 변형과 함께 제공됩니다. 이 구현은 시작 단계를 버퍼링하고 이를 외부 메트릭 시스템으로 배출하기 위한 것입니다.
애플리케이션은 모든 구성 요소에서 BufferingApplicationStartup 유형의 빈을 요청할 수 있습니다.

이 정보를 JSON 문서로 제공하는 시작 엔드포인트를 노출하도록 Spring Boot를 구성할 수도 있습니다.

## 7.2. 외부화된 구성

Spring Boot를 사용하면 구성을 외부화하여 다른 환경에서 동일한 애플리케이션 코드로 작업할 수 있습니다.
Java 속성 파일, YAML 파일, 환경 변수 및 명령줄 인수를 포함하여 다양한 외부 구성 소스를 사용할 수 있습니다.

속성 값은 @Value 어노테이션을 사용하여 빈에 직접 주입하거나 Spring의 환경 추상화를 통해 액세스하거나 @ConfigurationProperties를 통해 구조화된 개체에 바인딩할 수 있습니다.

Spring Boot는 합리적인 값 재정의를 허용하도록 설계된 매우 특별한 PropertySource 순서를 사용합니다. 속성은 다음 순서로 고려됩니다(하위 항목의 값이 이전 항목보다 우선함).

1. 기본 속성(SpringApplication.setDefaultProperties를 설정하여 지정됨).
2. `@Configuration` 클래스의 `@PropertySource` 어노테이션. 이러한 속성 소스는 응용 프로그램 컨텍스트가 새로 고쳐질 때까지 환경에 추가되지 않습니다.새로 고침이 시작되기 전에
   읽히는 `logging.*` 및 `spring.main.*`과 같은 특정 속성을 구성하기에는 너무 늦었습니다.
3. 구성 데이터(예: `application.properties` 파일).
4. `random.*`에만 속성이 있는 `RandomValuePropertySource`
5. OS 환경 변수
6. Java 시스템 특성(System.getProperties())
7. `java:comp/env`의 JNDI 속성
8. `ServletContext` 초기 매개변수
9. `ServletConfig` 초기 매개변수
10. `SPRING_APPLICATION_JSON`의 속성(환경 변수 또는 시스템 속성에 내장된 인라인 JSON).
11. command line 인자 값
12. 테스트의 properties 속성. 애플리케이션의 특정 부분을 테스트하기 위해 `@SpringBootTest` 및 테스트 어노테이션에서 사용할 수 있습니다.
13. 테스트의 `@TestPropertySource` 어노테이션.
14. devtools가 활성화된 경우 `$HOME/.config/spring-boot` 디렉토리의 Devtools 전역 설정 속성

구성 데이터 파일은 다음 순서로 간주됩니다

1. jar 안에 패키지된 애플리케이션 속성(`application.properties` 및 YAML 변형).
2. jar 안에 패키지된 프로필별 애플리케이션 속성(`application-{profile}.properties` 및 YAML 변형).
3. 패키징된 jar 외부의 애플리케이션 속성(`application.properties` 및 YAML 변형).
4. 패키지된 jar 외부의 프로필별 애플리케이션 속성(`application-{profile}.properties` 및 YAML 변형).

> Note
>
> 전체 애플리케이션에 대해 하나의 형식을 고수하는 것이 좋습니다. 동일한 위치에 .properties 및 .yml 형식이 모두 포함된 구성 파일이 있는 경우 .properties가 우선합니다.

구체적인 예를 제공하기 위해 다음 예와 같이 이름 속성을 사용하는 @Component를 개발한다고 가정합니다.

```java

@Component
public class MyBean {

    @Value("${name}")
    private String name;

    // ...

}
```

애플리케이션 클래스 경로(예: jar 내부)에서 이름에 대해 합리적인 기본 속성 값을 제공하는 `application.properties` 파일을 가질 수 있습니다
새 환경에서 실행할 때 이름을 재정의하는 jar 외부에 application.properties 파일을 제공할 수 있습니다. 일회성 테스트의 경우 특정 명령줄 스위치(
예: `java -jar app.jar --name="Spring"`)를 사용하여 시작할 수 있습니다.

> Tip
>
> `env` 및 `configprops` 엔드포인트는 속성에 특정 값이 있는 이유를 확인하는 데 유용할 수 있습니다. 이 두 엔드포인트를 사용하여 예기치 않은 속성 값을 진단할 수 있습니다. 자세한 내용은 "
> 프로덕션 준비 기능" 섹션을 참조하십시오.

### 7.2.1. 커멘드라인 속성 접근법

기본적으로 SpringApplication은 모든 명령줄 옵션 인수(즉, --server.port=9000과 같이 --로 시작하는 인수)를 속성으로 변환하고 Spring 환경에 추가합니다. 앞에서 언급했듯이 명령줄
속성은 항상 파일 기반 속성 소스보다 우선합니다.

명령줄 속성을 환경에 추가하지 않으려면 `SpringApplication.setAddCommandLineProperties(false)`를 사용하여 비활성화할 수 있습니다.

### 7.2.2. JSON 애플리케이션 속성

환경 변수 및 시스템 속성에는 종종 일부 속성 이름을 사용할 수 없음을 의미하는 제한이 있습니다. 이를 돕기 위해 Spring Boot를 사용하면 속성 블록을 단일 JSON 구조로 인코딩할 수 있습니다.

애플리케이션이 시작되면 `spring.application.json` 또는 `SPRING_APPLICATION_JSON` 속성이 구문 분석되어 환경에 추가됩니다.

예를 들어 UN*X 셸의 명령줄에서 환경 변수로 SPRING_APPLICATION_JSON 속성을 제공할 수 있습니다.

```shell
SPRING_APPLICATION_JSON='{"my":{"name":"test"}}' java -jar myapp.jar
```

앞의 예에서 Spring 환경에서 `my.name=test`로 끝납니다.

동일한 JSON을 시스템 속성으로 제공할 수도 있습니다.

```shell
java -Dspring.application.json='{"my":{"name":"test"}}' -jar myapp.jar
```

또는 커멘드라인 인자를 사용하여 JSON을 제공할 수 있습니다.

```shell
java -jar myapp.jar --spring.application.json='{"my":{"name":"test"}}'
```

기존 애플리케이션 서버에 배포하는 경우 `java:comp/env/spring.application.json`이라는 JNDI 변수를 사용할 수도 있습니다.

> Note
>
> JSON의 null 값이 결과 속성 소스에 추가되더라도 `PropertySourcesPropertyResolver`는 null 속성을 누락된 값으로 처리합니다.
> 즉, JSON은 하위 속성 소스의 속성을 null 값으로 재정의할 수 없습니다.

### 7.2.3. 외부 애플리케이션 속성

Spring Boot는 애플리케이션이 시작될 때 다음 위치에서 `application.properties` 및 `application.yaml` 파일을 자동으로 찾아 로드합니다.

1. 클래스 경로에서
    1. 클래스패스 루트
    2. 클래스패스 /config 패키지
2. 현재 디렉터리에서
    1. 1.현재 디렉터리
    2. 현재 디렉터리 /config 하위 디렉터리
    3. /config 하위 디렉터리의 직계 하위 디렉터리

목록은 우선 순위에 따라 정렬됩니다(하위 항목의 값이 이전 항목보다 우선함). 로드된 파일의 문서는 Spring 환경에 `PropertySource`로 추가됩니다.

구성 파일 이름으로 `application`이 마음에 들지 않으면 `spring.config.name` 환경 속성을 지정하여 다른 파일 이름으로 전환할 수 있습니다.
예를 들어 `myproject.properties` 및 `myproject.yaml` 파일을 찾으려면 다음과 같이 애플리케이션을 실행할 수 있습니다.

```shell
java -jar myproject.jar --spring.config.name=myproject
```

`spring.config.location` 환경 속성을 사용하여 명시적인 위치를 참조할 수도 있습니다. 이 속성은 확인할 하나 이상의 위치가 쉼표로 구분된 목록을 허용합니다.

다음 예에서는 두 개의 개별 파일을 지정하는 방법을 보여줍니다.

```shell
java -jar myproject.jar --spring.config.location=\
    optional:classpath:/default.properties,\
    optional:classpath:/override.properties
```

> Tip
>
> 선택적 접두사를 사용하십시오: 위치가 선택사항이고 위치가 존재하지 않아도 괜찮다면.

> Waring
> `spring.config.name`, `spring.config.location` 및 `spring.config.additional-location`은 로드해야 하는 파일을 결정하기 위해 매우 초기에 사용됩니다
> 환경 속성(일반적으로 OS 환경 변수, 시스템 속성 또는 커멘드라인 인자)으로 정의해야 합니다.

`spring.config.location`에 디렉토리(파일이 아닌)가 포함되어 있으면 /로 끝나야 합니다.런타임 시 로드되기 전에 `spring.config.name`에서 생성된 이름이
추가됩니다. `spring.config.location`에 지정된 파일은 직접 가져옵니다.

> Note
>
> 프로필별 파일을 확인하기 위해 디렉터리 및 파일 위치 값도 확장됩니다.
> 예를 들어 `classpath:myconfig.properties`의 `spring.config.location`이 있는 경우 적절한 `classpath:myconfig-<profile>.properties`
> 파일도 로드됩니다.

대부분의 경우 추가하는 각 `spring.config.location` 항목은 단일 파일 또는 디렉토리를 참조합니다. 위치는 정의된 순서대로 처리되며 이후 위치는 이전 위치의 값을 재정의할 수 있습니다.

복잡한 위치 설정이 있고 프로필별 구성 파일을 사용하는 경우 Spring Boot가 그룹화 방법을 알 수 있도록 추가 힌트를 제공해야 할 수 있습니다
위치 그룹은 모두 동일한 수준으로 간주되는 위치 모음입니다.
예를 들어 모든 클래스 경로 위치를 그룹화한 다음 모든 외부 위치를 그룹화할 수 있습니다. 위치 그룹 내의 항목은 `;`로 구분해야 합니다. 자세한 내용은 "프로필 특정 파일" 섹션의 예를 참조하십시오.

spring.config.location을 사용하여 구성된 위치는 기본 위치를 대체합니다.
예를 들어, spring.config.location이 optional:classpath:/custom-config/,optional:file:./custom-config/ 값으로 구성된 경우 고려되는 전체 위치
세트는 다음과 같습니다.

1. optional:classpath:custom-config/
2. optional:file:./custom-config/

추가 위치를 교체하는 것보다 추가하려는 경우 `spring.config.additional-location`을 사용할 수 있습니다.
추가 위치에서 로드된 속성은 기본 위치의 속성을 재정의할 수 있습니다.
예를 들어, spring.config.additional-location이 optional:classpath:/custom-config/,optional:file:./custom-config/ 값으로 구성된 경우
고려되는 전체 위치 세트는 다음과 같습니다.

1. optional:classpath:/;optional:classpath:/config/
2. optional:file:./;optional:file:./config/;optional:file:./config/*/
3. optional:classpath:custom-config/
4. optional:file:./custom-config/

이 검색 순서를 사용하면 하나의 구성 파일에서 기본값을 지정한 다음 해당 값을 다른 파일에서 선택적으로 재정의할 수 있습니다. 기본 위치 중 하나의 application.properties(또는
spring.config.name으로 선택한 다른 기본 이름)에서 애플리케이션의 기본값을 제공할 수 있습니다.
이러한 기본값은 사용자 지정 위치 중 하나에 있는 다른 파일을 사용하여 런타임에 재정의할 수 있습니다.

> Note
>
> 시스템 속성이 아닌 환경 변수를 사용하는 경우 대부분의 운영 체제는 마침표로 구분된 키 이름을 허용하지 않지만 대신 밑줄을 사용할 수 있습니다(예: spring.config.name 대신
> SPRING_CONFIG_NAME). 자세한 내용은 환경 변수에서 바인딩을 참조하십시오.

> Note
>
> 애플리케이션이 서블릿 컨테이너 또는 애플리케이션 서버에서 실행되는 경우 JNDI 속성(java:comp/env에 있음) 또는 서블릿 컨텍스트 초기화 매개변수를 환경 변수 또는 시스템 속성 대신 사용할 수 있습니다

### 선택적 위치

기본적으로 지정된 config 데이터 위치가 존재하지 않으면 Spring Boot는 `ConfigDataLocationNotFoundException`을 발생시키고 애플리케이션이 시작되지 않습니다.

위치를 지정하고 싶지만 위치가 항상 존재하지 않아도 괜찮다면 선택 사항인 접두사를 사용할 수 있습니다.
`spring.config.location` 및 `spring.config.additional-location` 속성과 함께 이 접두사를 사용할 수 있으며 `spring.config.import` 선언과 함께 사용할
수 있습니다

예를 들어 `optional:file:./myconfig.properties`의 `spring.config.import` 값을 사용하면 `myconfig.properties` 파일이 없어도 애플리케이션을 시작할 수
있습니다.

모든 `ConfigDataLocationNotFoundExceptions`를 무시하고 항상 애플리케이션을 계속 시작하려면 `spring.config.on-not-found` 속성을 사용할 수 있습니다.
`SpringApplication.setDefaultProperties(… )` 또는 시스템/환경 변수를 사용하여 무시하도록 값을 설정합니다.

### 와일드카드 위치

구성 파일 위치에 마지막 경로 세그먼트에 대한 * 문자가 포함되어 있으면 와일드카드 위치로 간주됩니다.
구성이 로드될 때 와일드카드가 확장되어 바로 하위 디렉토리도 확인됩니다.
와일드카드 위치는 구성 속성의 소스가 여러 개인 경우 Kubernetes와 같은 환경에서 특히 유용합니다.

예를 들어 일부 Redis 구성과 일부 MySQL 구성이 있는 경우 두 가지 구성을 별도로 유지하면서 둘 다 application.properties 파일에 있어야 할 수 있습니다.
이로 인해 `/config/redis/application.properties` 및 `/config/mysql/application.properties`와 같은 서로 다른 위치에 두 개의 개별
application.properties 파일이 마운트될 수 있습니다.
이러한 경우 와일드카드 위치가 `config/*/`이면 두 파일이 모두 처리됩니다.

기본적으로 Spring Boot는 기본 검색 위치에 config/*/를 포함합니다. 이는 jar 외부의 /config 디렉토리의 모든 하위 디렉토리가 검색됨을 의미합니다.

`spring.config.location` 및 `spring.config.additional-location` 속성과 함께 와일드카드 위치를 직접 사용할 수 있습니다.

> Note
>
> 와일드카드 위치는 하나의 *만 포함하고 디렉토리인 검색 위치의 경우 */로 끝나거나 파일인 검색 위치의 경우 */<파일 이름>으로 끝나야 합니다
> 와일드카드가 있는 위치는 파일 이름의 절대 경로를 기준으로 알파벳순으로 정렬됩니다.

> Tip
>
> 와일드카드 위치는 외부 디렉터리에서만 작동합니다. classpath: 위치에는 와일드카드를 사용할 수 없습니다.

### 프로필 특정 파일

애플리케이션 속성 파일과 마찬가지로 Spring Boot는 명명 규칙 `application-{profile}`을 사용하여 프로필별 파일을 로드하려고 시도합니다.
예를 들어 애플리케이션이 prod라는 프로필을 활성화하고 YAML 파일을 사용하는 경우 application.yml과 application-prod.yml이 모두 고려됩니다.

프로필별 속성은 표준 application.properties와 동일한 위치에서 로드되며, 프로필별 파일은 항상 비특정 파일을 재정의합니다. 여러 프로필이 지정된 경우 최종 성공 전략이 적용됩니다.
여러 프로필이 지정된 경우 최종 성공 전략이 적용됩니다. 예를 들어 프로필 prod,live가 spring.profiles.active 속성으로 지정된 경우 application-prod.properties의 값을
application-live.properties의 값으로 재정의할 수 있습니다.

> Note
>
> 최종 성공 전략은 위치 그룹 수준에서 적용됩니다. classpath:/cfg/,classpath:/ext/의 spring.config.location은 classpath:/cfg/;classpath:/ext/와
> 동일한 재정의 규칙을 갖지 않습니다.
>
> 예를 들어 위의 prod,live 예제를 계속하면 다음 파일이 있을 수 있습니다.
>
>
> ```text
> /cfg
>   application-live.properties
> /ext
>   application-live.properties
>   application-prod.properties
> ```
> classpath:/cfg/,classpath:/ext/의 spring.config.location이 있는 경우 모든 /ext 파일보다 먼저 모든 /cfg 파일을 처리합니다.
> 1. /cfg/application-live.properties
> 2. /ext/application-prod.properties
> 3. /ext/application-live.properties
>
> classpath:/cfg/;classpath:/ext/ 대신 ( ; 구분 기호 사용) /cfg 및
> 1. /ext/application-prod.properties
> 2. /cfg/application-live.properties
> 3. /ext/application-live.properties

환경에는 활성 프로필이 설정되지 않은 경우 사용되는 기본 프로필 집합(기본적으로 [default])이 있습니다. 즉, 프로필이 명시적으로 활성화되지 않은 경우 application-default의 속성이 고려됩니다.

> Note
>
> 속성 파일은 한 번만 로드됩니다. 프로필 특정 속성 파일을 이미 직접 가져온 경우 두 번째로 가져오지 않습니다.

### 추가 데이터 가져오기

애플리케이션 속성은 `spring.config.import` 속성을 사용하여 다른 위치에서 추가 구성 데이터를 가져올 수 있습니다.
imports은 발견되는 대로 처리되며 imports을 추가한 문서 바로 아래에 삽입된 추가 문서로 처리됩니다.

예를 들어 클래스 경로 application.properties 파일에 다음이 있을 수 있습니다.

```properties
spring.application.name=myapp
spring.config.import=optional:file:./dev.properties
```

```yaml
spring:
  application:
    name: "myapp"
  config:
    import: "optional:file:./dev.properties"
```

이렇게 하면 현재 디렉터리에 있는 dev.properties 파일 가져오기가 트리거됩니다(해당 파일이 있는 경우).
가져온 `dev.properties`의 값이 가져오기를 트리거한 파일보다 우선합니다. 위의 예에서 dev.properties는 `spring.application.name`을 다른 값으로 재정의할 수 있습니다.

가져오기는 선언 횟수에 관계없이 한 번만 가져옵니다. 속성/yaml 파일 내의 단일 문서 내에서 가져오기가 정의되는 순서는 중요하지 않습니다. 예를 들어, 아래의 두 예는 동일한 결과를 생성합니다.

```yaml
spring:
  config:
    import: "my.properties"
my:
  property: "value"
```

```yaml
my:
  property: "value"
spring:
  config:
    import: "my.properties"
```

위의 두 예에서 my.properties 파일의 값은 가져오기를 트리거한 파일보다 우선합니다.

단일 `spring.config.import` 키 아래에 여러 위치를 지정할 수 있습니다. 위치는 나중에 가져오기가 우선적으로 정의된 순서대로 처리됩니다.

> Note
>
> 적절한 경우 프로필별 변형도 가져오기에 대해 고려됩니다. 위의 예는 `my.properties`와 `my-<profile>.properties` 변형을 모두 가져옵니다.

> Tip
>
> Spring Boot에는 다양한 위치 주소를 지원할 수 있는 플러그형 API가 포함되어 있습니다. 기본적으로 Java 속성, YAML 및 "구성 트리"를 가져올 수 있습니다.
>
> 타사 jar는 추가 기술에 대한 지원을 제공할 수 있습니다(파일이 로컬이어야 한다는 요구 사항이 없음). 예를 들어 Consul, Apache ZooKeeper 또는 Netflix Archaius와 같은 외부
> 저장소의 구성 데이터를 상상할 수 있습니다.
>
> 자신의 locations 를 지원하려면 org.springframework.boot.context.config 패키지의 ConfigDataLocationResolver 및 ConfigDataLoader 클래스를
> 참조하세요.

### 확장자 없는 파일 가져오기

일부 클라우드 플랫폼은 볼륨 마운트 파일에 파일 확장자를 추가할 수 없습니다. 이러한 확장자 없는 파일을 가져오려면 스프링 부트에 로드 방법을 알 수 있도록 힌트를 제공해야 합니다. 대괄호 안에 확장 힌트를 넣으면
됩니다.

예를 들어 yaml로 가져오려는 `/etc/config/myconfig` 파일이 있다고 가정합니다. 다음을 사용하여 `application.properties`에서 가져올 수 있습니다.

```yaml
spring:
  config:
    import: "file:/etc/config/myconfig[.yaml]"
```

### 구성 트리 사용

클라우드 플랫폼(예: Kubernetes)에서 애플리케이션을 실행할 때 종종 플랫폼이 제공하는 구성 값을 읽어야 합니다.
이러한 목적으로 환경 변수를 사용하는 것은 드문 일이 아니지만 특히 값을 비밀로 유지해야 하는 경우 단점이 있을 수 있습니다

환경 변수에 대한 대안으로 이제 많은 클라우드 플랫폼에서 구성을 마운트된 데이터 볼륨에 매핑할 수 있습니다.
예를 들어 Kubernetes는 ConfigMap과 Secret을 모두 볼륨 마운트할 수 있습니다.

사용할 수 있는 두 가지 일반적인 볼륨 탑재 패턴이 있습니다.

1. 단일 파일에는 전체 속성 집합이 포함되어 있습니다(일반적으로 YAML로 작성됨).
2. 여러 파일이 디렉토리 트리에 기록되며 파일 이름이 '키'가 되고 내용이 '값'이 됩니다.

첫 번째 경우 위에서 설명한 대로 spring.config.import를 사용하여 YAML 또는 속성 파일을 직접 가져올 수 있습니다
두 번째 경우에는 Spring Boot가 모든 파일을 속성으로 노출해야 한다는 것을 알 수 있도록 configtree: 접두사를 사용해야 합니다.

예를 들어 Kubernetes가 다음 볼륨을 마운트했다고 가정해 보겠습니다.

```text
etc/
  config/
    myapp/
      username
      password
```

사용자 이름 파일의 내용은 구성 값이고 비밀번호의 내용은 비밀입니다.

이러한 속성을 가져오려면 `application.properties` 또는 `application.yaml` 파일에 다음을 추가할 수 있습니다.

```yaml
spring:
  config:
    import: "optional:configtree:/etc/config/"
```

그런 다음 일반적인 방법으로 환경에서 `myapp.username` 및 `myapp.password` 속성에 액세스하거나 주입할 수 있습니다.

> Tip
>
> 구성 트리 아래의 폴더는 속성 이름을 형성합니다. 위의 예에서 사용자 이름과 비밀번호로 속성에 액세스하려면 `spring.config.import`
> 를 `optional:configtree:/etc/config/myapp`로 설정할 수 있습니다.

> Note
>
> 점 표기법이 있는 파일 이름도 올바르게 매핑됩니다. 예를 들어 위의 예에서 /etc/config에 있는 myapp.username이라는 파일은 환경에서 myapp.username 속성이 됩니다.

> Tip
>
> 구성 트리 값은 예상되는 내용에 따라 string `String` 및 `byte[]` 유형 모두에 바인딩될 수 있습니다.

동일한 상위 폴더에서 가져올 구성 트리가 여러 개인 경우 와일드카드 바로 가기를 사용할 수 있습니다. 모든 configtree: /*/로 끝나는 위치는 모든 직계 자식을 구성 트리로 가져옵니다.

예를 들어 다음과 같은 볼륨이 있다고 가정합니다.

```text
etc/
  config/
    dbconfig/
      db/
        username
        password
    mqconfig/
      mq/
        username
        password
```

configtree:/etc/config/*/를 가져오기 위치로 사용할 수 있습니다.

```yaml
spring:
  config:
    import: "optional:configtree:/etc/config/*/"
```

이렇게 하면 `db.username`, `db.password`, `mq.username` 및 `mq.password` 속성이 추가됩니다.

> Note
>
> 와일드카드를 사용하여 로드된 디렉터리는 알파벳순으로 정렬됩니다. 다른 주문이 필요한 경우 각 위치를 별도의 가져오기로 나열해야 합니다.

구성 트리는 `Docker secret`에도 사용할 수 있습니다. `Docker swarm` 서비스에 비밀에 대한 액세스 권한이 부여되면 비밀이 컨테이너에 탑재됩니다
예를 들어 `db.password`라는 비밀이 `/run/secrets/` 위치에 마운트된 경우 다음을 사용하여 Spring 환경에서 `db.password`를 사용할 수 있도록 만들 수 있습니다.

```yaml
spring:
  config:
    import: "optional:configtree:/run/secrets/"
```

### 속성 자리 표시자

`application.properties` 및 `application.yml`의 값은 사용될 때 기존 환경을 통해 필터링되므로 이전에 정의된 값(예: 시스템 속성 또는 환경 변수)을 다시 참조할 수 있습니다.
표준 `${name}` 속성 자리 표시자 구문은 값 내 어디에서나 사용할 수 있습니다. 속성 자리 표시자는 `:`를 사용하여 기본값을 지정하여 속성 이름에서 기본값을 구분할 수도 있습니다(
예: `${name:default}`).

기본값이 있거나 없는 자리 표시자의 사용은 다음 예에 나와 있습니다.

```yaml
app:
  name: "MyApp"
  description: "${app.name} is a Spring Boot application written by ${username:Unknown}"
```

사용자 이름 속성이 다른 곳에서 설정되지 않았다고 가정하면 app.description은 `MyApp is a Spring Boot application written by Unknown`이라는 값을 갖게 됩니다.

> Note
>
> 항상 정규 형식(소문자만 사용하는 케밥-케이스)을 사용하여 자리 표시자에서 속성 이름을 참조해야 합니다. 이렇게 하면 @ConfigurationProperties를 완화 바인딩할 때와 동일한 논리를 Spring
> Boot에서 사용할 수 있습니다.
>
> 예를 들어 ${demo.item-price}는 application.properties 파일에서 demo.item-price 및 demo.itemPrice 양식을 선택하고 시스템 환경에서
> DEMO_ITEMPRICE를 선택합니다.
> ${demo.itemPrice}를 대신 사용한 경우 demo.item-price 및 DEMO_ITEMPRICE는 고려되지 않습니다.

> Tip
>
> 이 기술을 사용하여 기존 Spring Boot 속성의 "짧은" 변형을 만들 수도 있습니다. 자세한 내용은 Use 'Short' Command Line Arguments 하우투를 참조하세요.

### 다중 문서 파일 작업

Spring Boot를 사용하면 단일 물리적 파일을 각각 독립적으로 추가되는 여러 논리적 문서로 분할할 수 있습니다. 문서는 위에서 아래로 순서대로 처리됩니다. 이후 문서는 이전 문서에서 정의된 속성을 재정의할 수
있습니다.

application.yml 파일의 경우 표준 YAML 다중 문서 구문이 사용됩니다. 세 개의 연속 하이픈은 한 문서의 끝과 다음 문서의 시작을 나타냅니다.

예를 들어 다음 파일에는 두 개의 논리 문서가 있습니다.

```yaml
spring:
  application:
    name: "MyApp"
---
spring:
  application:
    name: "MyCloudApp"
  config:
    activate:
      on-cloud-platform: "kubernetes"
```

`application.properties` 파일의 경우 특수 `#---` 주석이 문서 분할을 표시하는 데 사용됩니다.

```properties
spring.application.name=MyApp
#---
spring.application.name=MyCloudApp
spring.config.activate.on-cloud-platform=kubernetes
```

> Note
>
> 속성 파일 구분 기호에는 선행 공백이 없어야 하며 정확히 3개의 하이픈 문자가 있어야 합니다. 구분 기호 바로 앞과 뒤의 줄은 주석이 아니어야 합니다.

> Tip
>
> 다중 문서 속성 파일은 종종 `spring.config.activate.on-profile`과 같은 활성화 속성과 함께 사용됩니다. 자세한 내용은 다음 섹션을 참조하십시오.

> Waring
>
> 다중 문서 속성 파일은 @PropertySource 또는 @TestPropertySource 어노테이션을 사용하여 로드할 수 없습니다.

### 활성화 속성

특정 조건이 충족될 때만 지정된 속성 집합을 활성화하는 것이 유용한 경우가 있습니다. 예를 들어 특정 프로필이 활성화된 경우에만 관련된 속성이 있을 수 있습니다.

`spring.config.activate.*`를 사용하여 속성 문서를 조건부로 활성화할 수 있습니다.

다음 활성화 속성을 사용할 수 있습니다.

테이블 5. 활성화 속성

| 속성                | Note                                    |
|-------------------|-----------------------------------------|
| on-profile        | 문서가 활성화되기 위해 일치해야 하는 프로필 표현식            |
| on-cloud-platform | 문서가 활성화되기 위해 감지되어야 하는 CloudPlatform입니다. |

예를 들어 다음은 두 번째 문서가 Kubernetes에서 실행될 때만 활성화되고 "prod" 또는 "staging" 프로필이 활성화될 때만 활성화되도록 지정합니다.

```yaml
myprop:
  "always-set"
---
spring:
  config:
    activate:
      on-cloud-platform: "kubernetes"
      on-profile: "prod | staging"
myotherprop: "sometimes-set"
```

### 7.2.4. 속성 암호화

Spring Boot는 속성 값 암호화에 대한 내장 지원을 제공하지 않지만 Spring 환경에 포함된 값을 수정하는 데 필요한 후크 포인트를 제공합니다.
EnvironmentPostProcessor 인터페이스를 사용하면 애플리케이션이 시작되기 전에 환경을 조작할 수 있습니다. 자세한 내용은 시작하기 전에 환경 또는 ApplicationContext 사용자 지정을
참조하십시오.

자격 증명과 암호를 안전하게 저장하는 방법이 필요한 경우 Spring Cloud Vault 프로젝트는 HashiCorp Vault에 외부화된 구성을 저장하기 위한 지원을 제공합니다.

### 7.2.5. YAML 작업

YAML은 JSON의 상위 집합이므로 계층적 구성 데이터를 지정하기 위한 편리한 형식입니다.
SpringApplication 클래스는 클래스 경로에 SnakeYAML 라이브러리가 있을 때마다 속성의 대안으로 YAML을 자동으로 지원합니다.

> Note
>
> "Starters"를 사용하면 spring-boot-starter에서 자동으로 SnakeYAML을 제공합니다.

### 속성에 YAML 매핑

YAML 문서는 계층적 형식에서 Spring 환경과 함께 사용할 수 있는 플랫 구조로 변환해야 합니다. 예를 들어 다음 YAML 문서를 고려하십시오

```yaml
environments:
  dev:
    url: "https://dev.example.com"
    name: "Developer Setup"
  prod:
    url: "https://another.example.com"
    name: "My Cool App"
```

환경에서 이러한 속성에 액세스하려면 다음과 같이 평면화됩니다.

```properties
environments.dev.url=https://dev.example.com
environments.dev.name=Developer Setup
environments.prod.url=https://another.example.com
environments.prod.name=My Cool App
```

마찬가지로 YAML 목록도 평면화해야 합니다. [인덱스] 역참조자가 있는 properties 키로 표시됩니다. 예를 들어 다음 YAML을 고려하십시오.

```yaml
my:
  servers:
    - "dev.example.com"
    - "another.example.com"
```

앞의 예제는 다음 properties으로 변환됩니다.

```properties
my.servers[0]=dev.example.com
my.servers[1]=another.example.com
```

> Tip
>
> [index] 표기법을 사용하는 속성은 Spring Boot의 Binder 클래스를 사용하여 Java List 또는 Set 개체에 바인딩할 수 있습니다. 자세한 내용은 아래의 "유형 안전 구성 속성" 섹션을
> 참조하십시오.

> Waring
>
> YAML 파일은 @PropertySource 또는 @TestPropertySource 어노테이션을 사용하여 로드할 수 없습니다. 따라서 이러한 방식으로 값을 로드해야 하는 경우 속성 파일을 사용해야 합니다.

### YAML 직접 로드

Spring Framework는 YAML 문서를 로드하는 데 사용할 수 있는 두 가지 편리한 클래스를 제공합니다. YamlPropertiesFactoryBean은 YAML을 속성으로 로드하고
YamlMapFactoryBean은 YAML을 맵으로 로드합니다.

YAML을 Spring PropertySource로 로드하려는 경우 YamlPropertySourceLoader 클래스를 사용할 수도 있습니다.

### 7.2.6 임의 값 구성

`RandomValuePropertySource`는 임의의 값을 주입(예: 비밀 또는 테스트 사례에)하는 데 유용합니다. 다음 예제와 같이 정수, long, uuids 또는 문자열을 생성할 수 있습니다.

```yaml
my:
  secret: "${random.value}"
  number: "${random.int}"
  bignumber: "${random.long}"
  uuid: "${random.uuid}"
  number-less-than-ten: "${random.int(10)}"
  number-in-range: "${random.int[1024,65536]}"
```

`random.int*` 구문은 `OPEN value (,max) CLOSE`입니다. 여기서 `OPEN,CLOSE`는 임의의 문자이고 `value,max`는 정수입니다. max가 제공되면 value는 최소값이고
max는 최대값(제외)입니다.

### 7.2.7. 시스템 환경 속성 구성

Spring Boot는 환경 속성에 대한 접두사 설정을 지원합니다. 이는 구성 요구 사항이 다른 여러 Spring Boot 애플리케이션에서 시스템 환경을 공유하는 경우에 유용합니다.
시스템 환경 속성의 접두사는 SpringApplication에서 직접 설정할 수 있습니다.

예를 들어 접두사를 input으로 설정하면 remote.timeout과 같은 속성도 시스템 환경에서 input.remote.timeout으로 해석됩니다.

### 7.2.8. 형식이 안전한 구성 속성

@Value("${property}") 어노테이션을 사용하여 구성 속성을 삽입하는 것은 때때로 번거로울 수 있습니다. 특히 여러 속성으로 작업하거나 데이터가 기본적으로 계층적일 경우 더욱 그렇습니다.
Spring Boot는 강력한 형식의 빈이 애플리케이션의 구성을 관리하고 유효성을 검사할 수 있도록 하는 속성 작업의 대체 방법을 제공합니다.

> Tip
>
> @Value와 유형 안전 구성 속성의 차이점도 참조하십시오.

### JavaBean 속성 바인딩

다음 예제와 같이 표준 JavaBean 속성을 선언하는 빈을 바인딩할 수 있습니다.

```java

@ConfigurationProperties("my.service")
public class MyProperties {

    private boolean enabled;

    private InetAddress remoteAddress;

    private final Security security = new Security();

    // getters / setters...

    public static class Security {

        private String username;

        private String password;

        private List<String> roles = new ArrayList<>(Collections.singleton("USER"));

        // getters / setters...

    }

}
```

앞의 POJO는 다음 속성을 정의합니다.

- my.service.enabled, 기본값은 false입니다.
- my.service.remote-address, 문자열에서 강제할 수 있는 유형 포함.
- my.service.security.username, 속성 이름으로 이름이 결정되는 중첩된 "보안" 개체 포함. 특히 유형이 전혀 사용되지 않으며 SecurityProperties일 수 있습니다.
- my.service.security.password.
- my.service.security.roles(기본값이 USER인 String 컬렉션 포함)

> Note
>
> 속성 파일, YAML 파일, 환경 변수 및 기타 메커니즘을 통해 구성되는 Spring Boot에서 사용 가능한 @ConfigurationProperties 클래스에 매핑되는 속성은 공용 API이지만 클래스 자체의
> 접근자(getter/setter)는 직접 사용.

> Note
>
> 이러한 배열은 기본 빈 생성자에 의존하며 getter 및 setter는 일반적으로 Spring MVC에서와 같이 표준 Java Beans property descriptors를 통해 바인딩되기 때문에 필수입니다.
> 다음과 같은 경우 setter를 생략할 수 있습니다.
> - Map들은 초기화되는 한 getter가 필요하지만 바인더에 의해 변경될 수 있으므로 setter가 반드시 필요한 것은 아닙니다.
> - 컬렉션 및 배열은 인덱스(일반적으로 YAML 사용)를 통해 또는 쉼표로 구분된 단일 값(속성)을 사용하여 액세스할 수 있습니다. 후자의 경우 세터는 필수입니다. 이러한 유형에 대해서는 항상 setter를
    추가하는 것이 좋습니다. 컬렉션을 초기화하는 경우 이전 예제에서와 같이 변경할 수 없는지 확인하십시오.
> - 중첩된 POJO 속성이 초기화되면(이전 예제의 Security 필드와 같이) setter가 필요하지 않습니다. 바인더가 기본 생성자를 사용하여 즉석에서 인스턴스를 생성하도록 하려면 setter가 필요합니다.
>
> 어떤 사람들은 Project Lombok을 사용하여 게터와 세터를 자동으로 추가합니다. 객체를 인스턴스화하기 위해 컨테이너에 의해 자동으로 사용되기 때문에 Lombok이 이러한 유형에 대한 특정 생성자를 생성하지
> 않는지 확인하십시오.
>
> 마지막으로 표준 Java Bean 속성만 고려되며 정적 속성에 대한 바인딩은 지원되지 않습니다.

### 생성자 바인딩

이전 섹션의 예제는 다음 예제와 같이 변경할 수 없는 방식으로 다시 작성할 수 있습니다.

```java

@ConstructorBinding
@ConfigurationProperties("my.service")
public class MyProperties {

    // fields...

    public MyProperties(boolean enabled, InetAddress remoteAddress, Security security) {
        this.enabled = enabled;
        this.remoteAddress = remoteAddress;
        this.security = security;
    }

    // getters...

    public static class Security {

        // fields...

        public Security(String username, String password, @DefaultValue("USER") List<String> roles) {
            this.username = username;
            this.password = password;
            this.roles = roles;
        }

        // getters...

    }

}
```

이 설정에서 @ConstructorBinding 주석은 생성자 바인딩을 사용해야 함을 나타내는 데 사용됩니다.
즉, 바인더는 바인딩하려는 매개 변수가 있는 생성자를 찾을 것으로 예상합니다.
Java 16 이상을 사용하는 경우 생성자 바인딩을 레코드와 함께 사용할 수 있습니다. 이 경우 레코드에 여러 생성자가 없으면 @ConstructorBinding을 사용할 필요가 없습니다.

@ConstructorBinding 클래스의 중첩 멤버(예: 위 예제의 Security)도 해당 생성자를 통해 바인딩됩니다.

기본값은 생성자 매개변수에서 @DefaultValue를 사용하거나 Java 16 이상을 사용하는 경우 레코드 구성요소를 사용하여 지정할 수 있습니다. 문자열 값을 누락된 속성의 대상 유형으로 강제 변환하기 위해 변환
서비스가 적용됩니다.

이전 예제를 참조하면 보안에 바인딩된 속성이 없으면 MyProperties 인스턴스에는 보안을 위한 null 값이 포함됩니다.
바인딩된 속성이 없는 경우에도 Security의 null이 아닌 인스턴스를 반환하려면 빈 @DefaultValue 주석을 사용하면 됩니다.

```java
public MyProperties(boolean enabled,InetAddress remoteAddress,@DefaultValue Security security){
        this.enabled=enabled;
        this.remoteAddress=remoteAddress;
        this.security=security;
        }
```

> Note
>
> 생성자 바인딩을 사용하려면 @EnableConfigurationProperties 또는 구성 속성 스캔을 사용하여 클래스를 활성화해야 합니다.
> 일반 Spring 메커니즘으로 생성된 빈(예: @Component 빈, @Bean 메서드를 사용하여 생성된 빈 또는 @Import를 사용하여 로드된 빈)에는 생성자 바인딩을 사용할 수 없습니다.

> Tip
>
> 클래스에 대한 생성자가 둘 이상인 경우 바인딩해야 하는 생성자에서 @ConstructorBinding을 직접 사용할 수도 있습니다.

> Note
>
> @ConfigurationProperties와 함께 java.util.Optional을 사용하는 것은 주로 반환 유형으로 사용하기 위한 것이므로 권장되지 않습니다. 따라서 구성 속성 주입에 적합하지 않습니다.
> 다른 유형의 속성과의 일관성을 위해 선택적 속성을 선언하고 값이 없는 경우 빈 선택적 대신 null이 바인딩됩니다.

### @ConfigurationProperties 어노테이션 유형 활성화

Spring Boot는 @ConfigurationProperties 유형을 바인딩하고 빈으로 등록하는 인프라를 제공합니다.
클래스별로 구성 속성을 활성화하거나 구성 요소 검색과 유사한 방식으로 작동하는 구성 속성 검색을 활성화할 수 있습니다.

경우에 따라 @ConfigurationProperties 어노테이션이 달린 클래스는 스캔에 적합하지 않을 수 있습니다. 예를 들어 자체 자동 구성을 개발하거나 조건부로 활성화하려는 경우입니다.
이러한 경우 @EnableConfigurationProperties 어노테이션을 사용하여 처리할 유형 목록을 지정합니다. 다음 예제와 같이 모든 @Configuration 클래스에서 이 작업을 수행할 수 있습니다.

```java

@Configuration(proxyBeanMethods = false)
@EnableConfigurationProperties(SomeProperties.class)
public class MyConfiguration {

}
```

구성 속성 스캔을 사용하려면 애플리케이션에 @ConfigurationPropertiesScan 어노테이션을 추가하세요.
일반적으로 @SpringBootApplication 어노테이션이 달린 기본 애플리케이션 클래스에 추가되지만 모든 @Configuration 클래스에 추가할 수 있습니다.
기본적으로 어노테이션을 선언하는 클래스의 패키지에서 스캔이 발생합니다. 검사할 특정 패키지를 정의하려는 경우 다음 예와 같이 수행할 수 있습니다.

```java

@SpringBootApplication
@ConfigurationPropertiesScan({"com.example.app", "com.example.another"})
public class MyApplication {

}
```

> Memo
>
> @ConfigurationProperties빈이 구성 속성 스캔을 사용하거나 를 통해 등록 되면 @EnableConfigurationProperties빈의 일반적인 이름은 <prefix>-<fqn> 여기서 는
> 어노테이션 <prefix>에 지정된 환경 키 접두어 이고 빈의 정규화된 이름입니다. 어노테이션이 접두부를 제공하지 않으면 Bean의 완전한 이름만 사용됩니다.@ConfigurationProperties<fqn>
>
> 위 예제의 빈 이름은 com.example.app-com.example.app.SomeProperties입니다.

@ConfigurationProperties는 환경만 처리하고 특히 컨텍스트에서 다른 빈을 주입하지 않는 것이 좋습니다.
코너 케이스의 경우 세터 주입을 사용하거나 프레임워크에서 제공하는 *Aware 인터페이스(예: 환경에 액세스해야 하는 경우 EnvironmentAware)를 사용할 수 있습니다.
여전히 생성자를 사용하여 다른 빈을 주입하려면 구성 속성 빈에 @Component로 어노테이션을 달고 JavaBean 기반 속성 바인딩을 사용해야 합니다.

### @ConfigurationProperties 어노테이션 유형 사용

이 스타일의 구성은 다음 예제와 같이 SpringApplication 외부 YAML 구성에서 특히 잘 작동합니다.

```yaml
my:
  service:
    remote-address: 192.168.1.1
    security:
      username: "admin"
      roles:
        - "USER"
        - "ADMIN"
```

@ConfigurationProperties 빈으로 작업하려면 다음 예제와 같이 다른 빈과 동일한 방식으로 주입할 수 있습니다.

```java

@Service
public class MyService {

    private final SomeProperties properties;

    public MyService(SomeProperties properties) {
        this.properties = properties;
    }

    public void openConnection() {
        Server server = new Server(this.properties.getRemoteAddress());
        server.start();
        // ...
    }

    // ...

}
```

### 타사 구성

클래스에 어노테이션을 추가하는 데 사용할 뿐만 아니라 공용 메서드 @ConfigurationProperties에서도 사용할 수 있습니다 . @Bean 이렇게 하면 제어할 수 없는 타사 구성 요소에 속성을 바인딩하려는
경우 특히 유용할 수 있습니다.

환경 속성에서 빈을 구성하려면 다음 예제와 같이 해당 빈 등록에 @ConfigurationProperties를 추가합니다.

```java

@Configuration(proxyBeanMethods = false)
public class ThirdPartyConfiguration {

    @Bean
    @ConfigurationProperties(prefix = "another")
    public AnotherComponent anotherComponent() {
        return new AnotherComponent();
    }

}
```

다른 접두사로 정의된 모든 JavaBean 속성은 이전 SomeProperties 예제와 유사한 방식으로 해당 AnotherComponent 빈에 매핑됩니다

### 이완된 바인딩

Spring Boot는 Environment 속성을 @ConfigurationProperties bean에 바인딩하기 위해 일부 완화된 규칙을 사용하므로 Environment 속성 이름과 bean 속성 이름이 정확히
일치할 필요가 없습니다.
이것이 유용한 일반적인 예에는 대시로 구분된 환경 속성(예: context-path가 contextPath에 바인딩됨) 및 대문자로 표시된 환경 속성(예: PORT가 포트에 바인딩됨)이 포함됩니다.

예를 들어 다음 @ConfigurationProperties 클래스를 고려하십시오.

```java

@ConfigurationProperties(prefix = "my.main-project.person")
public class MyPersonProperties {

    private String firstName;

    public String getFirstName() {
        return this.firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

}
```

앞의 코드에서 다음 속성 이름을 모두 사용할 수 있습니다.

Table 6. relaxed binding

| 속성                                | 노트                                             |
|-----------------------------------|------------------------------------------------|
| my.main-project.person.first-name | .properties 및 .yml 파일에서 사용하도록 권장되는 케밥 케이스.     |
| my.main-project.person.firstName  | 표준 카멜 케이스 구문.                                  |
| my.main-project.person.first_name | .properties 및 .yml 파일에서 사용하기 위한 대체 형식인 밑줄 표기법. |
| MY_MAINPROJECT_PERSON_FIRSTNAME   | 시스템 환경 변수를 사용할 때 권장되는 대문자 형식입니다.               |

> Note
>
> 어노테이션의 접두사 값은 kebab 케이스(my.main-project.person와 같이 소문자 및 -로 구분)여야 합니다.

Table 7. relaxed binding rules per property source

| 속성 소스                 | 심플                                         | 리스트                             |
|-----------------------|--------------------------------------------|---------------------------------|
| Properties Files      | 카멜 케이스, 케밥 케이스 또는 밑줄 표기법                   | [ ] 또는 쉼표로 구분된 값을 사용하는 표준 목록 구문 |
| YAML Files            | 카멜 케이스, 케밥 케이스 또는 밑줄 표기법                   | 표준 YAML 목록 구문 또는 쉼표로 구분된 값      |
| Environment Variables | 밑줄을 구분 기호로 사용하는 대문자 형식입니다(환경 변수에서 바인딩 참조). | 밑줄로 둘러싸인 숫자 값(환경 변수에서 바인딩 참조)   |
| System properties     | 카멜 케이스, 케밥 케이스 또는 밑줄 표기법                   | [ ] 또는 쉼표로 구분된 값을 사용하는 표준 목록 구문 |

> Tip
>
> 가능하면 my.person.first-name=Rod와 같은 소문자 케밥 형식으로 속성을 저장하는 것이 좋습니다.

### Binding Maps

지도 속성에 바인딩할 때 원래 키 값이 보존되도록 특수 대괄호 표기법을 사용해야 할 수 있습니다. 키가 []로 둘러싸여 있지 않으면 영숫자가 아닌 모든 문자, - 또는 . 제거됩니다.

```yaml
my:
  map:
    "[/key1]": "value1"
    "[/key2]": "value2"
    "/key3": "value3"
```

> Note
>
> YAML 파일의 경우 키를 올바르게 구문 분석하려면 대괄호를 따옴표로 묶어야 합니다.

위의 속성은 맵의 키로 /key1, /key2 및 key3을 사용하여 맵에 바인딩됩니다. 슬래시는 대괄호로 묶이지 않았기 때문에 key3에서 제거되었습니다.

스칼라 값에 바인딩할 때 . []로 둘러쌀 필요가 없습니다. 스칼라 값에는 Object를 제외한 java.lang 패키지의 enum 및 모든 유형이 포함됩니다.
a.b=c를 Map<String, String>에 바인딩하면 . 키에 {"a.b"="c"} 항목이 있는 맵을 반환합니다. 다른 유형의 경우 키에 `.`가 포함되어 있으면 대괄호 표기법을 사용해야 합니다.
예를 들어 a.b=c를 Map<String, Object>에 바인딩하면 {"a"={"b"="c"}} 항목이 있는 맵이 반환되는 반면 [a.b]=c는 항목이 {인 맵이 반환됩니다. "a.b"="c"}.

### 환경 변수에서 바인딩

대부분의 운영 체제는 환경 변수에 사용할 수 있는 이름에 대해 엄격한 규칙을 적용합니다.
예를 들어 Linux 셸 변수에는 문자(a ~ z 또는 A ~ Z), 숫자(0 ~ 9) 또는 밑줄 문자(_)만 포함될 수 있습니다. 관례적으로 Unix 쉘 변수는 이름도 대문자로 되어 있습니다.

Spring Boot의 완화된 바인딩 규칙은 가능한 한 이러한 명명 제한과 호환되도록 설계되었습니다.

정식 형식의 속성 이름을 환경 변수 이름으로 변환하려면 다음 규칙을 따를 수 있습니다.

- 점(.)을 밑줄(_)로 교체
- 대시(-)를 제거하십시오.
- 대문자로 변환합니다.

예를 들어 구성 속성 spring.main.log-startup-info는 SPRING_MAIN_LOGSTARTUPINFO라는 환경 변수입니다.

개체 목록에 바인딩할 때 환경 변수를 사용할 수도 있습니다. 목록에 바인딩하려면 변수 이름에서 요소 번호를 밑줄로 묶어야 합니다.

예를 들어 구성 속성 my.service[0].other는 MY_SERVICE_0_OTHER라는 환경 변수를 사용합니다.

### 복합 유형 병합

목록이 둘 이상의 위치에 구성된 경우 재정의는 전체 목록을 대체하여 작동합니다.

예를 들어 이름과 설명 속성이 기본적으로 null인 MyPojo 개체가 있다고 가정합니다. 다음 예제는 MyProperties에서 MyPojo 객체 목록을 노출합니다.

```java

@ConfigurationProperties("my")
public class MyProperties {

    private final List<MyPojo> list = new ArrayList<>();

    public List<MyPojo> getList() {
        return this.list;
    }

}
```

다음 구성을 고려하십시오.

```yaml
my:
  list:
    - name: "my name"
      description: "my description"
---
spring:
  config:
    activate:
      on-profile: "dev"
my:
  list:
    - name: "my another name"
```

dev 프로필이 활성화되지 않은 경우 MyProperties.list에는 이전에 정의된 대로 하나의 MyPojo 항목이 포함됩니다.
그러나 dev 프로필이 활성화된 경우 목록에는 여전히 하나의 항목만 포함됩니다(`my another name`의 이름과 `description`의 널값 포함). 이 구성은 목록에 두 번째 MyPojo 인스턴스를
추가하지 않으며 항목을 병합하지 않습니다.
이 구성은 목록에 두 번째 MyPojo 인스턴스를 추가하지 않으며 항목을 병합하지 않습니다.

목록이 여러 프로필에 지정된 경우 우선 순위가 가장 높은 프로필(해당 프로필만)이 사용됩니다. 다음 예를 고려하십시오.

```yaml
my:
  list:
    - name: "my name"
      description: "my description"
    - name: "another name"
      description: "another description"
---
spring:
  config:
    activate:
      on-profile: "dev"
my:
  list:
    - name: "my another name"
```

앞의 예에서 dev 프로필이 활성화된 경우 MyProperties.list에는 하나의 MyPojo 항목(my another name의 이름과 null의 설명 포함)이 포함됩니다.
YAML의 경우 쉼표로 구분된 목록과 YAML 목록을 모두 사용하여 목록의 콘텐츠를 완전히 재정의할 수 있습니다.

맵 속성의 경우 여러 소스에서 가져온 속성 값으로 바인딩할 수 있습니다.
그러나 여러 소스의 동일한 속성에 대해서는 우선 순위가 가장 높은 속성이 사용됩니다. 다음 예제는 MyProperties에서 Map<String, MyPojo>를 노출합니다.

```java

@ConfigurationProperties("my")
public class MyProperties {

    private final Map<String, MyPojo> map = new LinkedHashMap<>();

    public Map<String, MyPojo> getMap() {
        return this.map;
    }

}
```

다음 구성을 고려하십시오.

```yaml
my:
  map:
    key1:
      name: "my name 1"
      description: "my description 1"
---
spring:
  config:
    activate:
      on-profile: "dev"
my:
  map:
    key1:
      name: "dev name 1"
    key2:
      name: "dev name 2"
      description: "dev description 2"
```

개발 프로필이 활성화되지 않은 경우 MyProperties.map에는 키가 key1인 항목 하나(`my name 1`의 이름과 `my description 1`의 설명 포함)가 포함됩니다
그러나 dev 프로필이 활성화된 경우 map에는 key1(`dev name 1`의 이름과 `my description 1`의 설명 포함) 및 key2(`dev name 2`의 이름과 `my description 2`
의 설명 포함) 키가 있는 두 개의 항목이 포함됩니다. .

> Note
>
> 앞의 병합 규칙은 파일뿐만 아니라 모든 속성 소스의 속성에 적용됩니다.

### 속성 변환

Spring Boot는 @ConfigurationProperties 빈에 바인딩할 때 외부 애플리케이션 속성을 올바른 유형으로 강제 변환하려고 시도합니다.
사용자 지정 유형 변환이 필요한 경우 ConversionService 빈(conversionService라는 빈 포함) 또는 사용자 지정 속성 편집기(CustomEditorConfigurer 빈을 통해) 또는 사용자
지정 변환기(@ConfigurationPropertiesBinding으로 주석이 달린 빈 정의 포함)를 제공할 수 있습니다.

> Note
>
> 이 bean은 애플리케이션 수명 주기 동안 매우 초기에 요청되므로 ConversionService가 사용하는 종속성을 제한해야 합니다.
> 일반적으로 필요한 종속성은 생성 시 완전히 초기화되지 않을 수 있습니다.
> 구성 키 강제 변환에 필요하지 않고 @ConfigurationPropertiesBinding으로 인증된 사용자 정의 변환기에만 의존하는 경우 사용자 정의 ConversionService의 이름을 바꿀 수
> 있습니다.

### 기간 변환

Spring Boot는 기간 표현을 전담 지원합니다. java.time.Duration 속성을 노출하는 경우 애플리케이션 속성에서 다음 형식을 사용할 수 있습니다.

- 일반 긴 표현(@DurationUnit이 지정되지 않은 경우 기본 단위로 밀리초 사용)
- java.time.Duration에서 사용하는 표준 ISO-8601 형식
- 값과 단위가 결합된 보다 읽기 쉬운 형식(10s는 10초를 의미함)

다음 예를 고려하십시오.

```java

@ConfigurationProperties("my")
public class MyProperties {

    @DurationUnit(ChronoUnit.SECONDS)
    private Duration sessionTimeout = Duration.ofSeconds(30);

    private Duration readTimeout = Duration.ofMillis(1000);

    // getters / setters...

}
```

세션 제한 시간을 30초로 지정하려면 30, PT30S 및 30s가 모두 동일합니다. 500ms의 읽기 타임아웃은 500, PT0.5S 및 500ms 형식으로 지정할 수 있습니다.

지원되는 모든 단위를 사용할 수도 있습니다.

- ns for nanoseconds
- us for microseconds
- ms for milliseconds
- s for seconds
- m for minutes
- h for hours
- d for days

기본 단위는 밀리초이며 위의 샘플에 표시된 대로 @DurationUnit을 사용하여 재정의할 수 있습니다

생성자 바인딩을 사용하려는 경우 다음 예제와 같이 동일한 속성을 노출할 수 있습니다.

```java

@ConfigurationProperties("my")
@ConstructorBinding
public class MyProperties {

    // fields...

    public MyProperties(@DurationUnit(ChronoUnit.SECONDS) @DefaultValue("30s") Duration sessionTimeout,
                        @DefaultValue("1000ms") Duration readTimeout) {
        this.sessionTimeout = sessionTimeout;
        this.readTimeout = readTimeout;
    }

    // getters...

}
```

> Tip
>
> Long 속성을 업그레이드하는 경우 밀리초가 아닌 경우 단위를 정의해야 합니다(@DurationUnit 사용). 이렇게 하면 훨씬 더 풍부한 형식을 지원하면서 투명한 업그레이드 경로가 제공됩니다.

### 전환 기간

기간 외에도 Spring Boot는 java.time.Period 유형과도 작동할 수 있습니다. 애플리케이션 속성에서 다음 형식을 사용할 수 있습니다.

- 일반 int 표현(@PeriodUnit이 지정되지 않은 경우 일을 기본 단위로 사용)
- java.time.Period에서 사용하는 표준 ISO-8601 형식
- 값과 단위 쌍이 결합된 더 간단한 형식(1y3d는 1년 3일을 의미함)

다음 단위는 간단한 형식으로 지원됩니다.

- y for years
- m for months
- w for weeks
- d for days

> Note
>
> java.time.Period 유형은 실제로 주 수를 저장하지 않으며 "7일"을 의미하는 지름길입니다.

### 데이터 크기 변환

Spring Framework에는 크기를 바이트 단위로 표현하는 DataSize 값 유형이 있습니다. DataSize 속성을 노출하는 경우 애플리케이션 속성에서 다음 형식을 사용할 수 있습니다.

- 일반적인 긴 표현(@DataSizeUnit이 지정되지 않은 경우 바이트를 기본 단위로 사용)
- 값과 단위가 결합된 더 읽기 쉬운 형식(10MB는 10메가바이트를 의미함)
  다음 예를 고려하십시오.

```java

@ConfigurationProperties("my")
public class MyProperties {

    @DataSizeUnit(DataUnit.MEGABYTES)
    private DataSize bufferSize = DataSize.ofMegabytes(2);

    private DataSize sizeThreshold = DataSize.ofBytes(512);

    // getters/setters...

}
```

버퍼 크기를 10MB로 지정하려면 10MB와 10MB가 동일합니다. 256바이트의 크기 임계값은 256 또는 256B로 지정할 수 있습니다.

지원되는 모든 단위를 사용할 수도 있습니다. 이것들은:

- B for bytes
- KB for kilobytes
- MB for megabytes
- GB for gigabytes
- TB for terabytes

기본 단위는 바이트이며 위의 샘플에 표시된 대로 @DataSizeUnit을 사용하여 재정의할 수 있습니다.

생성자 바인딩을 사용하려는 경우 다음 예제와 같이 동일한 속성을 노출할 수 있습니다.

```java

@ConfigurationProperties("my")
@ConstructorBinding
public class MyProperties {

    // fields...

    public MyProperties(@DataSizeUnit(DataUnit.MEGABYTES) @DefaultValue("2MB") DataSize bufferSize,
                        @DefaultValue("512B") DataSize sizeThreshold) {
        this.bufferSize = bufferSize;
        this.sizeThreshold = sizeThreshold;
    }

    // getters...

}
```

> Tip
>
> Long 속성을 업그레이드하는 경우 바이트가 아닌 경우 단위를 정의해야 합니다(@DataSizeUnit 사용). 이렇게 하면 훨씬 더 풍부한 형식을 지원하면서 투명한 업그레이드 경로가 제공됩니다.

### @ConfigurationProperties 유효성 검사

Spring Boot는 @ConfigurationProperties 클래스에 Spring의 @Validated 어노테이션이 추가될 때마다 유효성 검사를 시도합니다.
구성 클래스에서 직접 JSR-303 javax.validation 제약 조건 주석을 사용할 수 있습니다.
이렇게 하려면 다음 예제와 같이 호환되는 JSR-303 구현이 클래스 경로에 있는지 확인한 다음 필드에 제약 조건 주석을 추가합니다.

```java

@ConfigurationProperties("my.service")
@Validated
public class MyProperties {

    @NotNull
    private InetAddress remoteAddress;

    // getters/setters...

}
```

> Tip
>
> @Validated로 구성 속성을 생성하는 @Bean 메서드에 주석을 달아 유효성 검사를 트리거할 수도 있습니다.

중첩된 속성에 대해 유효성 검사가 항상 트리거되도록 하려면 속성이 없는 경우에도 관련 필드에 @Valid 주석을 달아야 합니다.
다음 예제는 앞의 MyProperties 예제를 기반으로 합니다.

```java

@ConfigurationProperties("my.service")
@Validated
public class MyProperties {

    @NotNull
    private InetAddress remoteAddress;

    @Valid
    private final Security security = new Security();

    // getters/setters...

    public static class Security {

        @NotEmpty
        private String username;

        // getters/setters...

    }

}
```

configurationPropertiesValidator라는 빈 정의를 생성하여 커스텀 Spring Validator를 추가할 수도 있습니다.
@Bean 메소드는 정적으로 선언되어야 합니다.구성 속성 유효성 검사기는 애플리케이션의 수명 주기 초기에 생성되며 @Bean 메서드를 정적으로 선언하면 @Configuration 클래스를 인스턴스화하지 않고도 빈을
생성할 수 있습니다.
이렇게 하면 초기 인스턴스화로 인해 발생할 수 있는 문제를 피할 수 있습니다.

> Tip
>
> spring-boot-actuator 모듈에는 모든 @ConfigurationProperties 빈을 노출하는 엔드포인트가 포함되어 있습니다. 웹 브라우저에서 /actuator/configprops를 가리키거나
> 동등한 JMX 끝점을 사용합니다. 자세한 내용은 "생산 준비 기능" 섹션을 참조하십시오.

### @ConfigurationProperties vs. @Value

@Value 어노테이션은 핵심 컨테이너 기능이며 유형 안전 구성 속성과 동일한 기능을 제공하지 않습니다. 다음 표에는 @ConfigurationProperties 및 @Value에서 지원하는 기능이 요약되어
있습니다.

| Feature           | @ConfigurationProperties | @Value                   |
|-------------------|--------------------------|--------------------------|
| Relaxed binding   | Yes                      | Limited (see note below) |
| Meta-data support | Yes                      | No                       |
| SpEL evaluation   | No                       | Yes                      |

> Note
>
> @Value를 사용하려면 표준 형식(소문자만 사용하는 케밥-케이스)을 사용하여 속성 이름을 참조하는 것이 좋습니다.
> 이렇게 하면 @ConfigurationProperties를 완화 바인딩할 때와 동일한 논리를 Spring Boot에서 사용할 수 있습니다.
>
> 예를 들어 @Value("${demo.item-price}")는 application.properties 파일에서 demo.item-price 및 demo.itemPrice 형식을 선택하고 시스템 환경에서
> DEMO_ITEMPRICE를 선택합니다.
> @Value("${demo.itemPrice}")를 대신 사용한 경우 demo.item-price 및 DEMO_ITEMPRICE는 고려되지 않습니다.

자신의 구성 요소에 대한 구성 키 집합을 정의하는 경우 @ConfigurationProperties 주석이 달린 POJO에 그룹화하는 것이 좋습니다. 이렇게 하면 자신의 빈에 주입할 수 있는 구조화되고 형식이 안전한
개체가 제공됩니다.

응용 프로그램 속성 파일의 SpEL 표현식은 이러한 파일을 구문 분석하고 환경을 채울 때 처리되지 않습니다. 그러나 @Value에 SpEL 표현식을 작성할 수 있습니다.
애플리케이션 속성 파일의 속성 값이 SpEL 표현식인 경우 @Value를 통해 소비될 때 평가됩니다.

## 7.3 프로파일

Spring Profiles는 애플리케이션 구성의 일부를 분리하고 특정 환경에서만 사용할 수 있도록 하는 방법을 제공합니다.
다음 예제와 같이 모든 @Component, @Configuration 또는 @ConfigurationProperties를 @Profile로 표시하여 로드 시기를 제한할 수 있습니다.

```java

@Configuration(proxyBeanMethods = false)
@Profile("production")
public class ProductionConfiguration {

    // ...

}
```

> Note
>
> 자동 스캔이 아닌 @EnableConfigurationProperties를 통해 @ConfigurationProperties 빈을 등록한 경우 @EnableConfigurationProperties 주석이 있는
> @Configuration 클래스에 @Profile 주석을 지정해야 합니다.
> @ConfigurationProperties를 스캔하는 경우 @ConfigurationProperties 클래스 자체에 @Profile을 지정할 수 있다.

spring.profiles.active 환경 속성을 사용하여 활성화된 프로필을 지정할 수 있습니다.
이 장의 앞부분에서 설명한 방법으로 속성을 지정할 수 있습니다. 예를 들어 다음 예제와 같이 application.properties에 포함할 수 있습니다.

```yaml
spring:
  profiles:
    active: "dev,hsqldb"
```

--spring.profiles.active=dev,hsqldb 스위치를 사용하여 명령줄에서 지정할 수도 있습니다.

활성화된 프로필이 없으면 기본 프로필이 활성화됩니다. 기본 프로필의 이름은 default이며 다음 예제와 같이 spring.profiles.default 환경 속성을 사용하여 조정할 수 있습니다.

```yaml
spring:
  profiles:
    default: "none"
```

spring.profiles.active 및 spring.profiles.default는 프로필이 아닌 특정 문서에서만 사용할 수 있습니다. 즉, spring.config.activate.on-profile에 의해
활성화된 프로필 특정 파일이나 문서에 포함될 수 없습니다.

```yaml
# this document is valid
spring:
  profiles:
    active: "prod"
---
# this document is invalid
spring:
  config:
    activate:
      on-profile: "prod"
  profiles:
    active: "metrics"
```

### 7.3.1 활성 프로필 추가

spring.profiles.active 속성은 다른 속성과 동일한 순서 규칙을 따릅니다. 가장 높은 PropertySource가 우선합니다
즉, application.properties에서 활성 프로필을 지정한 다음 명령줄 스위치를 사용하여 바꿀 수 있습니다.

경우에 따라 활성 프로필을 대체하는 대신 추가하는 속성이 있는 것이 유용합니다. spring.profiles.include 속성은 spring.profiles.active 속성에 의해 활성화된 프로필 위에 활성
프로필을 추가하는 데 사용할 수 있습니다.
SpringApplication 진입점에는 추가 프로필을 설정하기 위한 Java API도 있습니다. SpringApplication의 setAdditionalProfiles() 메소드를 참조하십시오.

예를 들어, 다음 속성을 가진 애플리케이션이 실행되면 --spring.profiles.active 스위치를 사용하여 실행되는 경우에도 공통 및 로컬 프로파일이 활성화됩니다.

```yaml
spring:
  profiles:
    include:
      - "common"
      - "local"
```

> Warning
>
> spring.profiles.active와 마찬가지로 spring.profiles.include는 프로필이 아닌 특정 문서에서만 사용할 수 있습니다. 이는
> spring.config.activate.on-profile에 의해 활성화된 프로파일 특정 파일이나 문서에 포함될 수 없음을 의미합니다.

다음 섹션에서 설명하는 프로필 그룹은 지정된 프로필이 활성화된 경우 활성 프로필을 추가하는 데 사용할 수도 있습니다.

### 7.3.2. 프로필 그룹

경우에 따라 애플리케이션에서 정의하고 사용하는 프로필이 너무 세분화되어 사용하기 번거로워질 수 있습니다. 예를 들어 데이터베이스 및 메시징 기능을 독립적으로 활성화하는 데 사용하는 proddb 및 prodmq
프로필이 있을 수 있습니다.

이를 돕기 위해 Spring Boot에서는 프로필 그룹을 정의할 수 있습니다. 프로필 그룹을 사용하면 관련된 프로필 그룹의 논리적 이름을 정의할 수 있습니다.

예를 들어 proddb 및 prodmq 프로필로 구성된 프로덕션 그룹을 만들 수 있습니다.

```yaml
spring:
  profiles:
    group:
      production:
        - "proddb"
        - "prodmq"
```

이제 --spring.profiles.active=production을 사용하여 애플리케이션을 시작하여 프로덕션, proddb 및 prodmq 프로필을 한 번에 활성화할 수 있습니다.

### 7.3.3. 프로그래밍 방식으로 프로필 설정

애플리케이션이 실행되기 전에 SpringApplication.setAdditionalProfiles(… )를 호출하여 프로그래밍 방식으로 활성 프로필을 설정할 수 있습니다. Spring의
ConfigurableEnvironment 인터페이스를 사용하여 프로필을 활성화하는 것도 가능합니다.

### 7.3.4 프로필별 구성 파일

application.properties(또는 application.yml)와 @ConfigurationProperties를 통해 참조되는 파일의 프로필별 변형은 파일로 간주되어 로드됩니다. 자세한 내용은 "프로필
특정 파일"을 참조하십시오.

## 7.4 로깅

Spring Boot는 모든 내부 로깅에 Commons Logging을 사용하지만 기본 로그 구현은 열어 둡니다.
Java Util Logging, Log4J2 및 Logback에 대한 기본 구성이 제공됩니다. 각각의 경우에 로거는 선택적 파일 출력과 함께 콘솔 출력을 사용하도록 사전 구성됩니다.

기본적으로 "스타터"를 사용하는 경우 로깅에 Logback이 사용됩니다. Java Util Logging, Commons Logging, Log4J 또는 SLF4J를 사용하는 종속 라이브러리가 모두 올바르게
작동하도록 적절한 Logback 라우팅도 포함됩니다.

> Tip
>
> Java에 사용할 수 있는 많은 로깅 프레임워크가 있습니다. 위의 목록이 혼란스러워 보이더라도 걱정하지 마십시오. 일반적으로 로깅 종속성을 변경할 필요가 없으며 Spring Boot 기본값이 제대로 작동합니다.

> Tip
>
> 애플리케이션을 서블릿 컨테이너 또는 애플리케이션 서버에 배포할 때 Java Util Logging API로 수행된 로깅은 애플리케이션의 로그로 라우팅되지 않습니다.
> 이렇게 하면 컨테이너 또는 컨테이너에 배포된 다른 애플리케이션에서 수행한 로깅이 애플리케이션의 로그에 표시되지 않습니다.

### 7.4.1. 로그 포맷

Spring Boot의 기본 로그 출력은 다음 예제와 유사합니다.

```text
2022-08-17 19:56:50.643  INFO 1103 --- [           main] o.s.b.d.f.s.MyApplication                : Starting MyApplication using Java 1.8.0_345 on myhost with PID 1103 (/opt/apps/myapp.jar started by myuser in /opt/apps/)
2022-08-17 19:56:50.648  INFO 1103 --- [           main] o.s.b.d.f.s.MyApplication                : No active profile set, falling back to 1 default profile: "default"
2022-08-17 19:56:53.102  INFO 1103 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2022-08-17 19:56:53.124  INFO 1103 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2022-08-17 19:56:53.125  INFO 1103 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.65]
2022-08-17 19:56:53.284  INFO 1103 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2022-08-17 19:56:53.286  INFO 1103 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 2558 ms
2022-08-17 19:56:55.450  INFO 1103 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2022-08-17 19:56:55.469  INFO 1103 --- [           main] o.s.b.d.f.s.MyApplication                : Started MyApplication in 5.464 seconds (JVM running for 6.035)
```

다음 항목이 출력됩니다.

- 날짜 및 시간: 밀리초 정밀도로 쉽게 정렬할 수 있습니다.
- 로그 수준: ERROR, WARN, INFO, DEBUG 또는 TRACE.
- 프로세스 ID.
- 실제 로그 메시지의 시작을 구분하는 --- 구분 기호입니다.
- 스레드 이름: 대괄호로 묶습니다(콘솔 출력의 경우 잘릴 수 있음).
- 로거 이름: 이것은 일반적으로 소스 클래스 이름입니다(종종 축약됨)
- 로그 메세지

> Note
>
> Logback에는 FATAL 수준이 없습니다. ERROR에 매핑됩니다

### 7.4.2 콘솔 출력

기본 로그 구성은 메시지가 기록될 때 콘솔에 메시지를 표시합니다. 기본적으로 ERROR 수준, WARN 수준 및 INFO 수준 메시지가 기록됩니다.
--debug 플래그로 애플리케이션을 시작하여 "디버그" 모드를 활성화할 수도 있습니다.

```shell
java -jar myapp.jar --debug
```

> Note
>
> application.properties에서 debug=true를 지정할 수도 있습니다.

디버그 모드가 활성화되면 핵심 로거(임베디드 컨테이너, Hibernate 및 Spring Boot) 선택이 더 많은 정보를 출력하도록 구성됩니다. 디버그 모드를 활성화해도 DEBUG 수준의 모든 메시지를 기록하도록
애플리케이션이 구성되지는 않습니다.

또는 --trace 플래그(또는 application.properties에서 trace=true)로 애플리케이션을 시작하여 "추적" 모드를 활성화할 수 있습니다.
이렇게 하면 핵심 로거(임베디드 컨테이너, Hibernate 스키마 생성 및 전체 Spring 포트폴리오) 선택에 대한 추적 로깅이 활성화됩니다

### 색상으로 구분된 출력

터미널이 ANSI를 지원하는 경우 가독성을 높이기 위해 컬러 출력이 사용됩니다. spring.output.ansi.enabled를 지원되는 값으로 설정하여 자동 감지를 재정의할 수 있습니다.

색상 코딩은 %clr 변환 단어를 사용하여 구성됩니다. 가장 간단한 형태에서 변환기는 다음 예제와 같이 로그 수준에 따라 출력 색상을 지정합니다

```text
%clr(%5p)
```

다음 표에서는 색상에 대한 로그 수준 매핑을 설명합니다.

| 레벨    | 색깔  |
|-------|-----|
| FATAL | 빨강  |
| ERROR | 빨강  |
| WARN  | 노랑  |
| INFO  | 초록  |
| DEBUG | 초록  |
| TRACE | 초록  |

또는 변환에 대한 옵션으로 제공하여 사용해야 하는 색상이나 스타일을 지정할 수 있습니다. 예를 들어 텍스트를 노란색으로 만들려면 다음 설정을 사용합니다.

```text
%clr(%d{yyyy-MM-dd HH:mm:ss.SSS}){yellow}
```

다음 색상 및 스타일이 지원됩니다

- 파란색
- 청록색
- 희미한
- 초록
- 마젠타
- 빨간색
- 노란색

### 7.4.3. 파일 출력

기본적으로 Spring Boot는 콘솔에만 기록하고 로그 파일을 작성하지 않습니다.
콘솔 출력 외에 로그 파일을 작성하려면 logging.file.name 또는 logging.file.path 속성(예: application.properties에서)을 설정해야 합니다.

다음 표는 logging.* 속성을 함께 사용할 수 있는 방법을 보여줍니다.

| logging.file.name | logging.file.path | 예        | 설명                                                             |
|-------------------|-------------------|----------|----------------------------------------------------------------|
| (none)            | (none)            || 콘솔 전용 로깅 |
| 특정 파일             | (none)            | my.log   | 지정된 로그 파일에 씁니다. 이름은 정확한 위치이거나 현재 디렉터리에 상대적일 수 있습니다.            |
| (none)            | 특정 디렉터리           | /var/log | 지정된 디렉토리에 spring.log를 씁니다. 이름은 정확한 위치이거나 현재 디렉터리에 상대적일 수 있습니다. |

로그 파일은 10MB에 도달하면 교체되며 콘솔 출력과 마찬가지로 ERROR 수준, WARN 수준 및 INFO 수준 메시지가 기본적으로 기록됩니다.

> Tip
>
> 로깅 속성은 실제 로깅 인프라와 독립적입니다. 결과적으로 특정 구성 키(예: Logback의 logback.configurationFile)는 Spring Boot에서 관리되지 않습니다.

### 7.4.4 파일 로테이션

Logback을 사용하는 경우 application.properties 또는 application.yaml 파일을 사용하여 로그 로테이션 설정을 미세 조정할 수 있습니다.
다른 모든 로깅 시스템의 경우 로테이션 설정을 직접 구성해야 합니다(예: Log4J2를 사용하는 경우 log4j2.xml 또는 log4j2-spring.xml 파일을 추가할 수 있음).

다음 로테이션 정책 속성이 지원됩니다.

| 이름                                                  | 설명                                    |
|-----------------------------------------------------|---------------------------------------|
| logging.logback.rollingpolicy.file-name-pattern     | 로그 아카이브를 만드는 데 사용되는 파일 이름 패턴입니다.      |
| logging.logback.rollingpolicy.clean-history-on-star | 애플리케이션이 시작될 때 로그 아카이브 정리가 발생해야 하는 경우. |
| logging.logback.rollingpolicy.max-file-size         | 아카이브되기 전 로그 파일의 최대 크기.                |
| logging.logback.rollingpolicy.total-size-cap        | 로그 아카이브가 삭제되기 전에 취할 수 있는 최대 크기입니다.    |
| logging.logback.rollingpolicy.max-history           | 유지할 아카이브 로그 파일의 최대 수(기본값은 7)입니다.      |

### 7.4.5 로그 수준

지원되는 모든 로깅 시스템은 logging.level.<logger-name>=<level>을 사용하여 Spring 환경(예: application.properties)에서 로거 수준을 설정할 수 있습니다. 여기서
수준은 TRACE, DEBUG, INFO 중 하나입니다.
경고, 오류, 치명적 또는 꺼짐. 루트 로거는 logging.level.root를 사용하여 구성할 수 있습니다.

다음 예는 application.properties의 잠재적인 로깅 설정을 보여줍니다.

```yaml
logging:
  level:
    root: "warn"
    org.springframework.web: "debug"
    org.hibernate: "error"
```

환경 변수를 사용하여 로깅 수준을 설정할 수도 있습니다. 예를 들어, LOGGING_LEVEL_ORG_SPRINGFRAMEWORK_WEB=DEBUG는 org.springframework.web을 DEBUG로
설정합니다.

> Note
>
> 위의 접근 방식은 패키지 수준 로깅에만 적용됩니다.완화된 바인딩은 항상 환경 변수를 소문자로 변환하므로 이러한 방식으로 개별 클래스에 대한 로깅을 구성할 수 없습니다.
> 클래스에 대한 로깅을 구성해야 하는 경우 SPRING_APPLICATION_JSON 변수를 사용할 수 있습니다.

### 7.4.6. 로그그룹

관련 로거를 함께 그룹화하여 모두 동시에 구성할 수 있는 것이 유용한 경우가 많습니다. 예를 들어 일반적으로 모든 Tomcat 관련 로거의 로깅 수준을 변경할 수 있지만 최상위 패키지를 쉽게 기억할 수 없습니다.

이를 돕기 위해 Spring Boot를 사용하면 Spring 환경에서 로깅 그룹을 정의할 수 있습니다. 예를 들어, 다음은 application.properties에 추가하여 "tomcat" 그룹을 정의하는
방법입니다

```yaml
logging:
  group:
    tomcat: "org.apache.catalina,org.apache.coyote,org.apache.tomcat"
```

일단 정의되면 한 줄로 그룹의 모든 로거에 대한 수준을 변경할 수 있습니다.

```yaml
logging:
  level:
    tomcat: "trace"
```

Spring Boot에는 기본적으로 사용할 수 있는 다음과 같은 사전 정의된 로깅 그룹이 포함되어 있습니다.

| 이름  | 로거                                                                                                                                                                                                    |
|-----|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| web | org.springframework.core.codec, org.springframework.http, org.springframework.web, org.springframework.boot.actuate.endpoint.web, org.springframework.boot.web.servlet.ServletContextInitializerBeans |
| sql | org.springframework.jdbc.core, org.hibernate.SQL, org.jooq.tools.LoggerListener                                                                                                                       |

### 7.4.7. 로그 종료 후크 사용

애플리케이션 종료 시 로깅 리소스를 해제하기 위해 JVM 종료 시 로그 시스템 정리를 트리거하는 종료 후크가 제공됩니다.
이 종료 후크는 애플리케이션이 war 파일로 배포되지 않는 한 자동으로 등록됩니다. 애플리케이션에 복잡한 컨텍스트 계층이 있는 경우 종료 후크가 요구 사항을 충족하지 못할 수 있습니다.
그렇지 않은 경우 종료 후크를 비활성화하고 기본 로깅 시스템에서 직접 제공하는 옵션을 조사하십시오. 예를 들어 Logback은 각 로거가 자체 컨텍스트에서 생성될 수 있도록 하는 컨텍스트 선택기를 제공합니다.
logging.register-shutdown-hook 속성을 사용하여 종료 후크를 비활성화할 수 있습니다. false로 설정하면 등록이 비활성화됩니다. application.properties 또는
application.yaml 파일에서 속성을 설정할 수 있습니다.

```yaml
logging:
  register-shutdown-hook: false
```

### 7.4.8. 사용자 지정 로그 구성

다양한 로깅 시스템은 클래스 경로에 적절한 라이브러리를 포함하여 활성화할 수 있으며 클래스 경로의 루트 또는 다음 Spring 환경 속성(logging.config)에 지정된 위치에 적절한 구성 파일을 제공하여
추가로 사용자 정의할 수 있습니다.

`org.springframework.boot.logging.LoggingSystem` 시스템 속성을 사용하여 Spring Boot가 특정 로깅 시스템을 사용하도록 강제할 수 있습니다.
값은 LoggingSystem 구현의 정규화된 클래스 이름이어야 합니다. 값 없음을 사용하여 Spring Boot의 로깅 구성을 완전히 비활성화할 수도 있습니다.

> Note
>
> ApplicationContext가 생성되기 전에 로깅이 초기화되기 때문에 Spring @Configuration 파일의 @PropertySources에서 로깅을 제어할 수 없다.
> 로깅 시스템을 변경하거나 완전히 비활성화하는 유일한 방법은 시스템 속성을 통하는 것입니다.

로깅 시스템에 따라 다음 파일이 로드됩니다.

| 로깅 시스템                  | 커스터마이징                                                                    |
|-------------------------|---------------------------------------------------------------------------|
| Logback                 | logback-spring.xml, logback-spring.groovy, logback.xml, or logback.groovy |
| Log4j2                  | log4j2-spring.xml or log4j2.xml                                           |
| JDK (Java Util Logging) | logging.properties                                                        |

> Note
>
> 가능한 경우 로깅 구성에 -spring 변형을 사용하는 것이 좋습니다(예: logback.xml이 아닌 logback-spring.xml).
> 표준 구성 위치를 사용하는 경우 Spring은 로그 초기화를 완전히 제어할 수 없습니다.

> Warning
>
> '실행 가능한 jar'에서 실행할 때 문제를 일으키는 Java Util 로깅과 관련된 알려진 클래스 로딩 문제가 있습니다. 가능한 한 '실행 가능한 jar'에서 실행할 때 이를 피하는 것이 좋습니다.

사용자 지정을 돕기 위해 다음 표에 설명된 대로 몇 가지 다른 속성이 Spring 환경에서 시스템 속성으로 전송됩니다.

| 스프링 환경                            | 시스템 속성                        | 코멘트                                              |
|-----------------------------------|-------------------------------|--------------------------------------------------|
| logging.exception-conversion-word | LOG_EXCEPTION_CONVERSION_WORD | 예외를 기록할 때 사용되는 변환 단어입니다.                         |
| logging.file.name                 | LOG_FILE                      | 정의된 경우 기본 로그 구성에서 사용됩니다.                         |
| logging.file.path                 | LOG_PATH                      | 정의된 경우 기본 로그 구성에서 사용됩니다.                         |
| logging.pattern.console           | CONSOLE_LOG_PATTERN           | 콘솔에서 사용할 로그 패턴(stdout).                          |
| logging.pattern.dateformat        | LOG_DATEFORMAT_PATTERN        | 로그 날짜 형식에 대한 Appender 패턴입니다.                     |
| logging.charset.console           | CONSOLE_LOG_CHARSET           | 콘솔 로깅에 사용할 문자 집합입니다.                             |
| logging.pattern.file              | FILE_LOG_PATTERN              | 파일에서 사용할 로그 패턴입니다(LOG_FILE이 활성화된 경우).            |
| logging.charset.file              | FILE_LOG_CHARSET              | 파일 로깅에 사용할 문자 집합입니다(LOG_FILE이 활성화된 경우).          |
| logging.pattern.level             | LOG_LEVEL_PATTERN             | 로그 수준을 렌더링할 때 사용할 형식입니다(기본값 %5p).                |
| PID                               | PID                           | 현재 프로세스 ID(가능한 경우 발견되고 아직 OS 환경 변수로 정의되지 않은 경우). |

Logback을 사용하는 경우 다음 속성도 전송됩니다.

| 스프링 환경                                               | 시스템 속성                                       | 코멘트                                                      |
|------------------------------------------------------|----------------------------------------------|----------------------------------------------------------|
| logging.logback.rollingpolicy.file-name-pattern      | LOGBACK_ROLLINGPOLICY_FILE_NAME_PATTERN      | 롤오버된 로그 파일 이름의 패턴(기본값 ${LOG_FILE}.%d{yyyy-MM-dd}.%i.gz). |
| logging.logback.rollingpolicy.clean-history-on-start | LOGBACK_ROLLINGPOLICY_CLEAN_HISTORY_ON_START | 시작 시 아카이브 로그 파일을 정리할지 여부입니다.                             |
| logging.logback.rollingpolicy.max-file-size          | LOGBACK_ROLLINGPOLICY_MAX_FILE_SIZE          | 최대 로그 파일 크기.                                             |
| logging.logback.rollingpolicy.total-size-cap         | LOGBACK_ROLLINGPOLICY_TOTAL_SIZE_CAP         | 보관할 로그 백업의 총 크기입니다.                                      |
| logging.logback.rollingpolicy.max-history            | LOGBACK_ROLLINGPOLICY_MAX_HISTORY            | 보관할 최대 아카이브 로그 파일 수                                      |

지원되는 모든 로깅 시스템은 구성 파일을 구문 분석할 때 시스템 속성을 참조할 수 있습니다. 예제는 spring-boot.jar의 기본 구성을 참조하십시오.

- Logback
- Log4j 2
- Java Util logging

> Tip
> 
> 로깅 속성에서 자리 표시자를 사용하려면 기본 프레임워크의 구문이 아닌 Spring Boot의 구문을 사용해야 합니다.
> 특히 Logback을 사용하는 경우 속성 이름과 기본값 사이의 구분 기호로 :를 사용해야 하며 :-를 사용하지 않아야 합니다.

> Tip
> 
> LOG_LEVEL_PATTERN(또는 Logback이 있는 logging.pattern.level)만 재정의하여 MDC 및 기타 임시 콘텐츠를 로그 줄에 추가할 수 있습니다.
> 예를 들어, logging.pattern.level=user:%X{user} %5p를 사용하는 경우 다음 예제와 같이 기본 로그 형식에 "user"에 대한 MDC 항목이 포함됩니다(있는 경우).
```text
2019-08-30 12:30:04.031 user:someone INFO 22174 --- [  nio-8080-exec-0] demo.Controller
Handling authenticated request
```

### 7.4.9. 로그백 확장
Spring Boot에는 고급 구성에 도움이 될 수 있는 Logback에 대한 여러 확장이 포함되어 있습니다. logback-spring.xml 구성 파일에서 이러한 확장을 사용할 수 있습니다.

> Note
> 
> 표준 logback.xml 구성 파일이 너무 일찍 로드되기 때문에 확장을 사용할 수 없습니다. logback-spring.xml을 사용하거나 logging.config 속성을 정의해야 합니다.

> Warning
> 
> 확장은 Logback의 구성 스캔과 함께 사용할 수 없습니다. 그렇게 하려고 하면 구성 파일을 변경하면 다음 중 하나와 유사한 오류가 기록됩니다.
 
```text
ERROR in ch.qos.logback.core.joran.spi.Interpreter@4:71 - no applicable action for [springProperty], current ElementPath is [[configuration][springProperty]]
ERROR in ch.qos.logback.core.joran.spi.Interpreter@4:71 - no applicable action for [springProfile], current ElementPath is [[configuration][springProfile]]
```

### 프로필별 구성
`<springProfile>` 태그를 사용하면 활성 Spring 프로필을 기반으로 구성 섹션을 선택적으로 포함하거나 제외할 수 있습니다. 프로필 섹션은 `<configuration>` 요소 내 어디에서나 지원됩니다.
name 속성을 사용하여 구성을 수락하는 프로필을 지정합니다. <springProfile> 태그는 프로필 이름(예: 스테이징) 또는 프로필 표현식을 포함할 수 있습니다. 프로필 표현식을 사용하면 더 복잡한 프로필 논리를 표현할 수 있습니다(예: production & (eu-central | eu-west)).
자세한 내용은 참조 가이드를 확인하세요. 다음 목록은 세 가지 샘플 프로필을 보여줍니다.

```xml
<springProfile name="staging">
    <!-- configuration to be enabled when the "staging" profile is active -->
</springProfile>

<springProfile name="dev | staging">
    <!-- configuration to be enabled when the "dev" or "staging" profiles are active -->
</springProfile>

<springProfile name="!production">
    <!-- configuration to be enabled when the "production" profile is not active -->
</springProfile>
```

### 환경 속성
`<springProperty>` 태그를 사용하면 Logback 내에서 사용할 Spring 환경의 속성을 노출할 수 있습니다. 이렇게 하면 Logback 구성에서 application.properties 파일의 값에 액세스하려는 경우 유용할 수 있습니다.
이 태그는 Logback의 표준 `<property>` 태그와 유사한 방식으로 작동합니다. 그러나 직접 값을 지정하는 대신 환경에서 속성의 소스를 지정합니다.
로컬 범위가 아닌 다른 위치에 속성을 저장해야 하는 경우 범위 특성을 사용할 수 있습니다.
대체 값이 필요한 경우(환경에서 속성이 설정되지 않은 경우) defaultValue 특성을 사용할 수 있습니다. 다음 예에서는 Logback 내에서 사용할 속성을 노출하는 방법을 보여줍니다.

```xml
<springProperty scope="context" name="fluentHost" source="myapp.fluentd.host"
        defaultValue="localhost"/>
<appender name="FLUENT" class="ch.qos.logback.more.appenders.DataFluentAppender">
    <remoteHost>${fluentHost}</remoteHost>
    ...
</appender>
```

> Note
> 
> 소스는 kebab 대소문자(예: my.property-name)로 지정해야 합니다. 그러나 완화된 규칙을 사용하여 속성을 환경에 추가할 수 있습니다.

## 7.5 국제화
Spring Boot는 지역화된 메시지를 지원하므로 애플리케이션이 다양한 언어 기본 설정을 가진 사용자에게 적합할 수 있습니다. 기본적으로 Spring Boot는 클래스 경로의 루트에서 메시지 리소스 번들의 존재를 찾습니다.

> Note
> 
> 자동 구성은 구성된 리소스 번들에 대한 기본 속성 파일(기본적으로 messages.properties)을 사용할 수 있을 때 적용됩니다.
> 리소스 번들에 언어별 속성 파일만 포함된 경우 기본값을 추가해야 합니다. 구성된 기본 이름과 일치하는 속성 파일이 없으면 자동 구성된 MessageSource가 없습니다.

리소스 번들의 기본 이름과 기타 여러 속성은 다음 예제와 같이 spring.messages 네임스페이스를 사용하여 구성할 수 있습니다.

```yaml
spring:
  messages:
    basename: "messages,config.i18n.messages"
    fallback-to-system-locale: false
```

> Tip
> 
> spring.messages.basename은 쉼표로 구분된 위치 목록(패키지 한정자 또는 클래스 경로 루트에서 확인된 리소스)을 지원합니다.

지원되는 추가 옵션은 MessageSourceProperties를 참조하십시오.

## 7.6 JSON
Spring Boot는 세 가지 JSON 매핑 라이브러리와의 통합을 제공합니다.
- Gson 
- Jackson 
- JSON-B

Jackson은 선호되는 기본 라이브러리입니다.

### 7.6.1. Jackson
Jackson에 대한 자동 구성이 제공되며 Jackson은 spring-boot-starter-json의 일부입니다.
Jackson이 클래스 경로에 있으면 ObjectMapper 빈이 자동으로 구성됩니다. ObjectMapper의 구성을 사용자 정의하기 위해 여러 구성 특성이 제공됩니다.

### 7.6.2. Gson
Gson에 대한 자동 구성이 제공됩니다. Gson이 클래스 경로에 있으면 Gson 빈이 자동으로 구성됩니다.
구성을 사용자 지정하기 위해 여러 spring.gson.* 구성 속성이 제공됩니다. 더 많은 제어를 위해 하나 이상의 GsonBuilderCustomizer 빈을 사용할 수 있습니다.

### 7.6.3. JSON-B
JSON-B에 대한 자동 구성이 제공됩니다. JSON-B API와 구현이 클래스 경로에 있으면 Jsonb 빈이 자동으로 구성됩니다
기본 JSON-B 구현은 종속성 관리가 제공되는 Apache Johnzon입니다.

## 7.7 작업 실행 및 예약
컨텍스트에 Executor bean이 없으면 Spring Boot는 비동기 작업 실행(@EnableAsync) 및 Spring MVC 비동기 요청 처리에 자동으로 연결될 수 있는 합리적인 기본값으로 ThreadPoolTaskExecutor를 자동 구성합니다.

> Tip
> 
> 컨텍스트에서 사용자 지정 Executor를 정의한 경우 일반 작업 실행(즉, @EnableAsync)은 이를 투명하게 사용하지만 Spring MVC 지원은 AsyncTaskExecutor 구현(applicationTaskExecutor)이 필요하므로 구성되지 않습니다.
> 대상 배열에 따라 Executor를 ThreadPoolTaskExecutor로 변경하거나 사용자 지정 Executor를 래핑하는 ThreadPoolTaskExecutor 및 AsyncConfigurer를 모두 정의할 수 있습니다.
> 
> 자동 구성 TaskExecutorBuilder를 사용하면 자동 구성이 기본적으로 수행하는 작업을 재현하는 인스턴스를 쉽게 만들 수 있습니다.
 
스레드 풀은 로드에 따라 확장 및 축소할 수 있는 8개의 코어 스레드를 사용합니다. 이러한 기본 설정은 다음 예제와 같이 spring.task.execution 네임스페이스를 사용하여 미세 조정할 수 있습니다.

```yaml
spring:
  task:
    execution:
      pool:
        max-size: 16
        queue-capacity: 100
        keep-alive: "10s"
```

이렇게 하면 대기열이 가득 차면(작업 100개) 스레드 풀이 최대 16개의 스레드로 증가하도록 제한된 대기열을 사용하도록 스레드 풀이 변경됩니다.
스레드가 10초(기본적으로 60초가 아님) 동안 유휴 상태일 때 스레드가 회수되므로 풀 축소는 더욱 공격적입니다.

ThreadPoolTaskScheduler는 예약된 작업 실행에 연결해야 하는 경우 자동 구성될 수도 있습니다(예: @EnableScheduling 사용).
스레드 풀은 기본적으로 하나의 스레드를 사용하며 해당 설정은 다음 예제와 같이 spring.task.scheduling 네임스페이스를 사용하여 미세 조정할 수 있습니다.

```yaml
spring:
  task:
    scheduling:
      thread-name-prefix: "scheduling-"
      pool:
        size: 2
```

TaskExecutorBuilder 빈과 TaskSchedulerBuilder 빈은 모두 사용자 지정 실행기 또는 스케줄러를 생성해야 하는 경우 컨텍스트에서 사용할 수 있습니다.

## 7.8 테스트
Spring Boot는 애플리케이션을 테스트할 때 도움이 되는 여러 유틸리티와 주석을 제공합니다. 테스트 지원은 두 가지 모듈로 제공됩니다. spring-boot-test에는 핵심 항목이 포함되어 있고 spring-boot-test-autoconfigure는 테스트를 위한 자동 구성을 지원합니다.

대부분의 개발자는 Spring Boot 테스트 모듈과 JUnit Jupiter, AssertJ, Hamcrest 및 기타 여러 유용한 라이브러리를 모두 가져오는 spring-boot-starter-test "Starter"를 사용합니다.

> Tip
> 
> JUnit 4를 사용하는 테스트가 있는 경우 JUnit 5의 빈티지 엔진을 사용하여 테스트를 실행할 수 있습니다. 빈티지 엔진을 사용하려면 다음 예제와 같이 junit-vintage-engine에 대한 종속성을 추가합니다.

```xml
<dependency>
    <groupId>org.junit.vintage</groupId>
    <artifactId>junit-vintage-engine</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

hamcrest-core는 spring-boot-starter-test의 일부인 org.hamcrest:hamcrest를 위해 제외됩니다.

### 7.8.1. 테스트 범위 종속성
spring-boot-starter-test "Starter"(테스트 범위 내)에는 다음과 같은 라이브러리가 포함되어 있습니다.

- JUnit 5: 단위 테스트 Java 애플리케이션을 위한 사실상의 표준입니다. 
- Spring Test & Spring Boot Test: Spring Boot 애플리케이션을 위한 유틸리티 및 통합 테스트 지원.
- AssertJ: 유창한 어설션 라이브러리입니다. 
- Hamcrest: 매처 객체 라이브러리(제약 조건 또는 조건자라고도 함). 
- Mockito: 자바 모킹 프레임워크. 
- JSONassert: JSON용 어설션 라이브러리입니다. 
- JsonPath: JSON용 XPath.

일반적으로 이러한 공통 라이브러리는 테스트를 작성할 때 유용합니다. 이러한 라이브러리가 필요에 맞지 않으면 자체 테스트 종속성을 추가할 수 있습니다.

### 7.8.2. 스프링 애플리케이션 테스트
종속성 주입의 주요 이점 중 하나는 코드를 단위 테스트하기 쉽게 만들수 있다는 것입니다. Spring을 포함하지 않고도 new 연산자를 사용하여 개체를 인스턴스화할 수 있습니다. 실제 종속성 대신 mock 개체를 사용할 수도 있습니다.

종종 단위 테스트를 넘어 통합 테스트를 시작해야 합니다(Spring ApplicationContext 사용). 애플리케이션을 배포하거나 다른 인프라에 연결할 필요 없이 통합 테스트를 수행할 수 있으면 유용합니다.

Spring Framework에는 이러한 통합 테스트를 위한 전용 테스트 모듈이 포함되어 있습니다. org.springframework:spring-test에 직접 종속성을 선언하거나 spring-boot-starter-test "Starter"를 사용하여 전이적으로 가져올 수 있습니다.

이전에 spring-test 모듈을 사용해 본 적이 없다면 먼저 Spring Framework 참조 문서의 관련 섹션을 읽어야 합니다.

### 7.8.3. 스프링 부트 애플리케이션 테스트
Spring Boot 애플리케이션은 Spring ApplicationContext이므로 바닐라 Spring 컨텍스트로 일반적으로 수행하는 것 이상으로 테스트하기 위해 특별히 수행할 작업은 없습니다.

> Note
> 
> Spring Boot의 외부 속성, 로깅 및 기타 기능은 SpringApplication을 사용하여 생성하는 경우에만 기본적으로 컨텍스트에 설치됩니다.

Spring Boot는 @SpringBootTest 어노테이션을 제공하며 Spring Boot 기능이 필요할 때 표준 스프링 테스트 @ContextConfiguration 어노테이션의 대안으로 사용할 수 있습니다.
어노테이션은 SpringApplication을 통해 테스트에 사용되는 ApplicationContext를 생성하여 작동합니다. @SpringBootTest 외에도 응용 프로그램의 보다 구체적인 조각을 테스트하기 위해 여러 다른 주석도 제공됩니다.

> Tip
> 
> JUnit 4를 사용하는 경우 테스트에 @RunWith(SpringRunner.class)도 추가하는 것을 잊지 마십시오. 그렇지 않으면 주석이 무시됩니다
> JUnit 5를 사용하면 @ExtendWith(SpringExtension.class) as @SpringBootTest 및 기타 @… Test 어노테이션은 필요가 없습니다.

기본적으로 @SpringBootTest는 서버를 시작하지 않습니다. @SpringBootTest의 webEnvironment 속성을 사용하여 테스트 실행 방법을 더 세분화할 수 있습니다.

- MOCK(Default) : 웹 ApplicationContext를 로드하고 모의 웹 환경을 제공한다. 이 어노테이션을 사용하면 임베디드 서버가 시작되지 않습니다. 클래스 경로에서 웹 환경을 사용할 수 없는 경우 이 모드는 웹이 아닌 일반 ApplicationContext를 만드는 것으로 투명하게 대체됩니다. 웹 애플리케이션의 모의 기반 테스트를 위해 @AutoConfigureMockMvc 또는 @AutoConfigureWebTestClient와 함께 사용할 수 있습니다.
- RANDOM_PORT: WebServerApplicationContext를 로드하고 실제 웹 환경을 제공합니다. 임베디드 서버가 시작되고 임의의 포트에서 청취합니다.
- DEFINED_PORT: WebServerApplicationContext를 로드하고 실제 웹 환경을 제공합니다. 내장형 서버가 시작되고 정의된 포트(application.properties에서) 또는 기본 포트 8080에서 수신 대기합니다.
- NONE: SpringApplication을 사용하여 ApplicationContext를 로드하지만 웹 환경(mock 또는 기타)을 제공하지 않습니다.

> Note
> 
> 테스트가 @Transactional이면 기본적으로 각 테스트 메서드가 끝날 때 트랜잭션을 롤백합니다
> 그러나 RANDOM_PORT 또는 DEFINED_PORT와 함께 이 배열을 사용하면 암시적으로 실제 서블릿 환경을 제공하므로 HTTP 클라이언트와 서버는 별도의 스레드에서 실행되므로 별도의 트랜잭션에서 실행됩니다.이 경우 서버에서 시작된 트랜잭션은 롤백되지 않습니다.

> Note
> 
> @SpringBootTest with webEnvironment = WebEnvironment.RANDOM_PORT는 또한 애플리케이션이 관리 서버에 대해 다른 포트를 사용하는 경우 별도의 임의 포트에서 관리 서버를 시작합니다.

### 웹 애플리케이션 유형 감지
Spring MVC를 사용할 수 있는 경우 일반 MVC 기반 애플리케이션 컨텍스트가 구성됩니다. Spring WebFlux만 있는 경우 이를 감지하고 대신 WebFlux 기반 애플리케이션 컨텍스트를 구성합니다.

둘 다 있으면 Spring MVC가 우선합니다. 이 시나리오에서 반응형 웹 애플리케이션을 테스트하려면 spring.main.web-application-type 속성을 설정해야 합니다.

```java
@SpringBootTest(properties = "spring.main.web-application-type=reactive")
class MyWebFluxTests {

    // ...

}
```

### 테스트 구성 감지
Spring 테스트 프레임워크에 익숙하다면 로드할 Spring @Configuration을 지정하기 위해 @ContextConfiguration(classes=… )를 사용하는 데 익숙할 수 있습니다.
테스트 내에서 중첩된 @Configuration 클래스를 자주 사용했을 수 있습니다.

Spring Boot 애플리케이션을 테스트할 때 이는 종종 필요하지 않습니다. Spring Boot의 @*Test 어노테이션은 기본 구성을 명시적으로 정의하지 않을 때마다 자동으로 기본 구성을 검색합니다.

검색 알고리즘은 @SpringBootApplication 또는 @SpringBootConfiguration 어노테이션이 달린 클래스를 찾을 때까지 테스트를 포함하는 패키지에서 작동합니다.
코드를 합리적인 방식으로 구조화했다면 일반적으로 기본 구성을 찾을 수 있습니다.

> Note
> 
> 테스트 주석을 사용하여 애플리케이션의 보다 구체적인 부분을 테스트하는 경우 기본 메서드의 애플리케이션 클래스에서 특정 영역에 특정한 구성 설정을 추가하지 않아야 합니다.
> 
> @SpringBootApplication의 기본 구성 요소 스캔 구성은 슬라이싱이 예상대로 작동하는지 확인하는 데 사용되는 제외 필터를 정의합니다.
> @SpringBootApplication 주석 클래스에서 명시적인 @ComponentScan 지시문을 사용하는 경우 해당 필터가 비활성화된다는 점에 유의하십시오. 슬라이싱을 사용하는 경우 다시 정의해야 합니다.

기본 구성을 사용자 정의하려면 중첩된 @TestConfiguration 클래스를 사용할 수 있습니다.
애플리케이션의 기본 구성 대신 사용되는 중첩된 @Configuration 클래스와 달리 중첩된 @TestConfiguration 클래스는 애플리케이션의 기본 구성 외에도 사용됩니다.

> Note
> 
> Spring의 테스트 프레임워크는 테스트 간에 애플리케이션 컨텍스트를 캐시합니다. 따라서 테스트가 동일한 구성을 공유하는 한(검색 방법에 관계없이) 잠재적으로 시간이 많이 소요되는 컨텍스트 로드 프로세스는 한 번만 발생합니다.

### 테스트 구성 제외
애플리케이션이 컴포넌트 스캔을 사용하는 경우(예: @SpringBootApplication 또는 @ComponentScan을 사용하는 경우) 특정 테스트에 대해서만 생성한 최상위 구성 클래스가 실수로 모든 곳에서 선택되는 것을 볼 수 있습니다.

앞에서 본 것처럼 @TestConfiguration은 기본 구성을 사용자 지정하기 위해 테스트의 내부 클래스에서 사용할 수 있습니다.
최상위 클래스에 배치될 때 @TestConfiguration은 src/test/java의 클래스가 스캔으로 선택되지 않아야 함을 나타냅니다. 그런 다음 다음 예제와 같이 필요한 위치에서 해당 클래스를 명시적으로 가져올 수 있습니다.

```java
@SpringBootTest
@Import(MyTestsConfiguration.class)
class MyTests {

    @Test
    void exampleTest() {
        // ...
    }

}
```

> Note
> 
> @ComponentScan을 직접 사용한다면(즉, @SpringBootApplication을 통하지 않고) TypeExcludeFilter를 등록해야 합니다. 자세한 내용은 Javadoc을 참조하십시오.
 
### 애플리케이션 인수 사용
애플리케이션이 인수를 예상하는 경우 @SpringBootTest에서 args 속성을 사용하여 인수를 주입하도록 할 수 있습니다.
```java
@SpringBootTest(args = "--app.test=one")
class MyApplicationArgumentTests {

    @Test
    void applicationArgumentsPopulated(@Autowired ApplicationArguments args) {
        assertThat(args.getOptionNames()).containsOnly("app.test");
        assertThat(args.getOptionValues("app.test")).containsOnly("one");
    }

}
```

### mock환경으로 테스트
기본적으로 @SpringBootTest는 서버를 시작하지 않고 대신 웹 엔드포인트 테스트를 위한 모의 환경을 설정합니다

Spring MVC를 사용하면 다음 예제와 같이 MockMvc 또는 WebTestClient를 사용하여 웹 엔드포인트를 쿼리할 수 있습니다.

```java
@SpringBootTest
@AutoConfigureMockMvc
class MyMockMvcTests {

    @Test
    void testWithMockMvc(@Autowired MockMvc mvc) throws Exception {
        mvc.perform(get("/")).andExpect(status().isOk()).andExpect(content().string("Hello World"));
    }

    // If Spring WebFlux is on the classpath, you can drive MVC tests with a WebTestClient
    @Test
    void testWithWebTestClient(@Autowired WebTestClient webClient) {
        webClient
                .get().uri("/")
                .exchange()
                .expectStatus().isOk()
                .expectBody(String.class).isEqualTo("Hello World");
    }

}
```

> Tip
> 
> 웹 레이어에만 집중하고 완전한 ApplicationContext를 시작하지 않으려면 대신 @WebMvcTest를 사용하는 것이 좋습니다.

Spring WebFlux 끝점을 사용하면 다음 예제와 같이 WebTestClient를 사용할 수 있습니다.

```java
@SpringBootTest
@AutoConfigureWebTestClient
class MyMockWebTestClientTests {

    @Test
    void exampleTest(@Autowired WebTestClient webClient) {
        webClient
            .get().uri("/")
            .exchange()
            .expectStatus().isOk()
            .expectBody(String.class).isEqualTo("Hello World");
    }

}
```

> Tip
> 
> 모의 환경 내에서 테스트하는 것이 일반적으로 전체 서블릿 컨테이너로 실행하는 것보다 빠릅니다.
> 그러나 모킹은 Spring MVC 레이어에서 발생하기 때문에 하위 수준의 서블릿 컨테이너 동작에 의존하는 코드는 MockMvc로 직접 테스트할 수 없습니다.
> 
> 예를 들어 Spring Boot의 오류 처리는 서블릿 컨테이너에서 제공하는 "error page" 지원을 기반으로 합니다.
> 즉, MVC 레이어가 예상대로 예외를 발생시키고 처리하는지 테스트할 수 있지만 특정 사용자 지정 오류 페이지가 렌더링되는지 직접 테스트할 수는 없습니다.
> 이러한 하위 수준 문제를 테스트해야 하는 경우 다음 섹션에 설명된 대로 완전히 실행 중인 서버를 시작할 수 있습니다.
 
### 실행 중인 서버로 테스트
전체 실행 서버를 시작해야 하는 경우 임의 포트를 사용하는 것이 좋습니다. @SpringBootTest(webEnvironment=WebEnvironment.RANDOM_PORT)를 사용하는 경우 테스트가 실행될 때마다 사용 가능한 포트가 무작위로 선택됩니다.

@LocalServerPort 어노테이션은 테스트에 사용되는 실제 포트를 주입하는 데 사용할 수 있습니다.
편의를 위해 시작된 서버에 REST 호출을 해야 하는 테스트는 WebTestClient를 추가로 @Autowire할 수 있습니다. WebTestClient는 다음 예제와 같이 실행 중인 서버에 대한 상대 링크를 확인하고 응답 확인을 위한 전용 API와 함께 제공됩니다.

```java
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
class MyRandomPortWebTestClientTests {

   @Test
   void exampleTest(@Autowired WebTestClient webClient) {
      webClient
              .get().uri("/")
              .exchange()
              .expectStatus().isOk()
              .expectBody(String.class).isEqualTo("Hello World");
   }

}
```

> Tip
> 
> WebTestClient는 라이브 서버와 모의 환경 모두에 사용할 수 있습니다.

이 설정에는 클래스 경로에 spring-webflux가 필요합니다. webflux를 추가할 수 없거나 추가하지 않을 경우 Spring Boot는 TestRestTemplate 기능도 제공합니다.

```java
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
class MyRandomPortTestRestTemplateTests {

    @Test
    void exampleTest(@Autowired TestRestTemplate restTemplate) {
        String body = restTemplate.getForObject("/", String.class);
        assertThat(body).isEqualTo("Hello World");
    }

}
```

### WebTestClient 사용자 지정
WebTestClient Bean을 사용자 정의하려면 WebTestClientBuilderCustomizer Bean을 구성하십시오. 이러한 빈은 WebTestClient를 생성하는 데 사용되는 WebTestClient.Builder로 호출됩니다

### JMX 사용하기
테스트 컨텍스트 프레임워크가 컨텍스트를 캐시하므로 동일한 구성 요소가 동일한 도메인에 등록되는 것을 방지하기 위해 JMX는 기본적으로 비활성화되어 있습니다. 이러한 테스트가 MBeanServer에 액세스해야 하는 경우 더티 표시도 고려하십시오.
```java
@ExtendWith(SpringExtension.class)
@SpringBootTest(properties = "spring.jmx.enabled=true")
@DirtiesContext
class MyJmxTests {

    @Autowired
    private MBeanServer mBeanServer;

    @Test
    void exampleTest() {
        assertThat(this.mBeanServer.getDomains()).contains("java.lang");
        // ...
    }

}
```

### 지표 사용
클래스 경로에 관계없이 메모리 지원을 제외한 측정기 레지스트리는 @SpringBootTest를 사용할 때 자동 구성되지 않습니다.

통합 테스트의 일부로 메트릭을 다른 백엔드로 내보내야 하는 경우 @AutoConfigureMetrics로 주석을 추가합니다.

### Mocking and Spying Beans
테스트를 실행할 때 애플리케이션 컨텍스트 내에서 특정 구성 요소를 mocking해야 하는 경우가 있습니다.
예를 들어, 개발 중에 사용할 수 없는 일부 원격 서비스에 대한 퍼사드가 있을 수 있습니다. mock은 실제 환경에서 트리거하기 어려울 수 있는 오류를 시뮬레이션하려는 경우에도 유용할 수 있습니다

Spring Boot에는 ApplicationContext 내부의 빈에 대한 Mockito mock를 정의하는 데 사용할 수 있는 @MockBean 어노테이션이 포함되어 있습니다. 어노테이션을 사용하여 새 bean을 추가하거나 단일 기존 bean 정의를 바꿀 수 있습니다.
어노테이션은 테스트 클래스, 테스트 내의 필드 또는 @Configuration 클래스 및 필드에서 직접 사용할 수 있습니다. 필드에서 사용하면 생성된 모의 인스턴스도 주입됩니다.mock bean은 각 테스트 방법 후에 자동으로 재설정됩니다.

> Note
> 
> 테스트에서 Spring Boot의 테스트 주석(예: @SpringBootTest) 중 하나를 사용하는 경우 이 기능이 자동으로 활성화됩니다. 다른 배열로 이 기능을 사용하려면 다음 예와 같이 리스너를 명시적으로 추가해야 합니다.

```java
@ContextConfiguration(classes = MyConfig.class)
@TestExecutionListeners({ MockitoTestExecutionListener.class, ResetMocksTestExecutionListener.class })
class MyTests {

    // ...

}
```

다음 예제는 기존 RemoteService 빈을 모의 구현으로 바꿉니다.

```java
@SpringBootTest
class MyTests {

    @Autowired
    private Reverser reverser;

    @MockBean
    private RemoteService remoteService;

    @Test
    void exampleTest() {
        given(this.remoteService.getValue()).willReturn("spring");
        String reverse = this.reverser.getReverseValue(); // Calls injected RemoteService
        assertThat(reverse).isEqualTo("gnirps");
    }

}
```

> Note
> 
> @MockBean은 애플리케이션 컨텍스트 새로 고침 중에 실행되는 빈의 동작을 mock하는 데 사용할 수 없습니다. 테스트가 실행될 때까지 애플리케이션 컨텍스트 새로고침이 완료되고 모의 동작을 구성하기에는 너무 늦었습니다.
> 이 상황에서 @Bean 메서드를 사용하여 모의 객체를 만들고 구성하는 것이 좋습니다.

또한 @SpyBean을 사용하여 기존 빈을 Mockito 스파이로 래핑할 수 있습니다. 자세한 내용은 Javadoc을 참조하십시오.

> Note
> 
> 범위가 지정된 bean에 대해 생성된 것과 같은 CGLib 프록시는 프록시된 메서드를 최종으로 선언합니다. 이렇게 하면 기본 구성에서 최종 메서드를 모의하거나 감시할 수 없으므로 Mockito가 올바르게 작동하지 않습니다.
> 이러한 bean을 모의하거나 감시하려면 애플리케이션의 테스트 종속성에 org.mockito:mockito-inline을 추가하여 인라인 모의 작성기를 사용하도록 Mockito를 구성하십시오. 이를 통해 Mockito는 최종 메서드를 mock하고 감시할 수 있습니다.

> Note
> 
> Spring의 테스트 프레임워크는 테스트 간에 애플리케이션 컨텍스트를 캐싱하고 동일한 구성을 공유하는 테스트를 위해 컨텍스트를 재사용하지만 @MockBean 또는 @SpyBean을 사용하면 캐시 키에 영향을 미치므로 컨텍스트 수가 증가할 가능성이 높습니다.

> Tip
> 
> @SpyBean을 사용하여 이름으로 매개변수를 참조하는 @Cacheable 메서드로 빈을 감시하는 경우 애플리케이션을 -parameters로 컴파일해야 합니다.
> 이렇게 하면 bean이 염탐되면 캐싱 인프라에서 매개변수 이름을 사용할 수 있습니다.

> Tip
> 
> @SpyBean을 사용하여 Spring에 의해 프록시되는 bean을 감시하는 경우 주어진 상황 또는 when을 사용하여 기대치를 설정할 때와 같은 일부 상황에서 Spring의 프록시를 제거해야 할 수 있습니다. 그렇게 하려면 AopTestUtils.getTargetObject(yourProxiedSpy)를 사용하십시오

### 자동 구성된 테스트
Spring Boot의 자동 구성 시스템은 애플리케이션에 잘 작동하지만 때때로 테스트에는 너무 많을 수 있습니다.
애플리케이션의 "조각"을 테스트하는 데 필요한 구성 부분만 로드하는 것이 도움이 되는 경우가 많습니다.
예를 들어 Spring MVC 컨트롤러가 URL을 올바르게 매핑하고 있는지 테스트하고 이러한 테스트에 데이터베이스 호출을 포함하고 싶지 않거나 또는 관심없는 웹 레이어 테스트 말고 JPA 엔터티를 테스트하고 싶을 수 있다.

spring-boot-test-autoconfigure 모듈에는 이러한 "슬라이스"를 자동으로 구성하는 데 사용할 수 있는 여러 어노테이션이 포함되어 있습니다.
각각은 비슷한 방식으로 작동하여 ApplicationContext를 로드하는 @… Test 주석과 자동 구성 설정을 사용자 지정하는 데 사용할 수 있는 하나 이상의 @AutoConfigure… 어노테이션을 제공합니다.

> Note
> 
> 각 슬라이스는 구성 요소 스캔을 적절한 구성 요소로 제한하고 매우 제한된 자동 구성 클래스 집합을 로드합니다.
> 그 중 하나를 제외해야 하는 경우 대부분의 @… Test 주석은 excludeAutoConfiguration 속성을 제공합니다. 또는 @ImportAutoConfiguration#exclude를 사용할 수 있습니다.

> Note
> 
> 하나의 테스트에서 여러 @… 테스트 주석을 사용하여 여러 "슬라이스"를 포함하는 것은 지원되지 않습니다. 여러 "슬라이스"가 필요한 경우 @… Test 주석 중 하나를 선택하고 다른 "슬라이스"의 @AutoConfigure... 주석을 직접 포함합니다.

> Tip
> 
> 표준 @SpringBootTest 주석과 함께 @AutoConfigure… 주석을 사용하는 것도 가능합니다. 애플리케이션을 "슬라이싱"하는 데는 관심이 없지만 일부 자동 구성된 테스트 빈을 원하는 경우 이 조합을 사용할 수 있습니다.
 
### 자동 구성된 JSON 테스트
개체 JSON 직렬화 및 역직렬화가 예상대로 작동하는지 테스트하려면 @JsonTest 주석을 사용할 수 있습니다.
@JsonTest는 다음 라이브러리 중 하나일 수 있는 사용 가능한 지원 JSON 매퍼를 자동 구성합니다.
- Jackson ObjectMapper, 모든 @JsonComponent 빈 및 모든 Jackson 모듈
- Gson
- Jsonb

> Tip
> 
> @JsonTest에 의해 활성화된 자동 구성 목록은 부록에서 찾을 수 있습니다

자동 구성 요소를 구성해야 하는 경우 @AutoConfigureJsonTesters 주석을 사용할 수 있습니다.

Spring Boot에는 JSONAssert 및 JsonPath 라이브러리와 함께 작동하여 JSON이 예상대로 표시되는지 확인하는 AssertJ 기반 도우미가 포함되어 있습니다.
JacksonTester, GsonTester, JsonbTester 및 BasicJsonTester 클래스는 각각 Jackson, Gson, Jsonb 및 Strings에 사용할 수 있습니다.
테스트 클래스의 모든 도우미 필드는 @JsonTest를 사용할 때 @Autowired가 될 수 있습니다. 다음 예는 Jackson의 테스트 클래스를 보여줍니다.

```java
@JsonTest
class MyJsonTests {

    @Autowired
    private JacksonTester<VehicleDetails> json;

    @Test
    void serialize() throws Exception {
        VehicleDetails details = new VehicleDetails("Honda", "Civic");
        // Assert against a `.json` file in the same package as the test
        assertThat(this.json.write(details)).isEqualToJson("expected.json");
        // Or use JSON path based assertions
        assertThat(this.json.write(details)).hasJsonPathStringValue("@.make");
        assertThat(this.json.write(details)).extractingJsonPathStringValue("@.make").isEqualTo("Honda");
    }

    @Test
    void deserialize() throws Exception {
        String content = "{\"make\":\"Ford\",\"model\":\"Focus\"}";
        assertThat(this.json.parse(content)).isEqualTo(new VehicleDetails("Ford", "Focus"));
        assertThat(this.json.parseObject(content).getMake()).isEqualTo("Ford");
    }

}
```

> Note
> 
> JSON 도우미 클래스는 표준 단위 테스트에서 직접 사용할 수도 있습니다. 이렇게 하려면 @JsonTest를 사용하지 않는 경우 @Before 메서드에서 헬퍼의 initFields 메서드를 호출합니다.

Spring Boot의 AssertJ 기반 헬퍼를 사용하여 주어진 JSON 경로에서 숫자 값을 어설션하는 경우 유형에 따라 isEqualTo를 사용하지 못할 수 있습니다. 대신 AssertJ의 만족을 사용하여 값이 주어진 조건과 일치하는지 확인할 수 있습니다.
예를 들어 다음 예제에서는 실제 숫자가 오프셋 0.01 내에서 0.15에 가까운 부동 소수점 값이라고 주장합니다.

```java
@Test
void someTest() throws Exception {
    SomeObject value = new SomeObject(0.152f);
    assertThat(this.json.write(value)).extractingJsonPathNumberValue("@.test.numberValue")
            .satisfies((number) -> assertThat(number.floatValue()).isCloseTo(0.15f, within(0.01f)));
}
```

### 자동 구성된 Spring MVC 테스트
Spring MVC 컨트롤러가 예상대로 작동하는지 테스트하려면 @WebMvcTest 주석을 사용하십시오.
@WebMvcTest는 Spring MVC 인프라를 자동 구성하고 스캔된 빈을 @Controller, @ControllerAdvice, @JsonComponent, Converter, GenericConverter, Filter, HandlerInterceptor, WebMvcConfigurer, WebMvcRegistrations 및 HandlerMethodArgumentResolver로 제한합니다.
일반 @Component 및 @ConfigurationProperties 빈은 @WebMvcTest 주석이 사용될 때 스캔되지 않습니다. @EnableConfigurationProperties는 @ConfigurationProperties 빈을 포함하는 데 사용할 수 있습니다.

> Tip
> 
> @WebMvcTest에 의해 활성화된 자동 구성 설정 목록은 부록에서 찾을 수 있습니다.
 
> Tip
> 
> Jackson 모듈과 같은 추가 구성 요소를 등록해야 하는 경우 테스트에서 @Import를 사용하여 추가 구성 클래스를 가져올 수 있습니다.

종종 @WebMvcTest는 단일 컨트롤러로 제한되며 @MockBean과 함께 사용되어 필요한 협력자에게 mock 구현을 제공합니다.

@WebMvcTest는 MockMvc도 자동 구성합니다. Mock MVC는 전체 HTTP 서버를 시작할 필요 없이 MVC 컨트롤러를 빠르게 테스트할 수 있는 강력한 방법을 제공합니다.

> Tip
> 
> 또한 @AutoConfigureMockMvc로 어노테이션을 달아 @WebMvcTest가 아닌(예: @SpringBootTest)에서 MockMvc를 자동 구성할 수 있습니다. 다음 예제에서는 MockMvc를 사용합니다.

```java
@WebMvcTest(UserVehicleController.class)
class MyControllerTests {

    @Autowired
    private MockMvc mvc;

    @MockBean
    private UserVehicleService userVehicleService;

    @Test
    void testExample() throws Exception {
        given(this.userVehicleService.getVehicleDetails("sboot"))
            .willReturn(new VehicleDetails("Honda", "Civic"));
        this.mvc.perform(get("/sboot/vehicle").accept(MediaType.TEXT_PLAIN))
            .andExpect(status().isOk())
            .andExpect(content().string("Honda Civic"));
    }

}
```

> Note
> 
> 기본적으로 Spring Boot는 각 테스트 후에 드라이버가 종료되고 새 인스턴스가 주입되도록 WebDriver 빈을 특별한 "scope"에 넣습니다
> 이 동작을 원하지 않으면 WebDriver @Bean 정의에 @Scope("singleton")를 추가할 수 있습니다.

> Warning
> 
> Spring Boot에 의해 생성된 webDriver 범위는 동일한 이름의 사용자 정의 범위를 대체합니다. 자신의 webDriver 범위를 정의하면 @WebMvcTest를 사용할 때 작동이 중지될 수 있습니다.

클래스 경로에 Spring Security가 있는 경우 @WebMvcTest는 WebSecurityConfigurer 빈도 스캔합니다.
이러한 테스트에 대해 보안을 완전히 비활성화하는 대신 Spring Security의 테스트 지원을 사용할 수 있습니다. Spring Security의 MockMvc 지원을 사용하는 방법에 대한 자세한 내용은 이 Spring Security를 사용한 테스트 방법 섹션에서 찾을 수 있습니다.

> Tip
> 
> 때때로 Spring MVC 테스트를 작성하는 것만으로는 충분하지 않습니다. Spring Boot는 실제 서버에서 완전한 end-to-end 테스트를 실행할 수 있도록 도와줍니다.
 
### 자동 구성된 Spring WebFlux 테스트
Spring WebFlux 컨트롤러가 예상대로 작동하는지 테스트하려면 @WebFluxTest 주석을 사용할 수 있습니다.
@WebFluxTest는 Spring WebFlux 인프라를 자동 구성하고 스캔된 빈을 @Controller, @ControllerAdvice, @JsonComponent, Converter, GenericConverter, WebFilter 및 WebFluxConfigurer로 제한합니다.
일반 @Component 및 @ConfigurationProperties 빈은 @WebFluxTest 주석이 사용될 때 스캔되지 않습니다. @EnableConfigurationProperties는 @ConfigurationProperties 빈을 포함하는 데 사용할 수 있습니다.

> Tip
> 
> @WebFluxTest에 의해 활성화된 자동 구성 목록은 부록에서 찾을 수 있습니다.

> Tip
> 
> Jackson 모듈과 같은 추가 구성 요소를 등록해야 하는 경우 테스트에서 @Import를 사용하여 추가 구성 클래스를 가져올 수 있습니다.

종종 @WebFluxTest는 단일 컨트롤러로 제한되며 @MockBean 주석과 함께 사용되어 필요한 협력자에게 mock 구현을 제공합니다.

@WebFluxTest는 또한 WebTestClient를 자동 구성하여 전체 HTTP 서비스를 시작할 필요 없이 WebFlux 컨트롤러를 빠르게 테스트할 수 있는 강력한 방법을 제공합니다.
@AutoConfigureWebTestClient로 주석을 달아 @WebFluxTest가 아닌(예: @SpringBootTest)에서 WebTestClient를 자동 구성할 수도 있습니다. 다음 예제는 @WebFluxTest와 WebTestClient를 모두 사용하는 클래스를 보여줍니다.

> Tip
> 
> 또한 @AutoConfigureWebTestClient로 주석을 달아 @WebFluxTest가 아닌(예: @SpringBootTest)에서 WebTestClient를 자동 구성할 수 있습니다. 다음 예제는 @WebFluxTest와 WebTestClient를 모두 사용하는 클래스를 보여줍니다.
 
```java
@WebFluxTest(UserVehicleController.class)
class MyControllerTests {

    @Autowired
    private WebTestClient webClient;

    @MockBean
    private UserVehicleService userVehicleService;

    @Test
    void testExample() {
        given(this.userVehicleService.getVehicleDetails("sboot"))
            .willReturn(new VehicleDetails("Honda", "Civic"));
        this.webClient.get().uri("/sboot/vehicle").accept(MediaType.TEXT_PLAIN).exchange()
            .expectStatus().isOk()
            .expectBody(String.class).isEqualTo("Honda Civic");
    }

}
```

> Tip
> 
> 모의 웹 애플리케이션에서 WebTestClient를 사용하면 현재 WebFlux에서만 작동하므로 이 설정은 WebFlux 애플리케이션에서만 지원됩니다.

> Note
> 
> @WebFluxTest는 기능적 웹 프레임워크를 통해 등록된 경로를 감지할 수 없습니다. 컨텍스트에서 RouterFunction 빈을 테스트하려면 @Import를 사용하거나 @SpringBootTest를 사용하여 RouterFunction을 직접 가져오는 것을 고려하십시오.

> Note
> 
> @WebFluxTest는 SecurityWebFilterChain 유형의 @Bean으로 등록된 사용자 정의 보안 구성을 감지할 수 없습니다. 이를 테스트에 포함하려면 @Import 또는 @SpringBootTest를 사용하여 빈을 등록하는 구성을 가져와야 합니다.

> Tip
> 
> 때때로 Spring WebFlux 테스트를 작성하는 것만으로는 충분하지 않습니다. Spring Boot는 실제 서버에서 완전한 end-to-end 테스트를 실행할 수 있도록 도와줍니다.