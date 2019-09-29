
![springbatch](https://user-images.githubusercontent.com/7076334/65817494-86df2280-e242-11e9-81bb-f9d468076a5b.jpg)

우아한 세미나는 이번까지 5번 신청한것 같은데 드디어 당첨이 되서 가게 되었다.
회사 근처기도 하고 평소에 동영상을 보면 꽤 많은 인사이트를 얻을 수 있을 것 같아서이다.

회사 끝나고 바로 갔는데도 시간이 부족해서 저녁을 먹지 못했는데 감사하게도 빵과 음료수를 제공해주셨다.
![20190926_192700](https://user-images.githubusercontent.com/7076334/65833801-1d791580-e30f-11e9-8aef-5c83574fdedc.jpg)

이번에 주제는 스프링 배치였고 회사에서 Tasklet과 Step 방식으로 사용하고 있는 부분이 있었기 때문에 관심이 갔다.
보통 스프링4.0 이상인 경우 conifg 방식의 애노테이션을 많이 사용한다고 하는데, 아직 회사에서는 XML 방식으로 많이 쓰고 있는 것 같다.
이번에 소개한 스프링 배치들도 대부분다 애노테이션 기반의 예제였다.


![20190926_193504](https://user-images.githubusercontent.com/7076334/65833805-24078d00-e30f-11e9-986f-421e55540e16.jpg)
### 기본편

![20190926_193813](https://user-images.githubusercontent.com/7076334/65833873-01c23f00-e310-11e9-9f0e-cf605ca34269.jpg)
Batch 애플리케이션이란?
- 컴퓨터에서 사람과 상호 작용 없이 이어지는 프로그램들의 실행이다.


Web과 Batch 애플리케이션의 차이
- Web :  실시간 처리 / 상대적 속도 / QA 용이성
- Batch : 후속 처리 / 절대적인 속도 / QA 복잡성
  - Batch인 경우 QA 분들이 직접 테스트 하기 어렵기 때문에 테스트 코드를 꼭 짜야된다고 한다.


Batch와 Quartz의 차이
- Batch : 단발성으로 대용량의 데이터를 처리하는 어플리케이션
- Quartz : Quartz는 스케줄러의 역할이지, Batch와 같이 대용량 데이터 배치 처리에 대한 기능을 지원하지 않는다.
  - Quartz는 Batch의 보안제 역할이지 대체제가 아니다.
  - Batch 역시 Quartz의 다양한 스케줄 기능을 지원하지 않아서 보통은 Quartz + Batch를 조합해서 사용한다고 한다.


Batch 애플리케이션이 필요한 상황
- 일정 주기로 실행되어야 할 때
- 실시간 처리가 어려운 대량 데이터를 처리할 때
  - Batch 는 대용량 데이터 처리가 절대적인 요구 사항
  
  
![20190926_194705](https://user-images.githubusercontent.com/7076334/65834065-2a4b3880-e312-11e9-85c4-0b2da669be48.jpg)

Tasket과 Reader, Processor, Writer
- Tasklet과 Reader, Processor, Writer는 따른 개념이 아니다
  - ChunkOrientedTasklet도 결국 Tasklet 의 구현체이다.


### 활용편

![20190926_201219](https://user-images.githubusercontent.com/7076334/65834169-52876700-e313-11e9-9f36-1f7eab571b5e.jpg)

관리 도구들
- Cron (가장 구시대적 방식)
- Spring Batch Admin (뭔가 이름만 봐서는 공식적으로 지원되는 툴 같지만 Deprecated 됨)
- CI Tools (Jenkins)

Jenkins 장점
- 가장 추천하는 방식이고 현재 다니고 있는 회사도 Jenkins를 이용한 SpringBatch를 관리하고 있다.
- Integration (Slack, Email)
- 실행 이력 / 로그 관리 / Dashboard
- 다양한 실행 방법 (Rest API / 스케줄링 / 수동 실행)
- 계정 별 권한 관리
- 파이프라인
- Web UI + Script 둘다 사용 가능
- Plugin (Ansible Github, Logentries 등)


![20190926_202845](https://user-images.githubusercontent.com/7076334/65834258-418b2580-e314-11e9-8b93-c536cc31a816.jpg)
무중단 배포
- readlink를 사용


Jenkins 파이프라인
- Job들 여러개를 연결해서 사용할 수 있다고 한다.
- SpringBatch의 Step 대신 사용할 수 있다.
  - 발표하신분은 Step 대신 Jenkins 파이프라인을 선호하신다고 했는데 Step은 중간에 필요한 Job만 독립적으로 수행하기 어렵다고 한다.


발표자 분은 SpringBatch가 QA분이 직접 테스트 하기 어렵기 때문에 테스트 코드를 많이 짜는 편인데 이상하게도 전체 테스트 코드 수행 시, 완료 시간이 오래 걸려서 확인해 보니 Environment가 변경될 때마다 Spring Conext를 재시작하는 현상을 발견했다고 한다.


![20190926_204832](https://user-images.githubusercontent.com/7076334/65834415-7ba8f700-e315-11e9-8bfd-9e60f03a7c84.jpg)
Environment가 변경되는 조건
- 테스트 코드에서 @MockBean / @SpyBean 사용할때
- 테스트 코드에서 @TestPropertySource로 환경변수 변경할때
- @ConditionalOnProperty로 테스트마다 Config가 다를때



![20190926_205231](https://user-images.githubusercontent.com/7076334/65834414-7ba8f700-e315-11e9-9d8c-5a59a8774f0d.jpg)
해결방법
- ApplicationConext를 이용하여 직접 Job의 Bean을 조회 해온다.
  - 여러 테스트 코드에서도 호출해도, 결국 Spring Context는 한번만 기동된다. (Spring도 바보가 아닌 이상 ...)


![20190926_210433](https://user-images.githubusercontent.com/7076334/65834488-61234d80-e316-11e9-81e8-cc79789c3a98.jpg)
SpringBatch에서 JPA를 사용할 경우 발생하는 N+1 문제에서도 얘기해 주셨는데 이 부분은 조금 이해를 못해서 나중에 좀 더 찾아 봐야 될 것 같다.

JPA N+1 문제
- Join Fetch를 이용하면 해결
- 하위 엔티티 2개 종류 이상에서 Join Fetch 사용 시 MultipleBagFetchException이 발생 
  - Hibernate default_batch_fetch_size로 해결

참고) https://jojoldu.tistory.com/414


### 후기
![20190926_210433](https://user-images.githubusercontent.com/7076334/65834599-82386e00-e317-11e9-961a-f8d292642f5f.jpg)
마지막에 SpringBatch 관련 외국 서적을 추천해 주셨는데 2020년 경에 발표자분이 직접 쓰고 있는 SpringBatch 관련 책이 출간된다고 한다. (국내 SpringBatch 서적은 최초!)
내가 사용하고 있던 SpringBatch는 일부분이라는 것을 느꼈고 이렇게 잘 활용할 수 있다는 것을 알았다. 
쉬는 시간에 찍은 배민 조형물로 마무리 하겠다.
그럼 20000~

![20190926_200331](https://user-images.githubusercontent.com/7076334/65834069-30d9b000-e312-11e9-9f45-28780bcc192b.jpg)

