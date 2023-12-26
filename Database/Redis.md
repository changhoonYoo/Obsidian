####  목차

1. [Redis (Remote Dictionary Server)](#redis_remote_dictionary_server)
2. [Redis 활용하기 - 캐시(Cache)](#redis_활용하기_-_캐시cache)
    1. [캐쉬(Cache) 란?](#캐쉬cache_란?)
    2. [캐시의 구조 패턴](#캐시의_구조_패턴)
        1. [Look aside Cache 패턴](#look_aside_cache_패턴)
        2. [Write Back 패턴](#write_back_패턴)
    3. [Redis 캐시의 활용 사례](#redis_캐시의_활용_사례)
3. [Redis 활용하기 - 세션 스토어(Session Store)](#redis_활용하기_-_세션_스토어session_store)
    1. [서버 분산 처리 환경에서의 세션 불일치](#서버_분산_처리_환경에서의_세션_불일치)
    2. [In-memory DB vs Disk based DB](#in-memory_db_vs_disk_based_db)
    3. [Redis vs Memcached](#redis_vs_memcached)
        1. [Redis / Memcached 비교](#redis_/_memcached_비교)
        2. [Redis / Memcached 상황에 따른 선택](#redis_/_memcached_상황에_따른_선택)
4. [Redis의 문제점 (주의해야 할점)](#redis의_문제점_주의해야_할점)
    1. [시간 복잡도](#시간_복잡도)
    2. [메모리 파편화](#메모리_파편화)

1. [Redis (Remote Dictionary Server)](#redis_remote_dictionary_server)
2. [Redis 활용하기 - 캐시(Cache)](#redis_활용하기_-_캐시cache)
    1. [캐쉬(Cache) 란?](#캐쉬cache_란?)
    2. [캐시의 구조 패턴](#캐시의_구조_패턴)
        1. [Look aside Cache 패턴](#look_aside_cache_패턴)
        2. [Write Back 패턴](#write_back_패턴)
    3. [Redis 캐시의 활용 사례](#redis_캐시의_활용_사례)
3. [Redis 활용하기 - 세션 스토어(Session Store)](#redis_활용하기_-_세션_스토어session_store)
    1. [서버 분산 처리 환경에서의 세션 불일치](#서버_분산_처리_환경에서의_세션_불일치)
    2. [In-memory DB vs Disk based DB](#in-memory_db_vs_disk_based_db)
    3. [Redis vs Memcached](#redis_vs_memcached)
        1. [Redis / Memcached 비교](#redis_/_memcached_비교)
        2. [Redis / Memcached 상황에 따른 선택](#redis_/_memcached_상황에_따른_선택)
4. [Redis의 문제점 (주의해야 할점)](#redis의_문제점_주의해야_할점)
    1. [시간 복잡도](#시간_복잡도)
    2. [메모리 파편화](#메모리_파편화)

## **Redis (Remote Dictionary Server)**

Redis는 Remote(원격)에 위치하고 프로세스로 존재하는 In-Memory 기반의 Dictionary(key-value) 구조 데이터 관리 Server 시스템이다.

여기서 **key-value** **구조** 데이터란, mysql 같은 관계형 데이터가 아닌 비 관계형 구조로서 데이터를 그저 '키-값' 형태로 단순하게 저장하는 구조를 말한다.

그래서 관계형 데이터베이스와 같이 쿼리 연산을 지원하지 않지만, 대신 데이터의 **고속 읽기와 쓰기에 최적화** 되어 있다.

그래서 Redis는 일종의 **NoSQL 로 분류**되기도 한다.

[![key-value 구조](https://blog.kakaocdn.net/dn/c2ocA4/btrEbExl1vC/JVMpf7i3FQtJHoaTFXn0Q1/img.png)](https://blog.kakaocdn.net/dn/c2ocA4/btrEbExl1vC/JVMpf7i3FQtJHoaTFXn0Q1/img.png)

			
        

>             
> 
> Info
> 
>             
> 
> NoSQL은 Not Only SQL의 약자로써 기존 관계형 데이터베이스(RDBMS)보다 더 융통성 있는 데이터 모델을 사용하고 데이터의 저장 및 검색을 위한 특화된 메커니즘을 제공하는 데이터 저장기술을 의미한다.  
> NoSQL 데이터베이스는 단순 검색 및 추가 작업에 있어서 매우 최적화된 키-값 저장 기법을 사용하여 응답속도나 처리 효율 등에 있어서 매우 뛰어난 성능을 보여준다.
> 
>         

또한 Redis는 **인 메모리(In-Memory) 솔루션**으로도 분류되기도 하는데, 다양한 데이터 구조체를 지원함으로써 DB, Cache, Message Queue, Shared Memory 용도로 사용될 수 있다.

일반 데이터베이스 같이 디스크(ssd)에 데이터를 쓰는 구조가 아니라 메모리(dram)에서 데이터를 처리하기 때문에 **작업 속도가 상당히 빠르다**.

[![인 메모리(In-Memory)](https://blog.kakaocdn.net/dn/c9gOME/btrEkGuAPTR/OxsJ2hee1jdzrekd3YtVY0/img.png)](https://blog.kakaocdn.net/dn/c9gOME/btrEkGuAPTR/OxsJ2hee1jdzrekd3YtVY0/img.png)

|   |   |   |
|---|---|---|
메모리|
RAM|
CPU에서 이루어진 연산을 기록하는 메모리 CPU와 하드디스크를 연결시켜주는 장치|
외부 저장 장치|
HDD, SDD|
컴퓨터의 정보, 문서, 자료 등을 저장하고 읽을 수 있는 장치|

외부 저장 장치를 사용했다면 메모리와 외부 저장 장치와의 병목 현상이 발생했겠지만, 메모리만 사용하기 때문에 데이터 저장 속도가 매우 빠르다.

마지막으로 Redis가 인기 있는 이유는 Java, Python, PHP, C, C++, C#, JavaScript, Node.js, Ruby, R, Go를 비롯한 정말 많은 프로그래밍 언어 프레임워크에 대한 API를 폭넓게 지원하기 떄문이다. (참고 : [http://www.redis.io/clientsVisit Website](http://www.redis.io/clients) )

        

>             
> 
>             
> 
> Redis 탄생 배경에는 Salvatore Sanfilippo라는 이탈리아 해커가   
> MySQL로 어떤 어플을 개발하다가 느려터졌다고 생각해 직접 빠른 서버를 만들어봐야겠다고 생각했고,   
> 그 결과 Redis를 개발하게 되었다는 비하인드 스토리가 있다.
> 
>         

지금까지의 Redis의 대한 특징을 열거하면 다음과 같다.

- NoSql DBMS(비관계형 데이터베이스)로 분류되며 In memory 기반의 Key - Value 구조를 가진 데이터 관리 시스템
- 메모리 기반이라 모든 데이터들을 메모리에 저장하고 조회에 매우 빠르다. (리스트형 데이터 입력과 삭제가 MySQL에 비해서 10배정도 빠름)
- 메모리에 상주하면서 서비스의 상황에 따라 데이터베이스로 사용될 수 있으며, Cache로도 사용될 수 있다.
- Remote Data Storage로 여러 서버에서 같은 데이터를 공유하고 보고 싶을 때 사용할 수 있다.
- 다양한 자료구조 를 지원한다. (Strings, Set, Sorted-Set, Hashes, List ...)
- 쓰기 성능 증대를 위한 클라이언트 측 샤딩(Sharding)을 지원한다.  
    
    - Sharding : 같은 테이블 스키마를 가진 데이터(row)를 다수의 데이터베이스에 분산하여 저장하는 방법
    
- 메모리 기반이지만 Redis는 영속적인 데이터 보존(Persistence)이 가능하다. (메모리는 원래 휘발성)
- 스냅샷 기능을 제공해 메모리 내용을 *.rdb 파일로 저장하여 해당 시점으로 복구할 수 있다.
- 여러 프로세스에서 동시에 같은 key에 대한 갱신을 요청하는 경우, 데이터 부정합 방지 Atomic 처리 함수를 제공한다(원자성)
- Redis는 기본적으로 1개의 싱글 쓰레드로 수행되기 때문에, 안정적은 인프라를 구축하기위해서는 [Replication(Master-Slave 구조)Visit Website](http://redisgate.kr/redis/configuration/replication.php) 필수이다.

---

## **Redis 활용하기 - 캐시(Cache)**

### **캐쉬(Cache) 란?**

Cache란 한번 조회된 데이터를 **미리 특정 공간에 저장**해놓고, 똑같은 요청이 발생하게 되면 서버에게 다시 요청하지 말고 **저장해놓은 데이터를 제공해서 빠르게 서비스를 제공**해주는 것을 의미한다.

즉, 미리 결과를 저장하고 나중에 요청이 오면 그 요청에 대해서 DB 또는 API를 참조하지 않고 Cache를 접근하여 요청을 처리하는 기법이다.

서비스를 처음 운영할 때는 WEB-WAS-DB 정도로 작게 인프라를 구축하는데, 사용자가 늘어나면 DB에 무리가 가기 시작한다.

DB는 데이터를 물리 디스크에 직접 쓰기 때문에 서버에 문제가 발생해도 데이터가 손실되지는 않지만, 매 트랜잭션마다 디스크에 접근해야하므로 부하가 많아지면 성능이 떨어진다.

그래서 사용자가 늘어나면 DB를 **스케일 인** 또는 **스케일 아웃**하는 방식 외에도 **캐시 서버**를 검토하게 된다.

Redis Cache 는 메모리 단 (In-Memory) 에 위치한다.  따라서 용량은 적지만 접근 속도가 빠르다.

다만 저장하려는 데이터 셋이 주어진 메모리 크기보다 크면 디스크를 쓰는 것이 올바른 선택이다.

---

### **캐시의 구조 패턴**

#### **Look aside Cache 패턴**

캐시를 사용하는 패턴은 첫 번째로 Look aside Cache 패턴이다.

위에서 말한 일반적인 캐시 정의를 그대로 구현한 구조이다.

look aside cache는 캐시를 한 번 접근하여 데이터가 있는지 판단한 후, 있다면 캐시의 데이터를 사용하고 없으면 실제 DB 또는 API를 호출한다. 

**[Look aside Cache 쿼리 순서]**

1. 클라이언트에서 데이터 요청
2. 서버에서 캐시에 데이터 존재 유무 확인
3. 데이터가 있다면 캐시의 데이터 사용 (빠른 조회)
4. 데이터가 없다면 실제 DB 데이터에 접근
5. 그리고 DB에서 가져 온 데이터를 캐시에 저장하고 클라이언트에 반환

[![Look aside Cache](https://blog.kakaocdn.net/dn/bnCE2T/btrEjiIoKQG/mFWwKX9FXQ1uyAAG9Zt8q0/img.png)](https://blog.kakaocdn.net/dn/bnCE2T/btrEjiIoKQG/mFWwKX9FXQ1uyAAG9Zt8q0/img.png)

			
        

>             
> 
> Tip
> 
>             
> 
> Cache Miss : 메모리에 찾고자 하는 데이터가 없어서 디스크에 조회 할때  
> Cache Hit : 메모리에 찾고자 하는 데이터가 있을 때
> 
>         

#### **Write Back 패턴**

write back은 주로 **쓰기 작업**이 굉장히 많아서, **INSERT 쿼리를 일일이 날리지 않고 한꺼번에 배치 처리**를 하기 위해 사용한다.

예를 들어 영어 듣기 평가를 온라인으로 진행하는 서비스가 있을 때, 여러 학생이 동시에 제출 버튼을 누르면서 DB에 갑작스럽게 엄청난 쓰기 요청이 몰리게 되면 DB 서버가 죽을 수도 있다.

이때 write back 기반의 캐시를 사용하면 캐시 메모리에 데이터를 저장해 놓고, 이후 DB 디스크에 업데이트 해 주면 안전하게 쓰기 작업을 이행할 수 있는 것이다.

즉, insert 를 1개씩 500번 수행하는 것보다 500개를 한번에 삽입하는 동작이 훨씬 빠르기 때문에 write back 방식은 빠른 속도로 서비스가 가능하다.

하지만 단점도 있다.

DB에서 디스크를 접근하는 횟수가 줄어들기 때문에 성능 향상을 기대할 수 있지만, DB에 데이터를 저장하기 전에 캐시 서버가 죽으면 데이터가 유실된다는 문제점이 있다.

그래서 다시 재생 가능한 데이터나, 극단적으로 heavy 한 데이터에서 write back 방식을 많이 사용한다.

예를 들면 로그를 캐시에 저장하고 특정 시점에 DB 에 한번에 저장하는 경우가 있다.

**[write back 쿼리 순서]**

1. 우선 모든 데이터를 캐시에 싹 저장
2. 캐시의 데이터를 일정 주기마다 DB에 한꺼번에 저장 (배치)
3. 그리고나선 DB에 저장했으니 잔존 데이터를 캐시에서 제거

[![Write Back](https://blog.kakaocdn.net/dn/cVw4uA/btrEkqFULA6/9E2AxrDiyjfFp5fT79MIkk/img.png)](https://blog.kakaocdn.net/dn/cVw4uA/btrEkqFULA6/9E2AxrDiyjfFp5fT79MIkk/img.png)

---

### **Redis 캐시의 활용 사례**

Twitter는 140자 정도의 짧은 글을 올릴 수 있는 소셜 네트워킹 서비스(SNS)이다.

Twitter에서의 Timeline은 사용자가 Follow(구독)하는 사용자들의 최근 트윗을 확인할 수 있는 페이지이다.

2012년 당시 Twitter는 15만명이 넘는 실시간 활동 사용자와 초당 30만 건이 넘는 Timeline 요청이 발생했었다. 

이러한 규모의 Timeline 요청을 데이터베이스에 직접 접근하는 방식으로 처리하면 Query가 복잡해짐에 따라 속도가 현저히 떨어지는 문제가 발생한다.

Twitter는 이 문제를 해결하기 위해 메모리 기반 NoSQL 기술인 Redis를 사용하였다고 한다.

Twitter의 데이터 센터에 존재하는 방대한 양의 Redis Cluster는 각 사용자의 Timeline에 노출될 Tweet의 정보(Tweet ID, 작성자 ID)를 List 형태로 약 800개 정도 캐싱한다. 

발생하는 Timeline 요청은 바로 데이터베이스로 접근하지 않고 Redis에 캐싱된 Timeline 정보를 먼저 가져와서, 이를 토대로 Query를 단순화하여 데이터베이스에 접근하여 처리한다.

[![DBMS/Redis](https://blog.kakaocdn.net/dn/CnHg0/btrEkpmEWrU/EUJRlPamLeJrTWWBDk74T1/img.png)](https://blog.kakaocdn.net/dn/CnHg0/btrEkpmEWrU/EUJRlPamLeJrTWWBDk74T1/img.png)

단, 모든 사용자의 Timeline을 캐싱하게 되면 메모리가 부족해질 수 있으므로, 로그인을 안한 지 30일이 지난 사용자의 Timeline은 Redis Cluster에서 삭제하도록 설정한다.

Redis Cluster에서 삭제된 사용자의 Timeline 정보는 해당 사용자가 다시 로그인을 할 시에 재생성 하게 하는데, 이때 데이터베이스에 직접 접근하는 과정이 필요하기에 시간이 다소 소요될 수도 있다.

이외에도 Redis를 주로 사용하는 곳은 다음과 같다.

- 인증 토큰 등을 저장(Strings or Hash)
- Ranking 보드로 사용(Sorted-Set)
- 유저 API Limit
- 잡큐(list)

---

이처럼, Redis는 특히 Remote Dictionary로서 RDBMS의 **캐시 솔루션**으로 사용 용도가 굉장히 높다.

일반적으로 데이터베이스는 저장장치에 저장이 되는데, 데이터베이스를 조회하려면 저장장치로 i/o가 발생하게 된다.

RDBMS에서 SELECT 쿼리문을 날려 특정 데이터들을 FETCH했을 때, RDBMS의 구조상 DISK에서 데이터를 꺼내오는 데 Memory에서 읽어들이는 것보다 천배 가량 더 느리다.

예를 들어 데이터베이스에 접근하여 10,000개의 레코드를 읽는다고 가정했을 때 disk에 저장되어 있다면 약 30초의 시간이 걸리는 반면 RAM에서 읽을 경우엔 약 0.0002초 밖에 걸리지 않는다.

[![DBMS/Redis](https://blog.kakaocdn.net/dn/b6P8lZ/btrEh71r6Cr/idJJyo2XVks5KmUffkYNm0/img.png)](https://blog.kakaocdn.net/dn/b6P8lZ/btrEh71r6Cr/idJJyo2XVks5KmUffkYNm0/img.png)

따라서 Redis같은 유연한 자료구조를 가지는 인메모리 Key-value 솔루션을 사용하여 DB 부하의 Read 연산의 부하를 분산시키는 데 적용한다.

캐시는 in-memory 방식을 활용하여 데이터를 임시로 저장해두기 때문에 저장장치의 i/o보다 훨씬 빠르게 동작할 수 있다.

그래서 자주 사용하는 데이터는 캐시 서버에서 우선 조회하고 없을 때는 데이터베이스를 다시 조회하는 방식을 활용하면 전체적인 서비스의 속도를 향상시킬 수 있다.

또, 하드한 작업 같은 경우 쿼리문이 길고 복잡해 기본적으로 데이터베이스를 조회하는 시간이 오래 걸리는데, 만일 이 쿼리가 자주 사용되는 경우라면 해당 쿼리가 전체 서비스 속도의 병목이 될 수 있다.

그럴때는 쿼리 결과 자체를 Redis로 캐싱을 해두고, 쿼리의 결과가 바뀔 수 있는 이벤트가 발생할 때마다 캐시에 적재를 새로한다면 전체 서비스 속도를 향상 시킬 수도 있다.

그래서 캐싱이 필요할 때 많이 사용되는데 즉시 **메시지를 주고 받아야 될 때**나, **장바구니의 삭제**와 같은 경우에 많이 사용하는 편이다.

또한 RAM은 휘발성인데 그럼 실행중인 Redis를 끄면 데이터가 전부 날라간다고 생각이 들게 되는데, Redis는 **in-memory** 이지만 **persistent on-disk 데이터베이스** 이기도 하다.

Redis는 특정한 때에 현재까지의 in-memory 상태를 disk에 저장해 두었다가 Redis를 다시 시작했을 때 disk에 저장해 두었던 dump 파일들을 load 하기 때문에 데이터의 손실 발생을 방지할수도 있다.

---

## **Redis 활용하기 - 세션 스토어(Session Store)**

### **서버 분산 처리 환경에서의 세션 불일치**

보통 실무에서는 트래픽 부하를 방지하기 위해 로드밸런서에 서버를 여러대 운영한다.

그러나 서버를 여러대를 운영하게 되면 클라이언트의 세션이 서로 서버마다 달라 서비스 이용에 지장을 줄 수 있다는 문제점을 가지게 된다.

세션 불일치 문제와 해결방안 대한 자세한 내용은 다음 잘 정리된 포스팅을 참고하길 바란다.

>                 [
>                 ![postcard-title](https://scrap.kakaocdn.net/dn/qUo3X/hyOUZkvXcp/ZebY0GgGf1leTKdoocXCt0/img.png?width=800&height=400&face=0_0_800_400,https://scrap.kakaocdn.net/dn/JNVSp/hyOUWg0H4X/kIiOKPTa9k7xfeZNzQpzxk/img.png?width=800&height=400&face=0_0_800_400,https://scrap.kakaocdn.net/dn/bq5ZXq/hyOVZb6ZTE/9teIc7tUm8RiNgNcaETgK1/img.png?width=1200&height=600&face=0_0_1200_600)
>                     ](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-%EC%84%B8%EC%85%98Session-%EB%B6%88%EC%9D%BC%EC%B9%98-%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0%EB%B2%95-%E2%B8%A2%EC%84%9C%EB%B2%84-%EB%8B%A4%EC%A4%91%ED%99%94-%ED%99%98%EA%B2%BD-%E2%B8%A5)
>                 
> 
>                 
> 
>                 [[WEB] 🌐 세션(Session) 불일치 문제 및 해결 방법](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-%EC%84%B8%EC%85%98Session-%EB%B6%88%EC%9D%BC%EC%B9%98-%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0%EB%B2%95-%E2%B8%A2%EC%84%9C%EB%B2%84-%EB%8B%A4%EC%A4%91%ED%99%94-%ED%99%98%EA%B2%BD-%E2%B8%A5)
>                     
> 
>                 
> 
>                 
> 
>                 
>                 inpa.tistory.com
>                     
> 
>                     
> 
>                 
> 
>                 
> 
> 서버 다중화 환경에서의 세션 불일치 단일 서버 환경에서는 session을 통한 로그인을 구현할때 session 불일치 문제를 신경쓸 필요가 없다. 하지만 서비스가 커짐에 따라 한대의 서버로 운영하는것
> 
>                     
> 
>                 

---

### **In-memory DB vs Disk based DB**

별도의 세션 스토리지를 구성하여 세션의 정합성 문제를 해결하려고 할 때, Session storage를 이용하는 방법이 가장 이상적이다.

그럼 세션 스토리지로서 적합한 데이터 베이스는 무엇일까? 

데이터베이스의 데이터가 어느 공간에 저장이 되는가에 따라서 In-memory DB와 Disk based DB로 분류된다.

전자는 데이터를 메모리에 저장하여 관리하고, 후자는 데이터를 디스크에 저장하여 관리한다.

[![In-memory DB vs Disk based DB](https://blog.kakaocdn.net/dn/bVxIxa/btrEiaYEc82/kqqOIlB0b1wqhk1UhLXoU0/img.jpg)](https://blog.kakaocdn.net/dn/bVxIxa/btrEiaYEc82/kqqOIlB0b1wqhk1UhLXoU0/img.jpg)

|   |   |   |
|---|---|---|
in-memory DB|
메모리(RAM)|
속도는 빠르지만 영속성을 보장하지 않고(데이터 유실 가능), 저장 공간이 한정되어 있음|
disk-based DB|
외부 저장 장치(디스크)|
데이터를 읽어 메모리에 올리고, 메모리에 올라간 데이터를 읽기 때문에 속도가 느림|

우리가 만든 웹 서비스를 이용할때 비인가 사용자와 인가된 사용자 모두 접근할 수 있는 요청도 존재하고, 인가된 사용자(로그인 세션이 존재하는 사용자)만이 접근할 수 있는 요청도 존재할 것이다.

이렇게 인가된 사용자만 접근할 수 있는 요청을 처리할때마다 매번 세션 저장소에서 해당 로그인 세션이 존재하는지 확인하는 작업을 진행해야 하기 때문에 성능에 악영향을 주지 않도록 빠르게 세션 정보를 찾아서 제공해야 한다.

Disk based DB는 디스크에서 데이터를 찾아 페이지 단위로 버퍼로 전송하는 시간이 발생하기 때문에 여러 I/O 작업 처리에 있어서 병목 현상이 발생하게 된다.

이러한 이유로 세션이 존재하는지 확인하는 작업을 여러번 반복해야 하는 서비스에서 세션 스토리지로 Disk based DB를 사용하는것은 성능적인 측면에서 올바른 선택이 아니라고 생각된다.

반면, In-memory DB 는 애초에 모든 데이터를 메모리에 저장하기 때문에 Disk I/O 작업이 발생할 일이 없다. 따라서 디스크 기반의 데이터베이스에서 발생하는 병목 현상을 피할 수 있다.

그리고 세션은 **HTTP의 비연결 지향**과 **상태없음(stateless)**과 같은 특성을 보완하기 위해 사용하는 것이다.

세션에는 주로 로그인한 사용자의 정보를 저장하는데, 이 정보는 영원히 저장되어야 하는 정보가 아니다.

거기다 세션에 데이터 유실로 인해 발생하는 피해가 다른 데이터에 비해 적다. 극단적인 예로 세션 저장소의 데이터가 유실된다면 사용자는 재 로그인만 진행하면 된다.

그래서 세션 스토리지로 In-memory DB의 사용이 적절하다.

[![In-memory DB](https://blog.kakaocdn.net/dn/dpbsUp/btrEjiN1vr4/H9yt7KNdna8Kr752oEG9T1/img.png)](https://blog.kakaocdn.net/dn/dpbsUp/btrEjiN1vr4/H9yt7KNdna8Kr752oEG9T1/img.png)

또한 몇 몇 In-memory DB는 세션 스토리지 서버에 문제가 생겨서 데이터가 유실되는 것을 방지하기 위해 동일한 데이터를 또 다른 세션 스토리지에 복사하는 [마스터-슬레이브] 복제 방법을 사용하거나 'Consistent Hashing' 알고리즘을 사용하여 가용성을 보장하기도 한다.

---

### **Redis vs Memcached**

앞서 우리는 세션을 저장하는데 있어, **In-memory DB**를 활용하는 것이 효율적이라는 것을 배웠다.

같은 Relation Database라도 MySQL, MSSQL, Oracle 등의 종류가 있듯이, In-memory DB에도 다양한 데이터베이스들이 존재한다.  
그 중에서도 세션 저장소로 가장 많이 사용하되는 메모리 디비로는 Redis 와 Memcached 가 있다.

두 개의 공통점으로는 In Memory 저장소 라는 점과 Key-value 의 저장 방식을 가지고 있다.

이들의 특징은, 다음과 같다.

- 무료 오픈소스 데이터베이스 이기 때문에 라이센스 비용이 대폭 절약된다.
- sub-milisecond 단위의 높은 응답 속도를 보여주기 때문에 대용량 트래픽을 고려하는 우리 프로젝트에 적절하다.
- Key-Value 형태로 저장되기 때문에 같은 Key-Value 로 저장되는 세션 데이터를 다루는데 적합하다.

그럼 인 메모리 데이터베이스 이면서, 세션을 저장하는데 적합한 key-value 형태로 관리되고 속도도 빠른 이 두 데이터베이스의 차이점은 무엇일까?

[![Redis vs Memcached](https://blog.kakaocdn.net/dn/cSuJ88/btrEmQ417P2/rwRNZxytgYUmQ5DFmSd2yk/img.jpg)](https://blog.kakaocdn.net/dn/cSuJ88/btrEmQ417P2/rwRNZxytgYUmQ5DFmSd2yk/img.jpg)

#### **Redis / **Memcached 비교****

|   |   |   |
|---|---|---|
|
**Redis**|
**Memcached**|
코어|
싱글 코어|
멀티 코어|
자료구조|
Strings, Set,, Sorted-Set, Hashes 등 다양한 자료구조 지원|
Strings, Intergers만 지원|
데이터 저장|
Memory, Disk|
Memory|
속도|
읽기, 쓰기 속도가 Memcached보다 느림  <br>하지만 큰 차이는 없음|
디스크를 거치지 않기 때문에 읽기, 쓰기 속도가 Redis보다 빠름|
복제|
Master-Slave, Multi-Master Replication 방식 지원|
지원 X|
내구성|
Memcached보다 내구성이 뛰어남|
Redis보다 내구성이 떨어짐|
영속성|
영속성 데이터 사용|
지원 X|
파이셔닝 방법|
샤딩 지원|
지원 X|
메모리 재사용|
메모리를 재사용하지 않음, 명시적으로만 데이터 제거 가능|
저장소 메모리를 재사용. 만료전에 더 이상 데이터를 넣을 메모리가 없으면 LRU 알고리즘에 따라 데이터 삭제|
만료일 지정 방식|
만료일을 지정하면 만료된 데이터는 캐시처럼 사라짐|
동일|

  

[![Redis vs Memcached](https://blog.kakaocdn.net/dn/vMoMp/btrD8NvxSDP/uUuV5tkpn4AUwGpT3EPOEK/img.png)](https://blog.kakaocdn.net/dn/vMoMp/btrD8NvxSDP/uUuV5tkpn4AUwGpT3EPOEK/img.png)[![Redis vs Memcached](https://blog.kakaocdn.net/dn/DdKgZ/btrEcP6KRUF/TbBOUTzI2MQjZRWYUDT5P0/img.png)](https://blog.kakaocdn.net/dn/DdKgZ/btrEcP6KRUF/TbBOUTzI2MQjZRWYUDT5P0/img.png)[![Redis vs Memcached](https://blog.kakaocdn.net/dn/5jNOs/btrEdqFpMhC/HvovHfDgfcrNdOGqFD6uO1/img.png)](https://blog.kakaocdn.net/dn/5jNOs/btrEdqFpMhC/HvovHfDgfcrNdOGqFD6uO1/img.png)[![Redis vs Memcached](https://blog.kakaocdn.net/dn/LKHCq/btrD7FYWXza/mSTkDqDPrURvkugKmQbhK0/img.png)](https://blog.kakaocdn.net/dn/LKHCq/btrD7FYWXza/mSTkDqDPrURvkugKmQbhK0/img.png)

  

그래프 값이 낮을수록 좋은 것임

해당 표에서 볼 수 있듯이 Memcached의 경우 Write 연산에 있어서, Redis 보다 좋은 성능을 보임을 알 수 있다.

하지만, Read 연산에 있어서는 Redis가 더 좋은 성능을 보인다.

이를 비교해보았을 때, Redis가 세션을 저장하는 데 더 적합하다는 점을 알 수 있다.

세션 관련 작업의 경우, 쓰기 연산보다는 읽기 연산이 압도적으로 높은 작업이기 때문이다.

마지막으로 Redis가 Read/Write에 있어서 메모리를 더 효율적으로 사용하는 것을 볼 수 있다.

위 그래프 결과를 종합한 결과, 쓰기 성능에서는 Memcached가 앞서지만 메모리 사용 효율과 처리속도, 그리고 이외의 다양한 자료구조 지원과 아키텍쳐 지원을 모두 고려한다면 Redis가 상대적으로 더 나은 선택임을 알 수 있다. 

#### ****Redis / **Memcached**** 상황에 따른 선택**

**MemCached**

1. 메모리가 삭제되어도 원본 데이터로 복구가 가능하며 장애가 발생하지 않는 경우
2. 단순 조회로 통신 속도만을 향상시키는 목적인 경우

**Redis**

1. 메모리가 삭제되었을 때 서비스 장애가 발생할 수 있는 경우
2. 서비스의 특정 기능을 위한 목적으로 사용하는 경우 ( 다양한 데이터 타입을 활용해서 사용할 때 등 )

---

## **Redis의 문제점 (주의해야 할점)**

그러나 Redis에도 설계 구조상 단점이 존재한다.

### **시간 복잡도**

레디스는 자바스크립트와 같이 **싱글 쓰레드** 기반으로 돌아간다.

그래서 한 번에 딱 하나의 명령어만 실행하기 때문에, 긴 처리시간이 필요한 명령어를 쓰면 불리하고 요청 건을 처리하기 전까지 다른 서비스 요청을 받아들일수 없고 서버가 다운 되는 현상이 일어 날 수 있다.

따라서 전체 데이터를 다루는 시간복잡도를 가진 O(N) 명령어 ~~keys~~ ~~flush~~ ~~getall~~ 는 주의해서 사용할 필요가 있다.

[![시간 복잡도](https://blog.kakaocdn.net/dn/9eoEz/btrEzouoemv/5eIOxsx9CnA0AGBu7tmDW0/img.png)](https://blog.kakaocdn.net/dn/9eoEz/btrEzouoemv/5eIOxsx9CnA0AGBu7tmDW0/img.png)

			
        

>             
> 
> Tip
> 
>             
> 
> **대표적인 O(N) 명령 및 사례**  
>  - KEYS, FLUSHALL, FLUSHDB, Delete COlLECTIONS, Get All Collections  
>  - 모니터링 스크립트가 일초에 한번씩 keys 호출  
>  - 큰 컬렉션의 데이터를 다 가져오는 경우  
> -- Access Token 저장을 List(O(N)) 자료구조를 통해서 이루어짐 -> 삭제시에 모든 item을 찾아봐야함, 최근에는 Set을 통해 가져오도록 패치됨  
> - keys는 scan 명령어로 사용하는 것으로 하나의 긴명령을 짧은 여러 명령으로 변경 가능
> 
>         

### **메모리 파편화**

메모리를 할당 받고 해제 하는 과정에서 아래 그림과 같이 부분부분 빈 공간이 생기게 되는데, 4번같이 새로운 메모리를 할당할때 알맞는 공간(4칸)이 없기 때문에 마지막부분(우측) 부분에 프로세스가 위치해야 되고, 그러면 빈 공간 메모리가 남아 낭비가 발생하게 된다.

그리고 이 현상이 계속되면 실제 physical 메모리가 커져 프로세스가 죽는 현상이 발생 할 수도 있다.

또한 쓰기 연산이 copy on wirte 방식으로 동작하기 때문에 최대 메모리를 2배 이상까지 사용하기도 한다.

그래서 redis를 사용할때 메모리를 적당히 여유있게 사용하는 것이 좋다.

[![메모리 파편화](https://blog.kakaocdn.net/dn/NQWUa/btrEw7fOVv8/IRizBdZBSQ0beC8JhMoMg0/img.png)](https://blog.kakaocdn.net/dn/NQWUa/btrEw7fOVv8/IRizBdZBSQ0beC8JhMoMg0/img.png)

https://workinprogress.kr/wiki/programming/do-not-feed-the-gc/