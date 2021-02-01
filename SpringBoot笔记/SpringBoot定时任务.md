# 定时任务

## @Scheduled

```java
// 开启定时任务
@SpringBootApplication
@EnableScheduling
public class ScheduledApplication {
    public static void main(String[] args) {
        SpringApplication.run(ScheduledApplication.class, args);
    }
}

// 配置定时任务
@Component
public class MySchedule {
    @Scheduled(fixedDelay = 1000)
    public void fixedDelay() {
        System.out.println("fixedDelay:"+new Date());
    }
    
    @Scheduled(fixedRate = 2000)
    public void fixedRate() {
        System.out.println("fixedRate:"+new Date());
    }
    
    @Scheduled(initialDelay = 1000,fixedRate = 2000)
    public void initialDelay() {
        System.out.println("initialDelay:"+new Date());
    }
    
    @Scheduled(cron = "0 * * * * ?")
    public void cron() {
        System.out.println("cron:"+new Date());
    }
}
```

## Quartz

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-quartz</artifactId>
</dependency>
```

```java
// 开启定时服务
@SpringBootApplication
@EnableScheduling
public class ScheduledApplication {
    public static void main(String[] args) {
        SpringApplication.run(ScheduledApplication.class, args);
    }
}

// 创建 Job 的两种方法
@Component
public class MyFirstJob {
    public void sayHello() {
        System.out.println("MyFirstJob:sayHello:"+new Date());
    }
}

public class MySecondJob extends QuartzJobBean {
    private String name;
    public void setName(String name) {
        this.name = name;
    }
    @Override
    protected void executeInternal(JobExecutionContext context){
        System.out.println("hello:"+name+":"+new Date());
    }
}

// 配置 JobDetail 和 Trigger
@Configuration
public class QuartzConfig {
    @Bean
    MethodInvokingJobDetailFactoryBean jobDetail1() {
        MethodInvokingJobDetailFactoryBean bean =
                new MethodInvokingJobDetailFactoryBean();
        bean.setTargetBeanName("myFirstJob");
        bean.setTargetMethod("sayHello");
        return bean;
    }
    @Bean
    JobDetailFactoryBean jobDetail2() {
        JobDetailFactoryBean bean = new JobDetailFactoryBean();
        bean.setJobClass(MySecondJob.class);
        JobDataMap jobDataMap = new JobDataMap();
        jobDataMap.put("name","sang");
        bean.setJobDataMap(jobDataMap);
        bean.setDurability(true);
        return bean;
    }
    @Bean
    SimpleTriggerFactoryBean simpleTrigger() {
        SimpleTriggerFactoryBean bean =
                new SimpleTriggerFactoryBean();
        bean.setJobDetail(jobDetail1().getObject());
        bean.setRepeatCount(3);
        bean.setStartDelay(1000);
        bean.setRepeatInterval(2000);
        return bean;
    }
    @Bean
    CronTriggerFactoryBean cronTrigger() {
        CronTriggerFactoryBean bean =
                new CronTriggerFactoryBean();
        bean.setJobDetail(jobDetail2().getObject());
        bean.setCronExpression("* * * * * ?");
        return bean;
    }
    @Bean
    SchedulerFactoryBean schedulerFactory() {
        SchedulerFactoryBean bean = new SchedulerFactoryBean();
        SimpleTrigger simpleTrigger = simpleTrigger().getObject();
        CronTrigger cronTrigger = cronTrigger().getObject();
        bean.setTriggers(simpleTrigger,cronTrigger);
        return bean;
    }
}
```

