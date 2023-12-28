## 스프링부트 프로젝트를 jar파일로 생성할 때 properties 바라보는 위치 설정할 수 있어?

스프링 부트 프로젝트를 JAR 파일로 빌드할 때, 어떤 `application.properties` 또는 `application.yml` 파일을 사용할지를 결정하는 방법이 있습니다. 주로 다음과 같은 방법으로 설정합니다:

1. **우선순위에 따라 바라보는 위치:**
    
    - 스프링 부트는 여러 위치에서 `application.properties` 파일을 찾아 읽습니다. 이 위치들은 우선순위가 있습니다.
    - 스프링 부트가 찾는 우선순위에 따라 파일을 위치시키면 해당 위치에서 파일을 읽어옵니다.
    - 일반적으로는 다음과 같은 위치에서 찾습니다 (높은 우선순위에서 낮은 우선순위 순서로):
        - 파일 시스템의 현재 디렉토리
        - `classpath:/config/`
        - `classpath:/`
2. **명시적인 파일 위치 지정:**
    
    - `spring.config.location` 속성을 사용하여 특정 위치에 있는 설정 파일을 명시적으로 지정할 수 있습니다.
    - 명령행 인수로 지정할 수 있고, 다음과 같이 여러 위치를 콤마로 구분하여 지정할 수 있습니다:

        `java -jar your-application.jar --spring.config.location=classpath:/custom-config/`
        
3. **프로파일링을 활용:**
    
    - 프로파일을 사용하여 다양한 환경에 따라 다른 설정 파일을 사용할 수 있습니다.
    - 예를 들어, `application-dev.properties` 파일은 개발 환경에서, `application-prod.properties` 파일은 프로덕션 환경에서 사용될 수 있습니다.

이러한 방법 중 하나를 선택하여 프로젝트의 설정 파일을 지정하고, 빌드할 때 해당 설정 파일을 사용하도록 할 수 있습니다. 설정 파일의 위치나 이름을 변경하려면 `spring.config.name` 및 `spring.config.location`과 같은 스프링 부트의 다양한 설정 속성을 사용할 수 있습니다.
## 두개의 properties 파일을 바라보게 할 수도 있어?

네, 스프링 부트는 여러 개의 `properties` 파일을 바라볼 수 있습니다. 이를 위해 프로퍼티 파일의 이름을 조합하거나, 프로파일링을 활용하여 다양한 환경에 따라 다른 설정 파일을 사용할 수 있습니다.

### 1. 여러 프로퍼티 파일 이름 사용:

스프링 부트는 `application`이라는 기본 이름의 프로퍼티 파일을 찾습니다. 여러 개의 프로퍼티 파일을 사용하려면, 파일 이름에 프로파일 이름을 추가할 수 있습니다.

- `application-dev.properties`: 개발 환경 설정
- `application-prod.properties`: 프로덕션 환경 설정

여러 개의 프로파일을 지정하고자 할 때는 `spring.profiles.active` 속성을 사용할 수 있습니다.

### 2. `spring.config.name` 및 `spring.config.location` 사용:

- `spring.config.name`: 프로퍼티 파일의 이름을 설정합니다.
- `spring.config.location`: 프로퍼티 파일의 위치를 설정합니다.

예를 들어, `application-dev.properties`와 `custom-config/application-prod.properties` 두 개의 프로퍼티 파일이 있다면 다음과 같이 지정할 수 있습니다:

`java -jar your-application.jar --spring.config.name=application,custom-config/application --spring.profiles.active=dev,prod`

위 명령은 `application-dev.properties`와 `custom-config/application-prod.properties` 파일을 사용하며, 각각 `dev`와 `prod` 프로파일이 활성화됩니다.

이렇게 하면 여러 프로퍼티 파일을 함께 사용할 수 있습니다. 설정 파일의 위치와 이름을 명시적으로 지정하여 스프링 부트 애플리케이션이 올바른 설정을 로드하도록 할 수 있습



## jar 파일을 묶기전에 현재 내 프로젝트가 어떤 properties를 보는지는 어떻게 확인해?

스프링 부트 애플리케이션이 사용하는 설정 파일을 확인하려면 다음의 몇 가지 방법을 사용할 수 있습니다.

### 1. 실행 중인 애플리케이션 로그 확인:

실행 중인 스프링 부트 애플리케이션의 로그를 확인하여 어떤 프로퍼티 파일이 로드되었는지 확인할 수 있습니다. 애플리케이션 로그는 주로 콘솔에 출력되지만, 파일로 저장될 수도 있습니다. 로그에서는 다음과 비슷한 메시지를 찾을 수 있습니다.


```java
... 
The following profiles are active: dev 
Loading configuration for profiles: dev 
...
```

이 메시지에서 "The following profiles are active" 라인은 활성화된 프로파일을 나타내며, "Loading configuration for profiles" 라인은 어떤 설정 파일이 로드되었는지를 나타냅니다.

## 프로젝트가 특정 properties를 바라보게 설정하는법

스프링 부트 프로젝트에서 특정 properties 파일을 바라보게 하는 방법은 여러 가지가 있습니다. 아래에는 주요한 몇 가지 방법을 설명합니다.

### 1. `application.properties` 파일 사용:

스프링 부트는 기본적으로 `application.properties` 파일을 찾아 프로젝트의 설정으로 사용합니다. 따라서, 기본적인 설정 파일의 이름이나 위치를 변경하지 않은 경우에는 `src/main/resources` 디렉토리에 `application.properties` 파일을 추가하면 됩니다.

### 2. 활성 프로파일을 사용:

`application-{profile}.properties` 파일을 사용하여 특정 활성 프로파일에 대한 설정을 만들 수 있습니다. 예를 들어, `application-dev.properties`는 "dev" 프로파일에 대한 설정을 제공합니다. 활성 프로파일은 `spring.profiles.active` 속성으로 설정할 수 있습니다.

예를 들어, `application.properties`에 다음과 같이 추가할 수 있습니다:

`spring.profiles.active=dev`

### 3. 명령행 인수를 사용:

실행할 JAR 파일을 실행할 때 `--spring.config.location` 또는 `--spring.config.name` 옵션을 사용하여 특정한 설정 파일이나 위치를 지정할 수 있습니다.

예를 들어:

`java -jar your-application.jar --spring.config.name=your-custom-config`