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
이렇게 하려면 디버그 속성을 활성화하거나 `org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener`에 대한 DEBUG 로깅을 활성화해야 합니다

예를 들어 java -jar를 사용하여 애플리케이션을 실행하는 경우 다음과 같이 디버그 속성을 활성화할 수 있습니다.

```shell
java -jar myproject-0.0.1-SNAPSHOT.jar --debug
```

### 7.1.2. 지연 초기화
SpringApplication을 사용하면 애플리케이션을 느리게 초기화할 수 있습니다. 지연 초기화가 활성화되면 응용 프로그램 시작 중에가 아니라 필요할 때 Bean이 생성됩니다.
결과적으로 지연 초기화를 활성화하면 애플리케이션을 시작하는 데 걸리는 시간을 줄일 수 있습니다. 웹 애플리케이션에서 지연 초기화를 활성화하면 HTTP 요청이 수신될 때까지 많은 웹 관련 Bean이 초기화되지 않습니다.

지연 초기화의 단점은 응용 프로그램의 문제 발견을 지연시킬 수 있다는 것입니다. 잘못 구성된 빈이 느리게 초기화되면 시작 중에 더 이상 오류가 발생하지 않으며 빈이 초기화될 때만 문제가 분명해집니다.
JVM이 시작 중에 초기화되는 빈뿐만 아니라 애플리케이션의 모든 빈을 수용할 수 있는 충분한 메모리를 갖도록 주의를 기울여야 합니다.
이러한 이유로 지연 초기화는 기본적으로 활성화되어 있지 않으며 지연 초기화를 활성화하기 전에 JVM의 힙 크기를 미세 조정하는 것이 좋습니다.

지연 초기화는 SpringApplicationBuilder의 lazyInitialization 메서드 또는 SpringApplication의 setLazyInitialization 메서드를 사용하여 프로그래밍 방식으로 활성화할 수 있습니다.
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
파일에 UTF-8 이외의 인코딩이 있는 경우 `spring.banner.charset`을 설정할 수 있습니다. 텍스트 파일 외에 `banner.gif`, `banner.jpg` 또는 `banner.png` 이미지 파일을 클래스 경로에 추가하거나 `spring.banner.image.location` 속성을 설정할 수도 있습니다.
이미지는 ASCII 아트 표현으로 변환되어 텍스트 배너 위에 인쇄됩니다.

`banner.txt` 파일 내에서 환경에서 사용 가능한 모든 키와 다음과 같은 표시자를 사용할 수 있습니다

테이블 4. 배너 변수

| 변수 명                      |설명|
|---------------------------|---------|
| ${application.version}    |MANIFEST.MF에 선언된 애플리케이션의 버전 번호입니다. 예를 들어 Implementation-Version: 1.0은 1.0으로 인쇄됩니다.|
|${application.formatted-version}|MANIFEST.MF에 선언되고 표시 형식이 지정된 애플리케이션의 버전 번호(대괄호로 둘러싸이고 접두어 v가 붙음). 예를 들어(v1.0)|
|${spring-boot.version}|사용 중인 Spring Boot 버전입니다. 예를 들어 2.6.11-SNAPSHOT.|
|${spring-boot.formatted-version}|사용 중인 Spring Boot 버전으로, 표시용으로 형식이 지정되어 있습니다(대괄호로 둘러싸이고 접두어는 v임). 예를 들어(v2.6.11-SNAPSHOT).|
|${Ansi.NAME} (or ${AnsiColor.NAME}, ${AnsiBackground.NAME}, ${AnsiStyle.NAME})|여기서 NAME은 ANSI 이스케이프 코드의 이름입니다. 자세한 내용은 AnsiPropertySource를 참조하십시오.|
|${application.title}|MANIFEST.MF에 선언된 애플리케이션의 제목입니다. 예를 들어 구현-제목: MyApp은 MyApp으로 인쇄됩니다.|

> Tip
> 
> 프로그래밍 방식으로 배너를 생성하려는 경우 SpringApplication.setBanner(… ) 메서드를 사용할 수 있습니다. `org.springframework.boot.Banner` 인터페이스를 사용하고 고유한 printBanner() 메소드를 구현하십시오.

또한 `spring.main.banner-mode` 속성을 사용하여 배너를 `System.out(콘솔)`에 인쇄해야 하는지, 구성된 `로거(log)`로 보내야 하는지 또는 전혀 생성하지 않아야 하는지(off)를 결정할 수 있습니다

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
> SpringApplication에 전달된 생성자 인수는 Spring Bean의 구성 소스입니다. 대부분의 경우 이들은 @Configuration 클래스에 대한 참조이지만 @Component 클래스에 대한 직접 참조일 수도 있습니다.

application.properties 파일을 사용하여 SpringApplication을 구성하는 것도 가능합니다. 자세한 내용은 외부화된 구성을 참조하세요.

구성 옵션의 전체 목록은 SpringApplication Javadoc을 참조하세요.

### 7.1.5. Fluent Builder API
`ApplicationContext` 계층(부모/자식 관계가 있는 여러 컨텍스트)을 빌드해야 하거나 "fluent" 빌더 API 사용을 선호하는 경우 `SpringApplicationBuilder`를 사용할 수 있습니다.

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

Spring Boot 애플리케이션의 내부 상태는 대부분 Spring ApplicationContext로 표현됩니다. 애플리케이션 컨텍스트가 성공적으로 시작된 경우 Spring Boot는 애플리케이션이 유효한 상태에 있다고 가정합니다.
애플리케이션은 컨텍스트가 새로 고쳐지는 즉시 활성 상태로 간주됩니다. Spring Boot 애플리케이션 수명 주기 및 관련 애플리케이션 이벤트를 참조하세요.

### Readiness 상태
애플리케이션의 "Readiness" 상태는 애플리케이션이 트래픽을 처리할 준비가 되었는지 여부를 알려줍니다. 실패한 "Readiness" 상태는 플랫폼에 현재 애플리케이션으로 트래픽을 라우팅하지 않아야 함을 알려줍니다.
이것은 일반적으로 시작하는 동안, CommandLineRunner 및 ApplicationRunner 구성 요소가 처리되는 동안 또는 응용 프로그램이 추가 트래픽을 처리하기에는 너무 바쁘다고 판단하는 경우 언제든지 발생합니다.

애플리케이션 및 명령줄 실행기가 호출되는 즉시 애플리케이션이 준비된 것으로 간주됩니다. Spring Boot 애플리케이션 수명 주기 및 관련 애플리케이션 이벤트를 참조하세요.

> Tip
> 
> 시작 중에 실행될 것으로 예상되는 작업은 @PostConstruct와 같은 Spring 구성 요소 수명 주기 콜백을 사용하는 대신 CommandLineRunner 및 ApplicationRunner 구성 요소에 의해 실행되어야 합니다.

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
        }
        catch (CacheCompletelyBrokenException ex) {
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
3. `ApplicationContextInitializedEvent`는 ApplicationContext가 준비되고 ApplicationContextInitializers가 호출되었지만 빈 정의가 로드되기 전에 전송됩니다.
4. `ApplicationPreparedEvent`는 새로 고침이 시작되기 직전에 bean 정의가 로드된 후에 전송됩니다.
5. `ApplicationStartedEvent`는 컨텍스트가 새로 고쳐진 후 애플리케이션 및 명령줄 실행기가 호출되기 전에 전송됩니다.
6. `LivenessState.CORRECT`와 함께 바로 다음에 `AvailabilityChangeEvent`가 전송되어 애플리케이션이 라이브로 간주되었음을 나타냅니다.
7. `ApplicationReadyEvent`는 응용 프로그램 및 명령줄 실행기가 호출된 후에 전송됩니다.
8. Application이 요청을 처리할 준비가 되었음을 나타내기 위해 `ReadinessState.ACCEPTING_TRAFFIC`과 함께 바로 뒤에 `AvailabilityChangeEvent`가 전송됩니다.
9. 시작 시 예외가 있는 경우 `ApplicationFailedEvent`가 전송됩니다

위의 목록에는 SpringApplication에 연결된 SpringApplicationEvents만 포함됩니다. 이 외에도 ApplicationPreparedEvent 이후 및 ApplicationStartedEvent 이전에 다음 이벤트도 게시됩니다

- WebServer가 준비되면 WebServerInitializedEvent가 전송됩니다. ServletWebServerInitializedEvent 및 ReactiveWebServerInitializedEvent는 각각 서블릿 및 반응형 변형입니다.
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