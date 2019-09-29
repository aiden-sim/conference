
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
  - Batch의 보안제 역할이지 대체제가 아니다.
  - Batch 역시 Quartz의 다양한 스케줄 기능을 지원하지 않아서 보통은 Quartz + Batch를 조합해서 사용한다고 한다.



배치 애플리케이션이 필요한 상황
-일정 주기로 실행되어야 할 때
-실시간 처리가 어려운 대량 데이터를 처리할 때

배치는 대용량 데이터 처리가 절대적인 요구 사항
(프레임워크 단위로 지원을 한다.)

스프링 배치에서는 모든 데이터를 메모리에 쌓는 방법을 권장하지 않는다.

Tasklet과 reader, processor, writer는 따른 개념이 아니다
ChunkOrientedTasklet => Tasklet 의 구현체이다.

Job / Step / Tasklet 등등

Spring Batch의 JobParameter은 Enum LocaDatetime, LocalDate 등은 지원 안된다.
==> 형변환 해야 한다. ㅠㅠ

@JobScope와 @StepScope는 Late Binding (늦은 할당) 이라고 한다.
==> reader, writer 동적으로 변경하기 위해 , 로그, 유효성체크 등은 공통로직



### 활용편

관리 도구들
-Jenkins

Jenkins 장점
-Integration (Slack, Email)
-실행 이력 / 로그 관리 / Dashboard
-다양한 실행 방법 (Rest API / 스케줄링 / 수동 실행)
-계정 별 권한 관리
-파이프라인
-Web UI + Script 둘다 사용 가능
-Plugin (Ansible Github, Logentries 등)

무중단 배포
readlink


파이프라인
-Job들 여러개를 연결 시켜 주는것
-Step 대신 사용할 수 있다.

멱등성
-연산을 여러번 적용하더라도 결과가 달라지지 않는 성질


테스트 코드
-배치 애플리케이션은 테스트 하기 힘들기 때문에 테스트 코드를 짜기 더 힘들다.


Environment가 변경되는 조건
-테스트 코드에서 @MockBean / @SpyBean 사용할때
-테스트 코드에서 @TestPropertySource로 환경변수 변경할때
-@ConditionalOnProperty로 테스트마다 Config가 다를때


JPA & Spring Batch
-JPA N + 1
=> join fetch
=> 하위 엔티티 2개 종류 이상에서 Join Fetch 사용 시 MultipleBagFetchException 이 발생 (default_batch_fetch_size로 해결) - hibernate

소량일 경우 HibernateCusror
대량일 경우 JpaPagingItemReader
