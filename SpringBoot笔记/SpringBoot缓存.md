# SpringBoot 缓存

## Redis 单机缓存

**添加缓存依赖**

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>

<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

**缓存配置**

```properties
#缓存配置
spring.cache.cache-names=c1,c2
spring.cache.redis.time-to-live=1800s
#Redis配置
spring.redis.database=0
spring.redis.host=192.168.66.129
spring.redis.port=6379
spring.redis.password=123@456 
spring.redis.jedis.pool.max-active=8 
spring.redis.jedis.pool.max-idle=8
spring.redis.jedis.pool.max-wait=-1ms 
spring.redis.jedis.pool.min-idle=0
```

**开启缓存**

```java
@SpringBootApplication
@EnableCaching
public class RediscacheApplication {
    public static void main(String[] args) {
        SpringApplication.run(RediscacheApplication.class, args);
    }
}
```

**创建 BookDao**

```java
@Repository
public class BookDao {
    @Cacheable("c1")
    public Book getBookById(Integer id) {
        System.out.println("getBookById");
        Book book = new Book();
        book.setId(id);
        book.setName("三国演义");
        book.setAuthor("罗贯中");
        return book;
    }
}
```

**创建测试类**

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class RediscacheApplicationTests {

    @Autowired
    BookDao bookDao;

    @Test
    public void contextLoads() {
        bookDao.getBookById(1);
        bookDao.getBookById(2);
        bookDao.getBookById(3);
    }
}
```

## Redis 集群缓存

