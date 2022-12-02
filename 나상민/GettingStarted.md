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

### 4.3.2 Spring Boot CLI로 설치
Spring Boot CLI(명령줄 인터페이스)는 Spring으로 신속하게 프로토타입을 만드는 데 사용할 수 있는 명령줄 도구입니다
이를 통해 Groovy 스크립트를 실행할 수 있습니다. 즉, boilerplate 코드가 많이 없어도 친숙한 Java 유사 구문을 사용할 수 있습니다

CLI를 사용하여 Spring Boot를 사용할 필요는 없지만 IDE 없이 Spring 애플리케이션을 시작하는 빠른 방법입니다.

### 수동설치
Spring 소프트웨어 리포지토리에서 Spring CLI 배포버전을 다운로드할 수 있습니다.
- spring-boot-cli-2.6.11-SNAPSHOT-bin.zip
- spring-boot-cli-2.6.11-SNAPSHOT-bin.tar.gz

최첨단 스냅샷 배포도 가능합니다.

다운로드가 완료되면 압축을 푼 아카이브의 `INSTALL.txt` 지침을 따르십시오. 요약하면 `.zip` 파일의 `bin/` 디렉토리에 스프링 스크립트(Windows용 spring.bat)가 있습니다.
또는 .jar 파일과 함께 `java -jar`를 사용할 수 있습니다(스크립트는 클래스 경로가 올바르게 설정되었는지 확인하는 데 도움이 됨).

### SDKMAN!으로 설치
SDKMAN! (Software Development Kit Manager)는 Groovy 및 Spring Boot CLI를 포함하여 다양한 바이너리 SDK의 여러 버전을 관리하는 데 사용할 수 있습니다
SDKMAN!을 받으세요! sdkman.io에서 다음 명령을 사용하여 Spring Boot를 설치합니다

```shell
$ sdk install springboot
$ spring --version
Spring CLI v2.6.11-SNAPSHOT
```

CLI용 기능을 개발하고 빌드한 버전에 액세스하려면 다음 명령을 사용하십시오.

```shell
$ sdk install springboot dev /path/to/spring-boot/spring-boot-cli/target/spring-boot-cli-2.6.11-SNAPSHOT-bin/spring-2.6.11-SNAPSHOT/
$ sdk default springboot dev
$ spring --version
Spring CLI v2.6.11-SNAPSHOT
```

앞의 지침은 dev 인스턴스라는 스프링의 로컬 인스턴스를 설치합니다. 타겟 빌드 위치를 가리키므로 Spring Boot를 다시 빌드할 때마다 Spring이 최신 상태입니다.

다음 명령을 실행하여 볼 수 있습니다.

```shell
$ sdk ls springboot

================================================================================
Available Springboot Versions
================================================================================
> + dev
* 2.6.11-SNAPSHOT

================================================================================
+ - local version
* - installed
> - currently in use
================================================================================
```

### OSX Homebrew 설치
Mac을 사용 중이고 Homebrew를 사용하는 경우 다음 명령을 사용하여 Spring Boot CLI를 설치할 수 있습니다.

```shell
$ brew tap spring-io/tap
$ brew install spring-boot
```

Homebrew는 스프링을 `/usr/local/bin`에 설치합니다.

> 노트
> 
> 공식이 표시되지 않으면 추출 설치가 오래된 것일 수 있습니다. 이 경우 brew update를 실행하고 다시 시도하십시오.

### MacPorts 설치
Mac을 사용 중이고 MacPorts를 사용하는 경우 다음 명령을 사용하여 Spring Boot CLI를 설치할 수 있습니다.

```shell
$ sudo port install spring-boot-cli
```

### Command-line 완성
Spring Boot CLI에는 `BASH` 및 `zsh 셸`에 대한 명령 완성을 제공하는 스크립트가 포함되어 있습니다.
모든 셸에서 스크립트(spring이라고도 함)를 소싱하거나 개인 또는 시스템 전체 bash 완료 초기화에 넣을 수 있습니다.
Debian 시스템에서 시스템 전체 스크립트는 `/shell-completion/bash`에 있으며 해당 디렉토리의 모든 스크립트는 새 셸이 시작될 때 실행됩니다.
예를 들어 SDKMAN!을 사용하여 설치한 경우 스크립트를 수동으로 실행하려면 다음 명령을 사용하십시오.

```shell
$ . ~/.sdkman/candidates/springboot/current/shell-completion/bash/spring
$ spring <HIT TAB HERE>
  grab  help  jar  run  test  version
```

> 노트
> 
> Homebrew 또는 MacPorts를 사용하여 Spring Boot CLI를 설치하면 명령줄 완성 스크립트가 자동으로 셸에 등록됩니다.

### Windows Scoop 설치
Windows를 사용 중이고 Scoop을 사용하는 경우 다음 명령을 사용하여 Spring Boot CLI를 설치할 수 있습니다.

```shell
> scoop bucket add extras
> scoop install springboot
```

Scoop은 `~/scoop/apps/springboot/current/bin`에 spring을 설치합니다.

> 노트
> 
> 앱 manifest가 표시되지 않으면 스쿠프 설치가 오래된 것일 수 있습니다. 이 경우 `scoop update`를 실행하고 다시 시도하십시오.

### Quick-start Spring CLI 예제
다음 웹 응용 프로그램을 사용하여 설치를 테스트할 수 있습니다. 시작하려면 다음과 같이 `app.groovy`라는 파일을 만듭니다.

```groovy
@RestController
class ThisWillActuallyRun {

    @RequestMapping("/")
    String home() {
        "Hello World!"
    }

}
```

그런 다음 다음과 같이 셸에서 실행합니다.

```shell
$ spring run app.groovy
```

> 노트
> 
> 디펜더시가 다운로드되기 때문에 애플리케이션의 첫 번째 실행이 느립니다. 후속 실행이 훨씬 빠릅니다.

선호하는 웹 브라우저에서 `localhost:8080`을 엽니다. 다음 출력이 표시되어야 합니다.

```text
Hello World!
```