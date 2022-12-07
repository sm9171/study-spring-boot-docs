# 6. 스프링부트로 개발하기

이 섹션에서는 Spring Boot를 사용하는 방법에 대해 자세히 설명합니다. 빌드 시스템, 자동 구성 및 애플리케이션 실행 방법과 같은 주제를 다룹니다. 또한 몇 가지 Spring Boot 모범 사례도 다룹니다.
Spring Boot에 대해 특별히 특별한 것은 없지만(사용할 수 있는 또 다른 라이브러리일 뿐임) 따를 때 개발 프로세스를 좀 더 쉽게 만들어 주는 몇 가지 권장 사항이 있습니다.

Spring Boot를 시작하는 경우 이 섹션을 시작하기 전에 시작하기 가이드를 읽어야 합니다.

## 6.1 빌드 시스템들

종속성 관리를 지원하고 "Maven Central" 리포지토리에 게시된 아티팩트를 사용할 수 있는 빌드 시스템을 선택하는 것이 좋습니다.
Maven 또는 Gradle을 선택하는 것이 좋습니다. Spring Boot가 다른 빌드 시스템(예: Ant)과 함께 작동하도록 할 수 있지만 특별히 잘 지원되지는 않습니다.

### 6.1.1 종속성 관리

Spring Boot의 각 릴리스는 지원하는 선별된 종속성 목록을 제공합니다. 실제로 빌드 구성에서 이러한 종속성에 대한 버전을 제공할 필요가 없습니다. Spring Boot가 이를 관리하기 때문입니다.
Spring Boot 자체를 업그레이드하면 이러한 종속성도 일관된 방식으로 업그레이드됩니다.

> Note
>
> 필요한 경우 여전히 버전을 지정하고 Spring Boot의 권장 사항을 재정의할 수 있습니다.

선별된 목록에는 Spring Boot와 함께 사용할 수 있는 모든 Spring 모듈과 정제된 타사 라이브러리 목록이 포함되어 있습니다.
이 목록은 Maven 및 Gradle과 함께 사용할 수 있는 표준 BOM(`spring-boot-dependencies`)으로 제공됩니다.

> 경고
>
> Spring Boot의 각 릴리스는 Spring Framework의 기본 버전과 연결됩니다. 해당 버전을 지정하지 않는 것이 좋습니다.

### 6.1.2 Maven

Maven과 함께 Spring Boot를 사용하는 방법에 대해 알아보려면 Spring Boot의 Maven 플러그인에 대한 설명서를 참조하십시오.

- Reference (HTML and PDF)
- API

### 6.1.3. Gradle

Gradle과 함께 Spring Boot를 사용하는 방법에 대해 알아보려면 Spring Boot의 Gradle 플러그인 설명서를 참조하세요.

- Reference (HTML and PDF)
- API

### 6.1.4 Ant

Apache Ant+Ivy를 사용하여 Spring Boot 프로젝트를 빌드할 수 있습니다. `spring-boot-antlib` "AntLib" 모듈은 Ant가 실행 가능한 jar를 생성하는 데 도움이 되도록 사용할
수도 있습니다.

종속성을 선언하기 위한 일반적인 ivy.xml 파일은 다음 예제와 유사합니다.

```xml

<ivy-module version="2.0">
    <info organisation="org.springframework.boot" module="spring-boot-sample-ant"/>
    <configurations>
        <conf name="compile" description="everything needed to compile this module"/>
        <conf name="runtime" extends="compile" description="everything needed to run this module"/>
    </configurations>
    <dependencies>
        <dependency org="org.springframework.boot" name="spring-boot-starter"
                    rev="${spring-boot.version}" conf="compile"/>
    </dependencies>
</ivy-module>
```

일반적인 build.xml은 다음 예제와 같습니다.

```xml

<project
        xmlns:ivy="antlib:org.apache.ivy.ant"
        xmlns:spring-boot="antlib:org.springframework.boot.ant"
        name="myapp" default="build">

    <property name="spring-boot.version" value="2.6.11-SNAPSHOT"/>

    <target name="resolve" description="--> retrieve dependencies with ivy">
        <ivy:retrieve pattern="lib/[conf]/[artifact]-[type]-[revision].[ext]"/>
    </target>

    <target name="classpaths" depends="resolve">
        <path id="compile.classpath">
            <fileset dir="lib/compile" includes="*.jar"/>
        </path>
    </target>

    <target name="init" depends="classpaths">
        <mkdir dir="build/classes"/>
    </target>

    <target name="compile" depends="init" description="compile">
        <javac srcdir="src/main/java" destdir="build/classes" classpathref="compile.classpath"/>
    </target>

    <target name="build" depends="compile">
        <spring-boot:exejar destfile="build/myapp.jar" classes="build/classes">
            <spring-boot:lib>
                <fileset dir="lib/runtime"/>
            </spring-boot:lib>
        </spring-boot:exejar>
    </target>
</project>
```

> Tip
>
> spring-boot-antlib 모듈을 사용하지 않으려면 `build an Executable Archive from Ant without Using spring-boot-antlib` "How-to"를
> 참조하십시오.

### 6.1.5. Starters

스타터는 애플리케이션에 포함할 수 있는 편리한 종속성 설명자 집합입니다.
샘플 코드를 검색하고 많은 종속성 설명자를 복사하여 붙여넣지 않고도 필요한 모든 Spring 및 관련 기술에 대한 원스톱 상점을 얻을 수 있습니다.
예를 들어 데이터베이스 액세스를 위해 Spring 및 JPA를 사용하여 시작하려는 경우 프로젝트에 `spring-boot-starter-data-jpa` 종속성을 포함합니다.

스타터에는 일관되고 지원되는 관리 전이적 종속성 세트를 사용하여 프로젝트를 신속하게 시작하고 실행하는 데 필요한 많은 종속성이 포함되어 있습니다.

> Starters 이름의 뜻은?
>
> 모든 공식 스타터는 유사한 이름 지정 패턴을 따릅니다. `spring-boot-starter-*`, 여기서 *는 특정 유형의 애플리케이션입니다.
> 이 명명 구조는 스타터를 찾아야 할 때 도움을 주기 위한 것입니다. 많은 IDE의 Maven 통합을 통해 종속성을 이름으로 검색할 수 있습니다.
> 예를 들어 적절한 Eclipse 또는 Spring Tools 플러그인이 설치된 경우 POM 편집기에서 ctrl-space를 누르고 "`spring-boot-starter`"를 입력하면 전체 목록을 볼 수
> 있습니다.
>
> "자신만의 스타터 만들기" 섹션에서 설명한 것처럼 타사 스타터는 공식 Spring Boot 아티팩트용으로 예약되어 있으므로 spring-boot로 시작하면 안 됩니다. 오히려 타사 스타터는 일반적으로 프로젝트
> 이름으로 시작합니다.
> 예를 들어 thirdpartyproject라는 타사 스타터 프로젝트는 일반적으로 thirdpartyproject-spring-boot-starter로 이름이 지정됩니다.

다음 애플리케이션 스타터는 `org.springframework.boot` 그룹 아래의 Spring Boot에서 제공됩니다.

표 1. Spring Boot 애플리케이션 스타터

| 이름                           | 설명                                                                                                          |
|------------------------------|-------------------------------------------------------------------------------------------------------------|
| spring-boot-starter          | 자동 구성 지원, 로깅 및 YAML을 포함한 핵심 스타터                                                                             |
| spring-boot-starter-activemq | Apache ActiveMQ를 사용하는 JMS 메시징용 스타터                                                                          |
| spring-boot-starter-amqp     | Spring AMQP 및 Rabbit MQ 사용을 위한 스타터                                                                          |
| spring-boot-starter-aop | Spring AOP 및 AspectJ를 사용한 관점 지향 프로그래밍을 위한 스타터                                                               |
| spring-boot-starter-artemis | Apache Artemis를 사용하는 JMS 메시징용 스타터                                                                           |
| spring-boot-starter-batch | Spring Batch 사용을 위한 스타터                                                                                     |
| spring-boot-starter-cache | Spring Framework의 캐싱 지원을 사용하기 위한 스타터                                                                        |
| spring-boot-starter-data-cassandra | Cassandra 분산 데이터베이스 및 Spring Data Cassandra 사용을 위한 스타터                                                      |
| spring-boot-starter-data-cassandra-reactive | Cassandra 분산 데이터베이스 및 Spring Data Cassandra Reactive 사용을 위한 스타터                                             |
| spring-boot-starter-data-couchbase | Couchbase 문서 지향 데이터베이스 및 Spring Data Couchbase 사용을 위한 스타터                                                   |
| spring-boot-starter-data-couchbase-reactive | Couchbase 문서 지향 데이터베이스 및 Spring Data Couchbase Reactive 사용을 위한 스타터                                          |
| spring-boot-starter-data-elasticsearch | Elasticsearch 검색 및 분석 엔진과 Spring Data Elasticsearch를 사용하기 위한 스타터                                            |
| spring-boot-starter-data-jdbc | Spring Data JDBC를 사용하기 위한 스타터                                                                               |
| spring-boot-starter-data-jpa | Hibernate와 함께 Spring Data JPA를 사용하기 위한 스타터                                                                  |
| spring-boot-starter-data-ldap | Spring Data LDAP 사용을 위한 스타터                                                                                 |
| spring-boot-starter-data-mongodb | MongoDB 문서 지향 데이터베이스 및 Spring Data MongoDB 사용을 위한 스타터                                                       |
| spring-boot-starter-data-mongodb-reactive | MongoDB 문서 지향 데이터베이스 및 Spring Data MongoDB Reactive 사용을 위한 스타터                                              |
| spring-boot-starter-data-neo4j | Neo4j 그래프 데이터베이스 및 Spring Data Neo4j 사용을 위한 스타터                                                             |
| spring-boot-starter-data-r2dbc | Spring Data R2DBC를 사용하기 위한 스타터                                                                              |
| spring-boot-starter-data-redis | Spring Data Redis 및 Lettuce 클라이언트와 함께 Redis 키-값 데이터 저장소를 사용하기 위한 스타터                                        |
| spring-boot-starter-data-redis-reactive | Spring Data Redis 반응성 및 Lettuce 클라이언트와 함께 Redis 키-값 데이터 저장소를 사용하기 위한 스타터                                    |
| spring-boot-starter-data-rest | Spring Data REST를 사용하여 REST를 통해 Spring Data 리포지토리를 노출하기 위한 스타터                                              |
| spring-boot-starter-freemarker | FreeMarker 보기를 사용하여 MVC 웹 애플리케이션 구축을 위한 스타터                                                                 |
| spring-boot-starter-groovy-templates | Groovy 템플릿 뷰를 사용하여 MVC 웹 애플리케이션을 구축하기 위한 스타터                                                                |
| spring-boot-starter-hateoas | Spring MVC 및 Spring HATEOAS로 하이퍼미디어 기반 RESTful 웹 애플리케이션 구축을 위한 스타터                                          |
| spring-boot-starter-integration | Spring 통합 사용을 위한 스타터                                                                                        |
| spring-boot-starter-jdbc | HikariCP 연결 풀과 함께 JDBC를 사용하기 위한 스타터                                                                         |
| spring-boot-starter-jersey | JAX-RS 및 Jersey를 사용하여 RESTful 웹 애플리케이션을 구축하기 위한 스타터입니다. 대안`spring-boot-starter-web`                         |
| spring-boot-starter-jooq | jOOQ를 사용하여 JDBC로 SQL 데이터베이스에 액세스하기 위한 스타터입니다. 대안 `spring-boot-starter-data-jpa`또는`spring-boot-starter-jdbc` |
| spring-boot-starter-json | json 읽기 및 쓰기 스타터                                                                                            |
| spring-boot-starter-jta-atomikos |Atomikos를 사용하는 JTA 트랜잭션의 스타터|
| spring-boot-starter-mail |Java Mail 및 Spring Framework의 이메일 전송 지원을 사용하기 위한 스타터|
| spring-boot-starter-mustache |Mustache 뷰를 사용하여 웹 애플리케이션을 구축하기 위한 스타터|
| spring-boot-starter-oauth2-client |Spring Security의 OAuth2/OpenID Connect 클라이언트 기능을 사용하기 위한 스타터|
| spring-boot-starter-oauth2-resource-server |Spring Security의 OAuth2 리소스 서버 기능을 사용하기 위한 스타터|
| spring-boot-starter-quartz |Quartz 스케줄러 사용을 위한 스타터|
| spring-boot-starter-rsocket |RSocket 클라이언트 및 서버 구축을 위한 스타터|
| spring-boot-starter-security |Spring Security 사용을 위한 스타터|
| spring-boot-starter-test |JUnit Jupiter, Hamcrest 및 Mockito를 포함한 라이브러리로 Spring Boot 애플리케이션을 테스트하기 위한 스타터|
| spring-boot-starter-thymeleaf |Thymeleaf 뷰를 사용하여 MVC 웹 애플리케이션 구축을 위한 스타터|
| spring-boot-starter-validation |Hibernate Validator와 함께 Java Bean Validation을 사용하기 위한 스타터|
| spring-boot-starter-web |RESTful을 포함한 웹, Spring MVC를 사용하는 애플리케이션 구축을 위한 스타터. Tomcat을 기본 내장 컨테이너로 사용|
| spring-boot-starter-web-services |Spring Web Services 사용을 위한 스타터|
| spring-boot-starter-webflux |Spring Framework의 Reactive Web 지원을 사용하여 WebFlux 애플리케이션을 구축하기 위한 스타터|
| spring-boot-starter-websocket |Spring Framework의 WebSocket 지원을 사용하여 WebSocket 애플리케이션을 구축하기 위한 스타터|

애플리케이션 스타터 외에도 다음 스타터를 사용하여 프로덕션 준비 기능을 추가할 수 있습니다.

표 2. Spring Boot 생산 스타터

| 이름                           | 설명                                                                                            |
|------------------------------|-----------------------------------------------------------------------------------------------|
| spring-boot-starter-actuator         | 애플리케이션을 모니터링하고 관리하는 데 도움이 되는 프로덕션 준비 기능을 제공하는 Spring Boot의 Actuator를 사용하기 위한 스타터 |

마지막으로 Spring Boot에는 특정 기술 측면을 제외하거나 교체하려는 경우 사용할 수 있는 다음 스타터도 포함되어 있습니다.

표 3. Spring Boot 기술 스타터

| 이름                        | 설명                                                                                      |
|---------------------------|-----------------------------------------------------------------------------------------|
| spring-boot-starter-jetty | 포함된 서블릿 컨테이너로 Jetty를 사용하기 위한 스타터입니다. 대안 `spring-boot-starter-tomcat` |
| spring-boot-starter-log4j2| 로깅을 위해 Log4j2를 사용하기 위한 스타터. 대안 `spring-boot-starter-logging` |
| spring-boot-starter-logging | Logback을 사용하여 로깅하기 위한 스타터입니다. 기본 로깅 스타터 |
| spring-boot-starter-reactor-netty | 내장된 반응형 HTTP 서버로 Reactor Netty를 사용하기 위한 스타터입니다. |
| spring-boot-starter-tomcat | 포함된 서블릿 컨테이너로 Tomcat을 사용하기 위한 스타터입니다. 다음에서 사용하는 기본 서블릿 컨테이너 스타터 `spring-boot-starter-web` |
| spring-boot-starter-undertow | 내장된 서블릿 컨테이너로 Undertow를 사용하기 위한 스타터. 대안 `spring-boot-starter-tomcat` |

기술적 측면을 교환하는 방법에 대해 알아보려면 웹 서버 및 로깅 시스템 교환 방법 설명서를 참조하십시오.

> 팁
> 
> 추가 커뮤니티 기여 스타터 목록은 GitHub 의 모듈에 있는 README 파일 을 참조하십시오. `spring-boot-starters`

## 6.2 코드 구조화
Spring Boot는 작동하는 데 특정 코드 레이아웃이 필요하지 않습니다. 그러나 도움이 되는 몇 가지 모범 사례가 있습니다

### 6.2.1. 디폴트 패키지 사용
클래스가 패키지 선언을 포함하지 않으면 "디폴트 패키지"에 있는 것으로 간주됩니다. "디폴트 패키지"의 사용은 일반적으로 권장되지 않으며 피해야 합니다.
모든 jar의 모든 클래스를 읽기 때문에 @ComponentScan, @ConfigurationPropertiesScan, @EntityScan 또는 @SpringBootApplication 어노테이션을 사용하는 Spring Boot 애플리케이션에 특정 문제가 발생할 수 있습니다.

> 팁
> 
> Java의 권장 패키지 명명 규칙을 따르고 반전된 도메인 이름(예: com.example.project)을 사용하는 것이 좋습니다.

### 기본 애플리케이션 클래스 찾기
일반적으로 다른 클래스 위의 루트 패키지에서 기본 애플리케이션 클래스를 찾을 것을 권장합니다.
@SpringBootApplication 어노테이션은 종종 기본 클래스에 배치되며 특정 항목에 대한 기본 "검색 패키지"를 암시적으로 정의합니다.
예를 들어 JPA 애플리케이션을 작성하는 경우 @SpringBootApplication 어노테이션 클래스의 패키지를 사용하여 @Entity 항목을 검색합니다. 
루트 패키지를 사용하면 구성 요소 스캔을 프로젝트에만 적용할 수도 있습니다

> 팁
> 
> @SpringBootApplication을 사용하지 않으려면 가져오는 @EnableAutoConfiguration 및 @ComponentScan 어노테이션이 해당 동작을 정의하므로 대신 사용할 수도 있습니다.

다음 목록은 일반적인 레이아웃을 보여줍니다.

```text
com
 +- example
     +- myapplication
         +- MyApplication.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```

MyApplication.java 파일은 다음과 같이 기본 @SpringBootApplication과 함께 기본 메서드를 선언합니다

```java
@SpringBootApplication
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }

}
```