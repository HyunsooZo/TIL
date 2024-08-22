# 🚑 Spring Batch

### **Batch 의 정의 및 조건**

Batch Processing이란 프로그램 흐름에 따라 순차적으로 자료를 처리하는 방식을 뜻함. 어떠한 요청을 실시간이 아닌 일괄/대량 처리 하는 것을 의미한다.

배치 애플리케이션이 만족해야 할 몇 가지 조건이 있는데 다음과 같다.

**대용량 데이터** : 대량의 데이터를 전달, 계산, 가져오기 등의 처리 \
**자동화** : 심각한 문제 해결을 제외하고는 사용자 개입 없이 실행  \
**견고성** : 잘못된 데이터를 충돌/중단 없이 처리 \
**신뢰성** : 이슈 추적 (logging, 알림)\
**성능** : 지정된 시간 안에 처리를 완료하거나 동시에 실행되는 다른 애플리케이션을 방해하지 않음

### **Batch 프로세스 전체 흐름 (**[**출처**](https://terasoluna-batch.github.io/guideline/5.0.0.RELEASE/en/Ch02\_SpringBatchArchitecture.html)**)**

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

### **스프링 배치 (Spring Batch)**

* **Spring Batch**는 대규모 데이터 처리 작업을 java기반의 배치 처리 어플리케이션을 \
  만들기 위한 프레임워크
* 대용량 데이터를 처리하는 경우나 주기적인 업무 일괄 처리 등에 사용
* **Spring Batch**는 배치 작업을 트랜잭션 처리와 함께 관리하고, \
  실패한 작업을 복구하고 재시작할 수 있는 기능을 제공
* 잡(Job), 스텝(Step), 리더(Reader), 프로세서(Processor), 라이터(Writer) 등의 개념을 \
  이용하여 배치 처리를 설계하고 실행

### **쿼츠 (Quartz)**

* 쿼츠는 자바 기반의 오픈 소스 스케줄링 라이브러리
* 스케줄링 작업을 관리하고 실행할 수 있는 기능을 제공
* 주기적으로 실행되어야 하는 작업이나 예약된 작업을 관리하고 실행하는 데 사용
* 쿼츠는 크론 표현식을 사용하여 실행 스케줄을 설정하고, \
  다양한 트리거(trigger)를 통해 작업을 실행할 수 있음
* 분산 환경에서도 사용할 수 있는 확장 가능한 아키텍처를 가지고 있어, \
  대규모 시스템에서도 유연하게 활용됨

### **예시 코드**

#### [**의존성 추**](#user-content-fn-1)[^1]**가**

<pre class="language-groovy"><code class="lang-groovy"> dependencies { 
   // Spring Batch 의존성: 대량 데이터 처리 및 배치 작업 정의 
   implementation 'org.springframework.boot:spring-boot-starter-batch'
    // Quartz 의존성: 작업 스케줄링을 위한 라이브러리
<strong>   implementation 'org.springframework.boot:spring-boot-starter-quartz'
</strong><strong> }
</strong></code></pre>

#### **SpringBatch Job 설정**

```java
@Configuration
public class BatchConfig {

    // Spring Batch 작업(Job)을 정의하는 메서드
    @Bean
    public Job job(
        JobBuilderFactory jobBuilderFactory, 
        StepBuilderFactory stepBuilderFactory
    ) {
        // Job은 여러 Step으로 구성됨
        return jobBuilderFactory.get("exampleJob")
                .start(stepBuilder(stepBuilderFactory)) // 첫 번째 Step을 시작으로 정의
                .build();
    }

    // 배치 작업의 Step을 정의하는 메서드
    @Bean
    public Step stepBuilder(
        StepBuilderFactory stepBuilderFactory
    ) {
        return stepBuilderFactory.get("exampleStep")
                .<String, String>chunk(10) // 데이터를 청크 단위로 처리, 여기선 10개씩
                .reader(reader()) // 데이터를 읽는 로직
                .processor(processor()) // 데이터를 처리하는 로직
                .writer(writer()) // 처리된 데이터를 쓰는 로직
                .build();
    }

    // 데이터 읽기 역할을 하는 Reader
    @Bean
    public ItemReader<String> reader() {
        // 간단한 리스트를 데이터 소스로 사용
        return new ListItemReader<>(Arrays.asList("item1", "item2", "item3"));
    }

    // 데이터 처리 역할을 하는 Processor
    @Bean
    public ItemProcessor<String, String> processor() {
        // 데이터를 대문자로 변환하는 간단한 처리 로직
        return item -> item.toUpperCase();
    }

    // 처리된 데이터를 출력하는 Writer
    @Bean
    public ItemWriter<String> writer() {
        // 처리된 데이터를 콘솔에 출력
        return items -> items.forEach(System.out::println);
    }
}
```

#### **Quartz 스케줄러 설정**

```java
@Configuration
public class QuartzConfig {

    // Quartz에서 실행할 작업(Job)을 정의하는 JobDetail
    @Bean
    public JobDetail jobDetail() {
        return JobBuilder.newJob(BatchJobLauncher.class) // BatchJobLauncher 클래스를 Quartz 작업으로 설정
                .withIdentity("batchJob") // 작업의 고유 이름을 설정
                .storeDurably() // JobDetail을 지속적으로 유지 (삭제되지 않도록)
                .build();
    }

    // Quartz 트리거를 정의하여 특정 시간에 Job을 실행
    @Bean
    public Trigger trigger(
            JobDetail jobDetail
    ) {
        return TriggerBuilder.newTrigger()
                .forJob(jobDetail) // 위에서 정의한 JobDetail과 연결
                .withIdentity("batchTrigger") // 트리거의 고유 이름을 설정
                .withSchedule(CronScheduleBuilder.cronSchedule("0 0/1 * * * ?")) // 매 분마다 실행하는 크론 스케줄 설정
                .build();
    }
}
```

#### **Quartz Job 구현**

```java
// Quartz Job을 구현하여 배치 작업을 실행하는 역할
public class BatchJobLauncher extends QuartzJobBean {

    // Spring Batch의 JobLauncher를 주입 받아 사용
    @Autowired
    private JobLauncher jobLauncher;

    // 실행할 배치 작업(Job)을 주입 받아 사용
    @Autowired
    private Job job;

    // Quartz가 트리거될 때 실행되는 메서드
    @Override
    protected void executeInternal(
        JobExecutionContext context
    ) throws JobExecutionException {
        try {
            // JobParameters는 매 실행마다 고유하게 설정 (여기서는 현재 시간을 기반으로 설정)
            JobParameters params = new JobParametersBuilder()
                    .addLong("time", System.currentTimeMillis())
                    .toJobParameters();
            // Spring Batch 작업 실행
            jobLauncher.run(job, params);
        } catch (Exception e) {
            // 작업 실행 중 예외 발생 시 Quartz에 예외 전달
            throw new JobExecutionException(e);
        }
    }
}
```

위 예시코드를 작성 후 애플리케이션을 실행하면, \
Quartz가 매 분마다 Spring Batch 작업을 트리거하여 배치 작업이 실행될 것이다. \
이 예시는 간단한 구조지만, 실제로는 훨씬 더 복잡한 배치 작업과 스케줄링 요구사항을 처리할 수 있다.

[^1]: 
