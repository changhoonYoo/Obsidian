
- log, errorlog 분리하여 저장하기 위한 설정

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<!-- 60초마다 설정 파일의 변경을 확인 하여 변경시 갱신 -->  
<configuration scan="true" scanPeriod="60 seconds">  
  
    <!--Environment 내의 프로퍼티들을 개별적으로 설정할 수도 있다.-->  
    <springProperty scope="context" name="LOG_LEVEL" source="logging.level.root"/>  
    <!-- log file path -->  
    <property name="LOG_PATH" value="./logs"/>  
    <!-- log file name -->  
    <property name="LOG_FILE_NAME" value="log"/>  
    <!-- err log file name -->  
    <property name="ERR_LOG_FILE_NAME" value="err"/>  
    <!-- pattern -->  
    <property name="LOG_PATTERN"  
              value="%-5level %d{yy-MM-dd HH:mm:ss}[%thread] [%logger{0}:%line] - %msg%n"/>  
  
    <!-- Console Appender -->  
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">  
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">  
            <pattern>${LOG_PATTERN}</pattern>  
        </encoder>  
    </appender>  
  
    <!-- File Appender -->  
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">  
        <!-- 파일경로 설정 -->  
        <file>${LOG_PATH}/${LOG_FILE_NAME}.log</file>  
        <!-- 출력패턴 설정-->  
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">  
            <pattern>${LOG_PATTERN}</pattern>  
  
        </encoder>  
        <!-- Rolling 정책 -->  
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">  
            <!-- .gz,.zip 등을 넣으면 자동 일자별 로그파일 압축 -->  
            <fileNamePattern>${LOG_PATH}/${LOG_FILE_NAME}.%d{yyyy-MM-dd}_%i.log</fileNamePattern>  
            <timeBasedFileNamingAndTriggeringPolicy                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">  
                <!-- 파일당 최고 용량 kb, mb, gb -->                <maxFileSize>10MB</maxFileSize>  
            </timeBasedFileNamingAndTriggeringPolicy>  
            <!-- 일자별 로그파일 최대 보관주기(~일), 해당 설정일 이상된 파일은 자동으로 제거-->  
            <!--  <maxHistory>30</maxHistory>-->            <!--<MinIndex>1</MinIndex> <MaxIndex>10</MaxIndex>-->        </rollingPolicy>  
    </appender>  
  
    <!-- 에러의 경우 파일에 로그 처리 -->  
    <appender name="Error" class="ch.qos.logback.core.rolling.RollingFileAppender">  
        <filter class="ch.qos.logback.classic.filter.LevelFilter">  
            <level>error</level>  
            <onMatch>ACCEPT</onMatch>  
            <onMismatch>DENY</onMismatch>  
        </filter>  
        <file>${LOG_PATH}/${ERR_LOG_FILE_NAME}.log</file>  
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">  
            <pattern>${LOG_PATTERN}</pattern>  
        </encoder>  
        <!-- Rolling 정책 -->  
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">  
            <!-- .gz,.zip 등을 넣으면 자동 일자별 로그파일 압축 -->  
            <fileNamePattern>${LOG_PATH}/${ERR_LOG_FILE_NAME}.%d{yyyy-MM-dd}_%i.log</fileNamePattern>  
            <timeBasedFileNamingAndTriggeringPolicy                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">  
                <!-- 파일당 최고 용량 kb, mb, gb -->                <maxFileSize>10MB</maxFileSize>  
            </timeBasedFileNamingAndTriggeringPolicy>  
            <!-- 일자별 로그파일 최대 보관주기(~일), 해당 설정일 이상된 파일은 자동으로 제거-->  
            <!--  <maxHistory>30</maxHistory>-->        </rollingPolicy>  
    </appender>  
  
    <!-- root레벨 설정 -->  
    <root level="${LOG_LEVEL}">  
        <appender-ref ref="CONSOLE"/>  
        <appender-ref ref="FILE"/>  
        <appender-ref ref="Error"/>  
    </root>  
  
    <logger name="org.apache.ibatis" level="DEBUG" additivity="false">  
        <appender-ref ref="CONSOLE"/>  
        <appender-ref ref="FILE"/>  
        <appender-ref ref="Error"/>  
    </logger>  
  
    <!-- log4jdbc 옵션 설정 -->  
    <logger name="jdbc" level="OFF"/>  
    <!-- 커넥션 open close 이벤트를 로그로 남긴다. -->  
    <logger name="jdbc.connection" level="OFF"/>  
    <!-- SQL문만을 로그로 남기며, PreparedStatement일 경우 관련된 argument 값으로 대체된 SQL문이 보여진다. -->  
    <logger name="jdbc.sqlonly"  
            level="OFF"/> <!-- SQL문과 해당 SQL을 실행시키는데 수행된 시간 정보(milliseconds)를 포함한다. -->  
    <logger name="jdbc.sqltiming" level="DEBUG"/>  
    <!-- ResultSet을 제외한 모든 JDBC 호출 정보를 로그로 남긴다. 많은 양의 로그가 생성되므로 특별히 JDBC 문제를 추적해야 할 필요가 있는 경우를 제외하고는 사용을 권장하지 않는다. -->  
    <logger name="jdbc.audit" level="OFF"/>  
    <!-- ResultSet을 포함한 모든 JDBC 호출 정보를 로그로 남기므로 매우 방대한 양의 로그가 생성된다. -->  
    <logger name="jdbc.resultset" level="OFF"/>  
    <!-- SQL 결과 조회된 데이터의 table을 로그로 남긴다. -->  
    <logger name="jdbc.resultsettable" level="OFF"/>  
  
</configuration>
```


## Logback 이란

log4j 이후에 출시된 Java 기반 Logging Framework 중 하나로 SLF4j 의 구현체이고

Spring Boot 라면 기본적으로 포함되어 있다. log4j와 성능을 비교했을 때도 logback이 월등하다는 평가가 많다.

##   **Logback 설정 방법**

1. .yml(.properties) 파일 생성.

1-1 프로필 설정

```properties
# 프로필 설정
spring.profiles.active=local
#spring.profiles.active=dev
#spring.profiles.active=prod

#루트 레벨(전체 레벨) 전체 로깅 레벨 지정
logging.level.root=info
```

*dev, prod 등의 환경을 별도로 선언할 수 있고 프로필 마다 다른 로그 설정을 적용할 수 있다.

1-2 프로필에 해당하는 .yml(.properties) 파일 생성

위 예시의 경우 **local**로 설정을 해뒀기에 **local**에 해당하는 .yml(.properties) 파일을 추가로 만든다.

**logback-local.properties 파일 생성**

```properties
#로그파일 경로
log.config.path=/logs/local
#로그파일 이름
log.config.filename=local_log
```

*로그 파일을 저장할/출력할 경로 및 로그 파일의 명칭을 변수로 담아둔다.

2. resources밑에 logback-spring.xml 파일을 만들어서 참조한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!-- 60초마다 설정 파일의 변경을 확인 하여 변경시 갱신 -->
<configuration scan="true" scanPeriod="60 seconds">

 <!-- 이 곳에 추가할 기능을 넣는다. -->
 
</configuration>
```

하나씩 살펴보면서 추가해보자.

- 아래는 profile과 관련된 코드이다. 

profile의 이름에 따라 어떤 resource를 참조하면 되는지 설정해둔 것으로 이 예시의 경우 local로 설정해뒀기에

아까 local 전용으로 추가적으로 만든 logback-local.properties를 찾도록 설정되어 있다.

```xml
<!--springProfile 태그를 사용하면 logback 설정파일에서 복수개의 프로파일을 설정할 수 있다.-->
<springProfile name="local">
   <property resource="logback-local.properties"/>
</springProfile>
<springProfile name="dev">
   <property resource="logback-dev.properties"/>
</springProfile>
<springProfile name="prod">
   <property resource="logback-prod.properties"/>
</springProfile>
```

- 아래는 property와 관련된 코드이다.

```xml
<!--Environment 내의 프로퍼티들을 개별적으로 설정할 수도 있다.-->
    <springProperty scope="context" name="LOG_LEVEL" source="logging.level.root"/>

    <!-- log file path -->
    <property name="LOG_PATH" value="${log.config.path}"/>
    <!-- log file name -->
    <property name="LOG_FILE_NAME" value="${log.config.filename}"/>
    <!-- err log file name -->
    <property name="ERR_LOG_FILE_NAME" value="err_log"/>
    <!-- pattern -->
    <property name="LOG_PATTERN" value="%-5level %d{yy-MM-dd HH:mm:ss}[%thread] [%logger{0}:%line] - %msg%n"/>
```

1. **.yml(.properties) 파일**에 정의한 log level 혹은 dir(경로)를 설정할 수 있다.

위 경우 LOG_LEVEL로 .yml(.properties) 파일에 정의한 루트 레벨 로그 설정을 따라간다고 한 것이다.

2. **logback-local.properties**에서 설정해둔

log.config.path와 log.config.filename등을 property에 각각 담아둔다.

에러 로그 이름과 같이 XML 파일에서 바로 값을 기입할 수도 있다.

3. 로그 패턴을 설정해준다.

참고로 패턴에 사용되는 내용은 아래와 같다.

  
%-5level : 로그 레벨, -5는 출력의 고정폭 값(5글자)  
%msg : - 로그 메시지 (=%message)  
${PID:-} : 프로세스 아이디  
%d : 로그 기록시간  
%p : 로깅 레벨  
%F : 로깅이 발생한 프로그램 파일명  
%M : 로깅일 발생한 메소드의 명  
%l : 로깅이 발생한 호출지의 정보  
%L : 로깅이 발생한 호출지의 라인 수  
%thread : 현재 Thread 명  
%t : 로깅이 발생한 Thread 명  
%c : 로깅이 발생한 카테고리  
%C : 로깅이 발생한 클래스 명  
%m : 로그 메시지  
%n : 줄바꿈(new line)  
%% : %를 출력 
%r : 애플리케이션 시작 이후부터 로깅이 발생한 시점까지의 시간(ms)

아래 처럼 콘솔에 로그를 출력할 수도 있고


```xml
<!-- 콘솔에 로그를 남기는 것 -->
<appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
  <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
		<!-- 위에 PROPERTY로 정의한 LOG_PATTERN 가져다 사용하기 -->
		<pattern>${LOG_PATTERN}</pattern>
  </encoder>
</appender>
```

아래 처럼 파일로 로그를 출력할 수도 있다.

```xml
    <!-- File Appender -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
       
       <!-- 파일경로 설정 -->
        <file>${LOG_PATH}/${LOG_FILE_NAME}.log</file>
        
        <!-- 출력패턴 설정-->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>

        <!-- Rolling 정책 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        
            <!-- 로그 파일을 압축 하고싶다면 .log 대신 .zip이나 .gz을 끝에 붙이면 된다 -->
            <fileNamePattern>${LOG_PATH}/${LOG_FILE_NAME}.%d{yyyy-MM-dd}_%i.zip</fileNamePattern>
            
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
            <!-- 파일당 최대 용량 kb, mb, gb -->
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            
            <!-- 일자별 로그파일 최대 보관주기(~일), 해당 설정일 이상된 파일은 자동으로 제거-->
            <maxHistory>30</maxHistory>
            
            <!--10개가 넘어가면 오래된순으로 덮어쓰여진다-->
            <MinIndex>1</MinIndex>
            <MaxIndex>10</MaxIndex>
        
        </rollingPolicy>
    </appender>
```

여기서 ROLLING 정책은 파일로 로그를 저장하게 될 경우 그 용량이 계속 늘어나기 때문에 적용하는 정책으로

압축할 것인지, 파일당 최대 용량이 몇인지, 몇일 후에 로그를 지울 것인지 설정할 수 있다.

10MB단위로 로그 압축 파일이 생성되고 Index에 설정한대로 (1 ~ 10)

10개가 넘어가면 오래된순으로 덮어쓰여진다. 

즉, 하루에 생길 수 있는 로그는 무조건 10mb이며 이를 초과하게 되면 오래된 로그부터 없어지는 것.

추가로 저장된지 30일 이 지난 로그는  자동 제거되는 것이다.

에러 관련 파일 로그 출력은 위 파일 로그 출력과 그 포맷이 동일하다.

```xml
    <!-- 에러의 경우 파일에 로그 처리 -->
    <appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>error</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <file>${LOG_PATH}/${ERR_LOG_FILE_NAME}.log</file>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
        <!-- Rolling 정책 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- .gz,.zip 등을 넣으면 자동 일자별 로그파일 압축 -->
            <fileNamePattern>${LOG_PATH}/${ERR_LOG_FILE_NAME}.%d{yyyy-MM-dd}_%i.log</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <!-- 파일당 최고 용량 kb, mb, gb -->
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <!-- 일자별 로그파일 최대 보관주기(~일), 해당 설정일 이상된 파일은 자동으로 제거-->
            <maxHistory>60</maxHistory>
        </rollingPolicy>
    </appender>
```

마지막으로 root레벨을 설정해준다.

맨 처음 .yml(.properties) 파일에 설정한

> #루트 레벨(전체 레벨) 전체 로깅 레벨 지정  
> logging.level.root=info 를 logback-spring.xml파일에서 

> </springproperty scope="context" name="LOG_LEVEL" source="logging.level.root">  
> source로 받아서 LOG_LEVEL에 저장하고

아래 root레벨 설정에서 받아서

```
    <!-- root레벨 설정 -->
    <root level="${LOG_LEVEL}">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="FILE"/>
        <appender-ref ref="ERROR"/>
    </root>
```

root 레벨이 info 일 경우 아래 3가지

CONSOLE,FILE,ERROR등을 출력한다고 볼 수 있다.

*CONSOLE,FILE,ERROR는 각각 Appender의 이름으로 설정해둔 것이다.

그래서 만약 커스터마이징을 하여 console만 찍고 싶다면 아래와 같이 할 수 있을 것이다.

```
    <!-- root레벨 설정 -->
    <root level="${LOG_LEVEL}">
        <appender-ref ref="CONSOLE"/>
        <!-- <appender-ref ref="FILE"/> -->
        <!-- <appender-ref ref="ERROR"/> -->
    </root>
```

---

참고로  CASE 별 컴파일 순서는 다음과 같다.

1) resources밑에 **logback-spring.xml파일**이 있으면

=> **logback-spring.xml 파일 적용**

2) resources밑에 **logback-spring.xml파일**이 없으면

=> **.yml(.properties) **파일 적용****

3) **logback-spring.xml 파일**과 **.yml(.properties)**파일**** **둘다** 있으면

=>**.yml(.properties) **파일**을 먼저 적용 후 **logback-spring.xml 파일 적용**.

---

출처=https://samori.tistory.com/69