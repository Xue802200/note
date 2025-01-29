客户端发送到服务器端的 HTTP 请求：HttpServletRequest

服务器端发送请求到客户端 ：HttpServletResponse

# HTTP请求：

### 组成

  http请求协议由三部分组成：请求行（第一行  请求方式，请求路径，请求协议版本），请求头（键值对），请求正文（传递给服务器的数据）
  get没有请求正文，在网址后面，post有请求体
  get请求中，请求正文紧跟在？后面

###  格式：

​          请求行
​          请求头1
​          请求头2
​          。。。
​          请求空行
​          请求体
请求体以一个方法符号开头，以空格分开，后面跟着请求的URI和协议版本。
   格式如下 Method Request-URI HTTP-Version CRLF
​          Method 表示请求方法
​          Request-URI 是一个统一的资源标识符
​          HTTP-Verson表示请求的HTTP协议版本
​          CRLF表示回车和换行

# HTTP响应：

###    格式：

​          响应行（HTTP版本，响应状态码，状态码）
​          响应头1
​          响应头2
​          。。。
​          响应空行
​          响应体（响应正文）

# 三层框架：

controller（响应层）,service（服务层）,dao(对数据的处理)层->mapper
                   请求响应                  逻辑处理                数据访问操作

# mybatis入门：

##     1.准备工作

（创建springboot项目工程，数据库表user，实体类User）
            在包中创建pojo.User类，User类的属性要与数据库中的信息的数据类型保持一致

##     2.引入Mybatis的相关依赖，配置Mybatis

​            在properties的文件中导入配置信息，要包含驱动类名称，数据库连接的url，连接数据库的用户名，连接数据库的密码

##     3.编写SQL语句

​             在包中创建mapper.UserMapper类，通过注解的方式编写sql语句，在类钱要添加Mapper注解，在类中的方法上要添加@select注解，里面的内容表示sql语句

# JDBC： 

##     本质：

一套操作所有关系数据库的规范，即接口
             各个数据库厂商去实现这套接口，提供数据库驱动jar包
             我们可以用这套接口（JDBC）编程，真正执行的代码是驱动jar包中的实现类

# 数据库连接池：

## 本质： 

​    是一个容器，负责分配，管理数据库连接
​    允许应用程序重读使用一个现有的数据库连接，而不是再重新创建一个
​    释放空间时间超过最大空闲时间的连接，来避免因为没有释放连接而引起的数据库连接遗漏

## 要求：

​    实现标准接口：Datasource
​    实现Connection getconnection（） throws SQLException 中的这个getconnection方法

##  用法：

Druid  ，Hikari（springboot'默认）

## 切换数据池的操作：

   引入Druid的依赖
   在properties中配置数据库的连接信息

# Lombok ：解决实体类User中代码的过于臃肿

###  用法：

​        引入lombok的依赖
​        在实体类钱添加@data注解，在实体类中只需要写属性的信息

### 内容：

​     @Getter/@Setter 为所有的属性提供get/set方法
​     @ToString           会给类自动生易阅读的toString方法
​     @EuqalsAndHashCode    根据类所拥有的非静态字段自动重写equals方法和hashCode方法
​     @Data                             提供了更综合的生成代码功能(@Getter+ @Setter+@ToString+@EqualsAndHashcode) 
​      data注解包含前四个 
​     @NoArgsConstructor      为实体类生成无参的构造器方法
​     @AllArgsConstructor       为实体类生成除了static修饰的字段之外带有各参数的构造器方法，

# Mybatis基础操作：

## 1.准备工作：

​     准备数据库表emp
​     创建一个新的springboot工程，选择引入对应的起步依赖（mybatis，mysql驱动，lombok）
​     application.properties中引入数据库连接信息
​     创建对应的实体类Emp（实体类属性采用驼峰命名）
​     准备Mapper接口EmpMapper

### 删除操作： 

​     1.在mapper类的EmpMapper接口中新建一个名为delete的方法，改方法的注释为@Delete()，括号里面就是要进行删除操作的sql语句 
​     2.delete的方法形参为 Integer id，表示通过主键Id的信息去进行删除，sql语句：delete from emp where id=#{ id },避免sql注入的问题
​     3.在test类当中，首先申明一个EmpMapper接口的对象，并通过依赖注入的方式，在改方法钱要添加注释@Autowired，自动分类实现该类的对象名称为empMapper，通过对象名.方法名的形式去实现删除操作     

### 新增操作：类似于删除操作

​     insert方法的形参应该为实体类对象emp，在sql语句中 values后跟的值应为#{ }...这样的形式，

### 新增（主键返回）

​     在sql语句上加入注解@Options（keyProperty = "id",useGeneratedKeys=true）  会自动将生成的主键值赋值给emp对象的id属性

### 查询：方法同上

​     PS：实体类属性名和数据库表查询返回的字段名一致，mybatis会自动封装
​            如果实体类名和数据库表查询返回的字段名不一致，不能自动封装
   方案1：给字段起别名 select username, name, gender, image, job, entrydate, dept_id deptID, create_time createTime, update_time updateTime from emp 
   方案2：通过@Results @Result注解,column表示数据库中的字段名，property表示实体类的属性名
​        @Results({
​        @Result(column = "dept_id",property = "deptId"),
​        @Result(column = "create_time",property = "createTime"),
​        @Result(column = "update_time",property = "updateTime")
​    })
   方案3：开启mybatis的驼峰命名自动映射开关  
​          在pom.xml文件中添加相关配置信息

## 通过XML文件来进行SQL语句的书写

​     规范：
​        1.XML映射文件的名称与Mapper接口名称一致，并且将XML映射文件和Mapper接口放置在相同包下(同包同名)
​                 在mybatis中文网中找到相关配置信息，粘贴到xml文件的表头中
​                 在resource文件夹中新建包时，注意.yong/去代替
​        2.XML映射文件的namespace属性为Mapper接口全限定名一致
​                  复制引用
​        3.XML映射文件中的sql语句的id与Mapper接口的方法名一致，并保持返回类型一致
​                  返回值类型为单挑记录所封装的类型，list<Emp>即为Emp

         @Param("name")String name

## 动态SQL:

   查询操作：
   <mapper namespace="com.itheima.mapper.EmpMapper">
    <select id="select" resultType="com.itheima.pojo.Emp">
        select id,username,password,name,gender,image,job,entrydate,dept_id,create_time,update_time from emp
        <where>
        <if test=" name != null">
               name like concat('%',#{name},'%')
        </if>
        <if test="gender != null">
               and gender=#{gender}
        </if>
        <if test="entrydate1 != null and entrydate2 != null">
            and entrydate between #{entrydate1} and #{entrydate2}
        </if>
        </where>
    </select>
</mapper>
        表示的是com.itheima.mapper.EmpMapper包下的select'方法
        <where></where>会自动判断if内的语句，有一个成立就会自动生成where,全都不成立则不会生成where
        会动态判断sql语句前的and 和 or是否保存

##   操作步骤：

​    1.在mapper层中定义相应的方法
​    2.在resource中新建一个目录与mapper层所在的目录名相同，.用/去替代，新建一个文件，文件名与对应的mapper接口名相同
​    3.在mybatis官网中copy相关约束粘贴到对应的xml文件中，在约束下新建一个mapper标签，在第一个mapper标签下新添一个属性namespace，属性值为对应mapper接口的全类名
​    4.根据sql语句新建对应的标签，eg：select语句则新建一个select标签，update则新建一个update标签, 标签有两个属性id和resultType，id名与mapper接口里的方法名保持一致，resultType与方法的单体返回值的全类名保持一致
​    5.select标签内写select语句，将sql语句内的固定值换成动态值（方法的形参），用#{}表示
​    6.在select语句内添加if标签，if标签添加一个属性为test，test后添加要判断的条件
​     



##   删除操作：

​     <!-- 批量删除员工（18,19,20）-->
​    <!--
​       collection :遍历的集合
​       item       ：遍历出来的元素
​       separator  ：分隔符
​       open       ：遍历开始前拼接的SQL片段
​       close      ：便利结束后拼接的SQL片段
​    -->
​    <delete id="deleteByIds">
​            delete from emp where id in
​            <foreach collection="ids" item="id" separator="," open="(" close=")">
​                            #{id}
​            </foreach>
​    </delete>

sql & include
 <sql id="commonSelect">
        select id,username,password,name,gender,image,job,entrydate,dept_id,create_time,update_time from emp
    </sql>

   <include refid="commonSelect"/>

## Restful风格

   get：查询
   post：新增
   put：修改
   delete：删除

前后端交互统一响应结果
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Result{
      private Integer code;  //响应码 1代表成功  0代表失败
      private String msg;    //响应信息 描述字符串
      private Object data;  //返回的数据
      public static Result success() {
             return new Result(1,"success",null);
    }
      public static Result success(Object data){
             return new Result(1,"success",data)
     }
     public static Result error(String msg){
             return new Result(0,msg,null);
     }
}

请求响应数据的类型如果是json形式的，需要用@RequestBody注解的一个实体类去接受
服务端返回给前端的值就是controller中方法的返回值
如何要给controller层方法的形参设置默认值  @RequestParam(defaultValue = "num") num为默认值

# 文件上传：

##    简介：

​        将本地图片，视频，音频等文件上传到服务器，供其他用户浏览或下载的过程
​        发微博，发微信朋友圈都用到了文件上传
前端页面三要素：  表单项 type =“”“file” 表单提交方式=post    表单的enctype属性 multipart/form-data
服务端接收文件：MultipartFile 
​     String getOriginalFilename()        获取原始文件名
​     void transferTo(File dest)             将接受的文件转存到磁盘中
​     long getSize()                              获取文件大小，单位：字节
​     byte[] getBytes()                          获取文件内容的字节数组
​     InputStream getInputStream()     获取接收到的文件内容的输入流

##   本地存储：

​         在服务端，接收到上传上来的文件之后，将文件存储在本地服务器磁盘中
​         uuid（通用唯一识别码） 49cdf420-a0f5-4303-97f7-06eb49bcf500   UUID类
​         在SpringBoot中，文件上传，默认单个文件允许最大大小为1M。如果需要上传大文件，可以进行如下配置：
​               #配置单个文件最大上传大小
​                spring.servlet.multipart.max-file-size=10MB
​               #配置单个请求最大上传大小（一次请求可以上传多个文件）
​                spring.servlet.multipart.max-request-size=100MB

##    阿里云OSS-使用步骤

​       注册阿里云-充值-开通对象存储服务（OSS）-创建bucket-获取AccessKey（密钥）-参照官方SDK编写入门程序-案例集成OSS
​       bucket：存储空间是用户用于存储对象（Object，就是文件）的容器，所有的对象都必须隶属于某个存储空间
​       SDK：Software Development Kit的缩写，软件开发工具包，包括辅助软件开发的依赖（jar包），代码示例等，都可以叫做SDK
​       将sdk导入到idea中，将相关信息更改即可

###### 参数配置化：用@Value注解进行操作

yml配置文件：
      用法：
                  在resource文件夹下新建文件application.yml或者application.yaml文件，在文件内部添加所要配置的文件信息
      基本语法：
                1.大小写敏感
                2.数值前必须有空格，作为分隔符
                3.使用缩进表示层级关系，缩进时，不允许使用tab键，只能用空格（idea中会自动将tab转化为空格）
                4.缩进的空格数目不重要，只要相同层级的元素左侧对齐即可
                5.#表示注释，从这个字符一直到行尾，都会被解析器忽略
      用法：
		#自定义对象/map集合
		user:
  		name: Tom
 		 age: 20
 		 address: beijing
		#自定义数组List/Set
          	hobby:
			- java
			- C
			- game
				-sport
     常见配置文件格式对比：
                  XML 
                          <server>
                                <port>8080</port>
                
```yaml
                            <address>127.0.0.1</address>
            ​          </server>
              properties
            ​          server.port=8080
            ​         server.address=127.0.0.1
               yml/yaml
            ​        server:
            ​            port: 8080
            ​             address: 127.0.0.1
```

##    	@ConfigurationProperties（prefix = "")的用法

### 		用处：

​			依赖注入配置文件内的变量时，防止@Value（#{}）的过度使用

### 		用法：

​			以教程为例，在utils包下有一个类名为AliOSSUtils，类中的变量为连接阿里云所必须的属性值，属性值在application.yml中已经设置完成，要想完成依赖注入，避免过度使用
​			@Value注解，选择@ConfigurationProperties（prefix = "")，首先在utils包下新建一个类为AliOSSProperties类似充当于实体类，在该类中新建四个变量，分别代表连阿里云所必须的属性值，在类 
​			上添加注解@Data，@Component表示该类成为IOC容器中的一个bean，在AliOSSUtils中通过依赖注入的方式添加该对象，通过该对象的get和set方法获取各个属性值即可。

# 登录

## 	登录认证

### 会话技术

会话：用户打开浏览器，访问web服务器的资源，会话建立，直到有一方断开连接，会话结束。在一次会话中可以包含多次请求和响应

会阿跟踪：一种维护浏览器状态的方法，服务器需要识别多次请求是否来自同一个浏览器，以便再同一次会话的多次请求中共享数据

会话跟踪方案：客户端会话跟踪技术：Cookie  服务端会话跟踪技术：Session    令牌技术

## 客户端会话跟踪技术：Cookie

### 原理

当浏览器第一次发送请求给服务端中的某个接口时，接口执行完成之后会生成一个cookie，cookie中存储着信息

当服务端给浏览器响应时，会将cookie传给客户端，客户端会将cookie保存在本地

当客户端再次发送请求时，会将cookie'一并传送，此时服务端会判断这个cookie存不存在，并作出相关操作

### 如何请求和响应？

请求头：Cookie ：name=value

响应头Set-Cookie :name=value

### 用法

```java
//设置cookie，存储值
//服务器响应给浏览器才会存储
@GetMapping("/c1")
public Result cookie1(HttpServletResponse response) {
    //设置参数及其对应的值,打印到日志中
    response.addCookie(new Cookie("login_username","itheima"));
    log.info("cookie1",response.hashCode());

    return Result.success();
}

//获取cookie
//浏览器请求服务端时,浏览器会获取cookie
@GetMapping("c2")
public Result cookie2(HttpServletRequest request){
    //获取每一个cookie对象
    Cookie[] cookies = request.getCookies();

    //遍历cookies，找到符合条件的值
    for(Cookie cookie:cookies){
        if(cookie.getName().equals("login_username")){
            System.out.println(cookie.getValue());
        }
    }
    return Result.success();
}
```

优缺点

优点：HTTP协议中支持的技术

缺点：移动端APP无法使用Cookie     不安全，用户可以自己禁用cookie   cookie不能跨域

## 服务端会话跟踪技术：Session

### 原理

浏览器第一次发送请求给服务端时，服务端会自动生成Session对象，并且具有唯一标识符成为Session ID

服务端响应给浏览器时，会通过Cookie的方式将Session ID发送给浏览器

浏览器再次发送请求时，会将Session ID传给服务器，服务器会将这个ID和已经存在的Session对象进行匹配

### 如何请求和响应

请求：Cookie ：JESSIONID=1

响应：Set-Cookie ：JESSIONID=1

### 用法

```java
//session存储
//浏览器请求服务端时，在服务端存储
@GetMapping("s1")
public Result session1(HttpSession session){
    //设置存储对象,打印在日志上
    session.setAttribute("login_username","itheima");
    log.info("session1",session.hashCode());

    return Result.success();
}

//读取session的值
//服务器响应时,浏览器会读取session中的ID
@GetMapping("/s2")
public Result session2(HttpServletRequest request){
    //获取所有的session,打印在日志上
    HttpSession session = request.getSession();
    log.info("session2",session.hashCode());

    //根据login_username找到匹配的值,打印在日志上
    Object loginUsername = session.getAttribute("login_username");
    log.info("loginUsername",loginUsername);

    return Result.success(loginUsername);
}
```

### 优缺点：

优点：存储在服务器，安全

缺点：服务器集群环境下无法直接使用Session   Cookie的缺点

## 令牌技术

### 原理：

将原来的JSON格式的数据进行一系列的封装

### 用法

1.未导入工具包,生成和解析

```Java
@Test
public void testGenJWT(){
    Map<String, Object> claims = new HashMap<>();
    claims.put("sub", "admin");
    claims.put("login","xzj");
    String jwt = Jwts.builder()
            .signWith(SignatureAlgorithm.HS256, "itheima") //设置密钥和算法
            .setClaims(claims) //添加有效载荷
            .setExpiration(new Date(System.currentTimeMillis() + 3600 * 1000))
            .compact();
    System.out.println(jwt);
}

@Test
public void testParseJwt(){
    Claims claims = Jwts.parser()
            .setSigningKey("itheima") //传递密钥
            .parseClaimsJws("eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhZG1pbiIsImxvZ2luIjoieHpqIiwiZXhwIjoxNzM2ODM0NDgxfQ.PH5jeSZ3iY7AeOCh8rnbgIekQlmQGYrCB22dEgZnuIk")
            .getBody();
    System.out.println(claims);
}
```

2.导入相关的 工具包，可以直接使用

```java
@RestController
@Slf4j
public class LoginController {
    @Autowired
    private EmpService empService;
    //登录
    @PostMapping("/login")
    public Result login(@RequestBody Emp emp) {
        log.info("登录的用户为:{}",emp);
        Emp e = empService.getEmpByIdAndPassword(emp);
        //对e进行判断
        //不为null，则下放一个JWT令牌
        if(e!= null){
            Map<String, Object> claims = new HashMap<>();
            claims.put("name", e.getName());
            claims.put("id", e.getId());
            claims.put("username", e.getUsername());
            String jwt = JwtUtils.generateJwt(claims);
            return Result.success(jwt);
        }

        //e不存在，则登录失败。返回对应的消息
        return Result.error("用户名或密码错误");
    }
}
```

### 优缺点

优点：支持PC端，移动端    解决集群环境下的认证问题   减轻服务器端存储压力

缺点：

需要自己实现

## 登录校验——Filter校验器

### 作用

可以把对资源的请求拦截下来，从而实现一些特殊的功能

一般完成一些通用的操作，eg：登录校验，统一编码处理，敏感字符处理

### 用法

1.定义Filter：定义一个类，实现Filter接口，重写其所有的方法

2.配置Filter：Filter类上加@WebFilter注解，配置拦截资源的路径。引导类上加@ServletComponentScan开启Servlert组件支持

```java
@WebFilter(urlPatterns = "/*")
public class DemoFilter implements Filter {

    //初始化方法，Web服务器启动，创建Filter时调用，只调用一次
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("init方法被执行了！");
    }

    //拦截打请求时，调用改方法，可调用多次。
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("Demo拦截到了请求,放行前逻辑...");
        //放行
        filterChain.doFilter(servletRequest, servletResponse);
        System.out.println("Demo拦截到了请求,放行后逻辑...");
    }

    //销毁请求，服务器关闭时调用，只调用一次
    @Override
    public void destroy() {
        System.out.println("destory方法被执行了！");
    }
}
```

### 执行过程

当第一次启动Filter时，容器会调用init方法进行初始化操作并且只执行一次，在filter类上加上@WebFilter注解标注要被拦截的方法

在浏览器发送请求时，会首先执行filter中的方法，如果需要放行，则先执行接口的方法，执行完之后再执行filter中doFilter后面的方法

请求->放行前逻辑->放行->资源->放行后逻辑

![image-20250116151114103](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250116151114103.png)

### 拦截路径

![image-20250116151228535](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250116151228535.png)

### 过滤器链

一个web应用中，配置了多个过滤器，就形成了一个过滤器链

### 流程

![image-20250116161015241](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250116161015241.png)

1.获取url

2.判断是否包含login，如果有则放行，说明是登陆操作

3.获取请求头中的token

4.判断token是否存在，不存在则返回错误结果(未登录)

5.解析token，如果是错的，则返回错误结果(未登录)

6.放行

```Java
@WebFilter(urlPatterns = "/*")
@Slf4j
public class LoginCheckerFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest req=(HttpServletRequest)servletRequest;
        HttpServletResponse rep=(HttpServletResponse)servletResponse;

        //1.获取url路径
        String url = req.getRequestURL().toString();
        log.info("url为：{}",url);

        //2.判断url中是否包含login，包含则放行，是登陆操作
        if(url.contains("login")){
            log.info("url为:{},包含login,登录放行...",url);
            filterChain.doFilter(servletRequest,servletResponse);
            return;
        }

        //3.获取请求头中的令牌(token)
        String jwt = req.getHeader("token");
        log.info("jwt令牌为:{}",jwt);

        //4.判读jwt令牌是否存在，不存在返回错误信息(NOT_LOGIN)
        //ps:返回的数据类型为JSON,需要手动转换
        if(!StringUtils.hasLength(url)){
            log.info("jwt令牌不存在");
            Result error=Result.error("NOT_LOGIN");
            //手动转换 对象->json----------->阿里巴巴fastJSON
            String json = JSONObject.toJSONString(error);
            //将手动转换的这个json返回给客户端
            rep.getWriter().write(json);
            return;
        }

        //5.解析json，解析错误的话，返回错误信息(NOT_LOGIN)
        try {
            JwtUtils.parseJWT(jwt);
        } catch (Exception e) { //捕捉到错误则表示：jwt令牌不存在，返回报错信息
            log.info("jwt令牌:{} 解析有误,请检查",jwt);
            Result error=Result.error("NOT_LOGIN");
            String json = JSONObject.toJSONString(error);
            rep.getWriter().write(json);
            return;
        }

        //6.放行
        log.info("令牌合法，放行...");
        filterChain.doFilter(servletRequest,servletResponse);
    }
}
```

## 拦截器Interceptor

### 概念

是一种动态拦截方法调用的机制，类似于过滤器。Spring框架中提供的，用来动态拦截控制器方法的执行

### 作用

拦截请求，在指定的方法调用前后，根据业务需要执行预先设定的代码

使用步骤

1.定义拦截器，实现HandlerInterceptor接口，并重写其方法

2.注册拦截器

```Java
@Component
public class LoginCheckInterceptor implements HandlerInterceptor {
    @Override //目标资源方法(一般为controller中的方法)前执行，返回true：放行  返回false：不放行
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle...");
        return true;
    }

    @Override //目标资源方法执行后执行
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle...");
    }

    @Override  //视图渲染完毕后执行，最后执行
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion...");
    }
}

-----------------------------------------------------------------------------------------------------------------------------------------

@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Autowired
    private LoginCheckInterceptor loginCheckInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loginCheckInterceptor).addPathPatterns("/**");
    }
}

```

### 拦截

![image-20250116204219093](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250116204219093.png)

### 执行流程

![image-20250116212351130](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250116212351130.png)

# 事务

## 事务回顾

### 概念

事务是一组操作的集合，它是一个不可分割的工作单位，这些操作要么同时成功，要么同时失败

### 操作

开启事务（一组操作开始前，开启事务）:start transaction / begin;

提交事务（这组操作全部成功后，提交事务）:commit

回滚事务（中间任何一个操作出现失误，回滚事务）：rollback

## spring-事务

### 注解

@Transactional  

位置：业务(Service)层的方法上，类上，接口上

作用：将当前方法交给spring进行事务管理，方法执行前，开启事务；成功执行完毕，提交事务；出现异常，回滚事务

### 事务属性-回滚  rollbackFor

rollbackFor：默认情况下，只有出现RuntimeException才会回滚异常。rollbackFor属性用于控制出现何种异常类型，回滚事务。

```Java
//删除部门信息
//ps:删除部门信息,需要将这个部门下的员工中的deptID都设为null
@Transactional(rollbackFor = Exception.class)  //spring事务管理
@Override
public void delDept(Integer id) throws Exception {
    log.info("删除部门的id为{}",id);
    deptMapper.deleteDept(id);  //删除部门信息
    if (true) {
        throw new Exception("我出错啦...");
    }
    empMapper.setEmpDeptIdNull(id);  //将被删除部门的员工中的deptId属性设置为NULL
}
```

### 事务传播行为-propagation 

事务传播行为指的就是当一个事务方法被另一个事务方法调用时，这个事务方法应该如何进行事务控制

![image-20250121222902247](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250121222902247.png)

# AOP

## 概念

Aspect Oriented Programming（面向切面编程，面向方面编程），其实就是面向特定方法编程

## 场景

![image-20250122141902881](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250122141902881.png)

## 实现

动态代理是面向切面编程最主流的实现。而SpringAOP是Spring框架的高级技术，旨在管理bean对象的过程中，主要通过底层的动态代理机制，对特定的方法进行编程。

1.在pom.xml文件中导入相关的依赖

2.新建一个类，完成相应的逻辑，在方法上加入切入点表达式表示对哪些方法起作用

```Java
@Component
@Aspect  //AOP类
@Slf4j
public class TimeAspect {
    //分析方法的执行花费多长时间
    @Around("execution(* com.itheima.service.*.*(..))") //切入点表达式
    public Object timeAspect(ProceedingJoinPoint joinPoint) throws Throwable {
        //1.获取当前时间
        long begin = System.currentTimeMillis();

        //2.运行原始方法
        Object proceed = joinPoint.proceed();

        //3.获取结束时间
        long end = System.currentTimeMillis();

        //将信息打印在日志上
        log.info( joinPoint.getSignature()+"方法执行的时间为:{}ms", end - begin);
        return proceed;
    }
}
```

## 核心概念

连接点：JoinPoint ，可以被AOP控制的方法（暗含方法执行时的相关信息）

通知：Advice，指哪些重复的逻辑，也就是共性功能（最终体现为一个方法）

切入点：PointCut，匹配连接点的条件，通知仅会在切入点方法执行时被应用

切面：Aspect，描述通知与切入点的对应关系（通知+切入点）

目标对象：Target，通知所应用的对象

![image-20250122145802354](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250122145802354.png)

# AOP进阶

### 通知类型

![image-20250122162407929](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250122162407929.png)

![image-20250122184501554](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250122184501554.png)

PS：![image-20250122184851393](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250122184851393.png)

可以在方法的最上面申明一个void的方法，加上注解@Pointcut，在下面使用的过程中直接使用方法pt()即可

### 通知顺序

1.不同切面类中，默认按照切面类的类名字母排序：

目标方法前的通知方法：字母排名靠前的先执行

目标方法后的通知方法：字母排名靠前的后  执行

2.用@Order(数字)加在切面类上来控制顺序

目标方法前的通知方法：数字小的先执行

目标方法后的通知方法：数字小的后执行

### 切入点表达式

#### 作用

决定有哪些方法需要加入通知

### execution

![image-20250122202702632](C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250122202702632.png)

### annotation注解

1.在aop包中新建一个自定义注解MyLog

```Java
@Target(ElementType.METHOD) //表示当前注解作用在方法上
@Retention(RetentionPolicy.RUNTIME)  //表示当前注解作用在程序运行的过程中
public @interface MyLog {
}
```

2.在要被作用的方法上加上这个MyLog注解

3.在annotation中将被注解的方法加上去

@Pointcut("@annotation(com.itheima.aop.MyLog)")

@PointCut：注解  ：将公共的切点表达式抽取出来，需要用到时引用该切点表达式即可

```Java
@Pointcut("execution(* com.itheima.service.impl.DeptServiceImpl.* (..))")
public void pt(){}
```

```Java
@Around("com.itheima.aop.TimeAspect.pt()")
public Object recordTime(ProceedingJoinPoint joinPoint) throws Throwable {
}
```

### 连接点

<img src="C:\Users\simple\AppData\Roaming\Typora\typora-user-images\image-20250122211810881.png" alt="image-20250122211810881" style="zoom:%;" />

```Java
@Slf4j
@Aspect
@Component
public class ExampleAspect {
    @Pointcut("execution(* com.itheima.service.DeptService.* (..))")
    private void pt(){}

    @Around("pt()")
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        log.info("MyAspect around before...");

        //1.获取目标对象的类名
        String name = joinPoint.getTarget().getClass().getName();
        log.info("目标对象的类名:{}",name);

        //2.获取目标方法的方法名
        String methodName = joinPoint.getSignature().getName();
        log.info("目标方法的方法名:{}",methodName);

        //3.获取目标方法运行时传入的参数
        Object[] args = joinPoint.getArgs();
        log.info("目标方法传入的参数为:{}", Arrays.toString(args));

        //4.放行目标方法执行
        Object result = joinPoint.proceed();
        
        //5.将方法的返回值返回
        log.info("这个方法的返回值为:{}",result);

        log.info("MyAspect around after...");
        return null;
    }
}
```