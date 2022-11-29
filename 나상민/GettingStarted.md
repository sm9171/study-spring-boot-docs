# 4. 시작하기
만약에 `Spring Boot` 나 일반적인 `Spring`을 시작한다면, 이 섹션을 읽고 시작 해야 합니다.
이 문서는 `무엇을`, `어떻게`, `왜`와 같은 질문에 대한 기본 답변입니다. 또한, 설치 방법을 포함한 SpringBoot에 대한 소개가 포함되어 있습니다.
그런 다음 당신의 첫 Spring Boot 어플리케이션을 빌드하는 과정을 안내하고 몇 가지 핵심 원칙에 대해 논의 합니다.

## 4.1 SpringBoot 소개
Spring Boot는 독립적으로 배포 실행할 수 있는 Spring 기반의 애플리케이션을 만드는데 도움이 됩니다.
최소한의 비용으로 시작할 수 있도록 Spring플랫폼 및 타사 라이브러리에 대해 지향하는 견해를 갖습니다.
대부분의 Spring Boot 어플리케이션은 최소한의 Spring 설정이 필요합니다.

Spring Boot를 사용하여 java -jar 또는 보다 전통적인 war 배포를 사용하여 시작할 수 있는 Java 애플리케이션을 만들 수 있습니다.
`Spring Script`를 실행하는 명령줄 도구 또한 제공합니다.

우리의 주요 목표는 :
- 모든 Spring 개발을 위해 더 빠르고 광범위하게 시작하는 경험을 제공합니다.
- 추가적인 설정없이 사용하는 것을 지향하지만, 기본 설정과 다른 요구사항이 있으면 바로 변경이 가능합니다.
- 임베디드 서버, 보안, 메트릭, 상태 확인 및 외부화된 구성과 같은 대규모 프로젝트 클래스에 공통적인 다양한 비기능 기능을 제공합니다.
- 코드 생성 및 XML 구성에 대한 요구 사항이 전혀 없습니다.

## 4.2 시스템 요구사항
Spring Boot 2.6.11-SNAPSHOT에는 Java 8이 필요하며 Java 18까지 호환됩니다. Spring Framework 5.3.22 이상이 필요합니다.

다음 빌드 도구에 대한 명시적 빌드 지원이 제공됩니다:

| 빌드 툴  | 버전 |
|-------|--------------|
| Maven | 3.5+         |
| Gradle | 6.8.x, 6.9.x, and 7.x |

### 4.2.1. Servlet Containers
Spring Boot는 다음 임베디드 서블릿 컨테이너를 지원합니다:

| 이름  | 서블릿 버전 |
|-------|--------|
| Tomcat 9.0 | 4.0    |
| Jetty 9.4 | 3.1    |
| Jetty 10.0 | 4.0    |
| Undertow 2.0 | 4.0    |
모든 서블릿 3.1+ 호환 컨테이너에 Spring Boot 애플리케이션을 배포할 수도 있습니다.

## 4.3 Spring Boot 설치
SpringBoot 고전적인 자바 개발 도구와 함께 사용하거나 커멘드 라인으로 설치할 수 있습니다.
어떤 방법이든 Java SDK v1.8이상이 필요합니다. 시작하기 전에 다음 명령을 사용하여 현재 자바가 설치되어있는지 확인해야 합니다.
```shell
$ java -version
```
자바 개발이 처음이거나 Spring Boot를 경험하고 싶다면 먼저 Spring Boot CLI를 사용해 볼수 있습니다. 그렇지 않으면 고전적인 설치 지침을 읽으세요.

### 4.3.1 자바 개발자를 위한 설치 지침
표준 자바 라이브러리와 동일한 방식으로 스프링 부트를 사용할 수 있습니다. 진행할려면, 클래스 경로에 적절한 `spring-boot-*.jar`파일을 포함하세요.
스프링 부트에는 특별한 도구 통합이 필요하지 않으므로 모든 IDE 또는 텍스트 편집기를 사용할 수 있습니다.
또한 스프링 부트 애플리케이션에는 특별한 것이 없으므로 다른 자바 프로그램과 마찬가지로 스프링 부트 애플리케이션을 실행하고 디버그할 수 있습니다.

`Spring Boot jars`를 복사할 수 있지만, 일반적으로 종속성 관리를 지원하는 빌드 도구(예: Maven 또는 Gradle)를 사용하는 것이 좋습니다.

### Maven 설치
스프링 부트는 Apache Maven 3.3 이상과 호환됩니다. Maven을 아직 설치하지 않은 경우 [maven.apache.org](https://maven.apache.org/)의 지침을 따를 수 있습니다.
> TIP
>  
> 많은 운영 체제에서 Maven은 패키지 관리자와 함께 설치할 수 있습니다. 
 OSX Homebrew를 사용하는 경우 brew install maven을 사용해 보세요.
 Ubuntu 사용자는 sudo apt-get install maven을 실행할 수 있습니다.
 Chocolatey를 사용하는 Windows 사용자는 상승된(관리자) 프롬프트에서 choco install maven을 실행할 수 있습니다.

스프링부트 디펜더시는 `org.springframework.boot` `groupId`를 사용합니다.
일반적으로 Maven POM 파일은 `spring-boot-starter-parent` 프로젝트에서 상속되며 하나 이상의 "Starters"에 대한 종속성을 선언합니다.
Spring Boot는 또한 실행 가능한 jar를 생성하기 위한 선택적 Maven 플러그인을 제공합니다.

Spring Boot 및 Maven을 시작하는 방법에 대한 자세한 내용은 Maven 플러그인 참조 가이드의 시작하기 섹션에서 찾을 수 있습니다.

### Gradle 설치
Spring Boot는 Gradle 6.8, 6.9 및 7.x와 호환됩니다. Gradle을 아직 설치하지 않은 경우 gradle.org의 지침을 따를 수 있습니다.

Spring Boot 종속성은 `org.springframework.boot` 그룹을 사용하여 선언할 수 있습니다. 일반적으로 프로젝트는 하나 이상의 "스타터"에 대한 종속성을 선언합니다.
Spring Boot는 종속성 선언을 단순화하고 실행 가능한 jar를 생성하는 데 사용할 수 있는 유용한 Gradle 플러그인을 제공합니다.

>Gradle Wrapper
> 
> Gradle Wrapper는 프로젝트를 빌드해야 할 때 Gradle을 얻기위한 좋은 방법을 제공합니다.
> 빌드 프로세스를 부트스트랩하기 위해 코드와 함께 커밋하는 작은 스크립트 및 라이브러리입니다.
> 자세한 내용은 docs.gradle.org/current/userguide/gradle_wrapper.html을 참조하세요.

Spring Boot 및 Gradle을 시작하는 방법에 대한 자세한 내용은 Gradle 플러그인 참조 가이드의 시작하기 섹션에서 확인할 수 있습니다.