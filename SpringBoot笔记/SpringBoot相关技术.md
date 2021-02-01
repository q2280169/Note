# SpringBoot 相关技术

## 事务处理

## Druid

## 表单验证

表单验证校验用户提交的数据的合理性。

* @Null 限制只能为 null
* @NotNull 限制不为null
* @AssertFalse 限制必须为 false
* @AssertTrue 限制必须为 true
* @DecimalMax(value) 限制必须为一个不大于指定值 value 的数
* @DecimalMin(value) 限制必须为一个不小于指定值 value 的数
* @Digits(integer, fraction) 限制必须为一个小数，且整数部分的位数不能超过 inieger，小数部分的位数不能超过 fraction。
* @Future 限制必须为一个将来的日期
* @Max(value) 限制必须为一个不大于指定值 value 的数
* @Min(value) 限制必须为一个不小于指定值 value 的数
* @Past 限制必须为一个过去的日期
* @Pattern(value) 限制必须符合指定的正则表达式
* @Size(min, max) 限制字符长度必须为 min~max
* @NotEmpty 限制元素值不为 null 且 不为空
* @NotBlank 限制元素值不为空
* @Email 限制元素值是 Email

## 文件操作

### 文件上传

```java
// 上传单个文件，IO流方式
public String upload(MultipartFile file) {
        if (!file.isEmpty()) {
            try {
                String fileName = file.getOriginalFilename();
                File f = new File(fileName);
                FileOutputStream fileOutputStream = new FileOutputStream(f);
                BufferedOutputStream out = new BufferedOutputStream(fileOutputStream);
                out.write(file.getBytes());
                out.flush();
                out.close();
            }
            catch (FileNotFoundException e) {
                e.printStackTrace();
                return "上传失败," + e.getMessage();
            }
            catch (IOException e) {
                e.printStackTrace();
                return "上传失败," + e.getMessage();
            }
            return "上传成功";
        }
        else {
            return "上传失败，因为文件是空的.";
        }
    }

// 上传单个文件，transferTo()方法
@PostMapping("/upload")
    public String upload(MultipartFile uploadFile, HttpServletRequest request){
        // 文件保存路径
        String path = request.getSession().getServletContext().getRealPath("/uploadFile");
        File folder = new File(path);
        if(!folder.isDirectory()){
            folder.mkdir();
        }
        // 文件名称
        String name = uploadFile.getOriginalFilename();
        try{
            uploadFile.transferTo(new File(folder, name));

        }catch (IOException e){
            e.printStackTrace();
        }
        return "上传失败";
    }

// 上传多个文件，IO流方式
public String fileUploadMulti(HttpServletRequest request){
        List<MultipartFile> files = ((MultipartHttpServletRequest)request).getFiles("file");
        if(!files.isEmpty()){
            MultipartFile file = null;
            BufferedOutputStream stream = null;
            for (int i =0; i< files.size(); ++i) {
                file = files.get(i);
                if (!file.isEmpty()) {
                    try {
                        stream =new BufferedOutputStream(new FileOutputStream(new File(file.getOriginalFilename())));
                        stream.write(file.getBytes());
                        stream.close();
                    }
                    catch (Exception e) {
                        return "上传失败" + e.getMessage();
                    }
                }
            }
            return "上传成功";
        }
        else{
            return "上传失败，因为文件是空的.";
        }
    }

// 上传多个文件，transferTo()方法
@PostMapping("/upload")
    public String upload(MultipartFile[] uploadFiles, HttpServletRequest request){
        for(MultipartFile uploadFile : uploadFiles){
            // 文件保存路径
            String path = request.getSession().getServletContext().getRealPath("/uploadFile");
            File folder = new File(path);
            if(!folder.isDirectory()){
                folder.mkdir();
            }
            // 文件名称
            String name = uploadFile.getOriginalFilename();
            try{
                uploadFile.transferTo(new File(folder, name));

            }catch (IOException e){
                e.printStackTrace();
            }
        }
        return "上传成功";
    }
```

### 文件下载

```java
public void download(HttpServletResponse resp) throws IOException {
    String fileName = "test.txt";
    String pathName ="D:/";
    File file = new File(pathName+fileName);
    resp.setHeader("content-type", "application/octet-stream");
    resp.setContentType("application/octet-stream");
    resp.setHeader("Content-Disposition", "attachment;filename=" + fileName);
    byte[] buff = new byte[1024];
    BufferedInputStream bis = null;
    OutputStream os = null;
    try {
        os = resp.getOutputStream();
        bis = new BufferedInputStream(new FileInputStream(file));
        int i = bis.read(buff);
        while (i != -1) {
            os.write(buff, 0, buff.length);
            os.flush();
            i = bis.read(buff);
        }
    }
    catch (IOException e) {
        e.printStackTrace();
    }
    finally {
        if (bis != null) {
            try {
                bis.close();
            }
            catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```



## 异常处理

### 简单配置页面

可以在 resources/static 目录下创建 error 目录，在其中创建静态错误展示页面；也可以在 classpath:templates/ 目录下创建 error 目录，在其中创建动态错误展示页面；

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\错误页面目录.png" style="zoom:67%;" />

## CORS 支持

CORS 是由 W3C 制定的一种跨域资源共享技术标准，其目的就是为了解决前端的跨域请求。

```java
// 细粒度配置
@PostMapping("/")
@CrossOrigin(value="http://localhost:8081", maxAge=1800, allowedHeaders="*")
public String addBook(String name){
    return "receive" + name;
}

// 全局配置
@Configuration
public class MyWevMvcConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/book/**")
                .allowedHeaders("*")
                .allowedMethods("*")
                .maxAge(1800)
                .allowedOrigins("http://localhost:8081");
    }
}
```

## 拦截器

拦截器中的方法按 **preHandle Controller postHandle afterCompletion** 的顺序执行。

```java
// 创建拦截器
public class MyInterceptor1 implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, 
                             HttpServletResponse response, 
                             Object handler){
        System.out.println("MyInterceptor1 >> preHandle");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, 
                           HttpServletResponse response, 
                           Object handler, 
                           ModelAndView modelAndView) throws Exception {
        System.out.println("MyInterceptor1 >> postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, 
                                HttpServletResponse response, 
                                Object handler, 
                                Exception ex) throws Exception {
        System.out.println("MyInterceptor1 >> afterCompletion");
    }
}

// 配置拦截器
@Configuration
public class MyWevMvcConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new MyInterceptor1())
                .addPathPatterns("/**")
                .excludePathPatterns("/hello");
    }
}
```

## 启动系统任务

```java
@Component
@Order(1)
public class MyCommandLineRunner1 implements CommandLineRunner{
    @Override
    public void run(String... args) throws Exception {
        System.out.println("Runner1 >> " + Arrays.toString(args));
    }
}

@Component
@Order(2)
public class MyCommandLineRunner2 implements CommandLineRunner{
    @Override
    public void run(String... args) throws Exception {
        System.out.println("Runner2 >> " + Arrays.toString(args));
    }
}
```

<img src="C:\Users\GaoWei\Desktop\SpringBoot笔记\图片\命令参数.png" style="zoom: 67%;" />

## 路径映射

有一些页面在控制器中不需要加载数据，只是完成简单的跳转，可以直接配置路径映射，提高访问速度。

```java
@Configuration
public class MyWevMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/login").setViewName("login");
        registry.addViewController("/index").setViewName("index");
    }
}
```

## AOP

* Joinpoint（连接点）：类里面可以增强的方法。
* Pointcut（切入点）：对连接点进行拦截的定义。
* Advice（通知）：拦截点连接点之后要做的事情。
* Aspect（切面）：Pointcut 和 Advice 的结合。
* Target（目标对象）：要增强的类。

```

```

