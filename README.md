# Java-Web-note
## Maven

### 概述

Maven提供了一套标准化的项目结构，所有IDE使用Maven构建的项目结构完全一样，所有IDE创建的Maven项目都可以通用

即：Maven制定了一套项目结构标准，可以让各个编译器通用该项目结构

### maven环境配置

```
MAVEN_HOME : maven安装的路径
path路径下 : %MAVEN_HOME%\bin
```

### 确认是否配置成功 

cmd输入`mvn -version`

### 配置本地仓库

修改conf/setting.xml中的<localRepository>为一个指定的目录，用于存放maven所需jar包

### 配置镜像仓库

修改conf/setting.xml中的<mirrors>标签，为其添加如下子标签：

```html
<mirror>
  <id>alimaven</id>
  <name>aliyun maven</name>
  <url>http://mevan.aliyun.com/nexus/content/groups/public/</url>
  <mirrorOf>central</mirrorOf>
</mirror>
```

### 常用命令

- compile：编译
- clean：清理
- test：测试
- package：打包
- install：安装

### 生命周期

- Maven构建项目生命周期描述的是一次构建过程经历经历了多少个事件

- Mevan对项目构建的生命周期划分为三套
  - clean：清理工作
  - default：核心工作，例如编译，测试，打包，安装等
  - site：产生报告，发布站点等

### IDEA配置Maven

![image-20231203111324824](./assets/image-20231203111324824.png)

### maven坐标

- 坐标

  - Maven中的坐标是资源的唯一标识

  - 使用坐标来定义项目或引入项目中需要的依赖

- Maven坐标主要组成
  - groupld:定义当前Maven项目隶属组织名称(通常是域名反写，例如: com.itheima)
  - artifactld:定义当前Maven项目名称(通常是模块名称,例如order service、goods service)
  - version:定义当前项目版本号版本

### 依赖管理

1. 在pom.xml中按`alt`+`Fn`+`insert`，选择Dependency
2. 在弹出的面板中搜索对应坐标，然后双击选中对应坐标
3. 点击刷新按钮，使坐标生效

### 依赖范围

通过设置坐标的依赖范围，可以设置对应jar包的作用范围：编译环境、测试环境、运行环境

```html
<dependency>
    <groupId>junit</ groupId>
    <artifactId>junit</artifactId>
    <version>4.13</version>
    <scope>test</scope>
</dependency>
```

![image-20231203133236517](./assets/image-20231203133236517.png)

<scope>默认值：compile

## MyBatis

用于简化JDBC开发

### 持久层

- 负责将数据到保存到数据库的那一层代码
- JavaEE三层架构：表现层、业务层、持久层

### 框架

- 框架就是一个半成品软件，是一套可重用的、通用的、软件基础代码模型
- 在框架的基础之上构建软件编写更加高效、规范、通用、可拓展

### Mapper代理开发

1. 定义与SQL映射文件同名的Mapper接口，并且将Mapper接口和SQL映射文件放置在同一目录下
2. 设置SQL映射文件的namespace属性为Mapper接口全限定名
3. 在Mapper接口中定义方法，方法名就是SQL映射文件中sql语句的id，并保持参数类型和返回 值类型一致 

### 核心配置文件

MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的顶层结构如下：

- configuration（配置）
  - [properties（属性）](https://mybatis.net.cn/configuration.html#properties)
  - [settings（设置）](https://mybatis.net.cn/configuration.html#settings)
  - [typeAliases（类型别名）](https://mybatis.net.cn/configuration.html#typeAliases)
  - [typeHandlers（类型处理器）](https://mybatis.net.cn/configuration.html#typeHandlers)
  - [objectFactory（对象工厂）](https://mybatis.net.cn/configuration.html#objectFactory)
  - [plugins（插件）](https://mybatis.net.cn/configuration.html#plugins)
  - environments（环境配置）
    - environment（环境变量）
      - transactionManager（事务管理器）
      - dataSource（数据源）
  - [databaseIdProvider（数据库厂商标识）](https://mybatis.net.cn/configuration.html#databaseIdProvider)
  - [mappers（映射器）](https://mybatis.net.cn/configuration.html#mappers)

**注意：配置文件需保证属性的先后顺序**

### 查询

#### 查询所有&结果映射

由于数据库中字段命名和Java代码中的命名可能不一致，故可在SQL语句文件中使用resultMap

即：将SQL语句的返回结果改为resultMap格式，在SQL语句上定义resultMap标签，在resultMap中写明result标签

再在result标签中写明列标签和属性，如：

```html
<resultMap id="ResultMap" type="返回值对应的类">
	<result column="数据库中字段" property="类中属性" />
</resultMap>
```

#### 查看详情

参数占位符

- `#{}` ： 会将其替换为？. 为了防止SQL注入
- `${}`  : 拼SQL。会存在SQL注入问题

参数传递时使用`#{}`，表名或列名不固定的情况下使用`${}`

例如

```html
<select id="selectById" resultMap="userResultMap">
    select *
    from tb_user where if = #{id};
</select>
```

传入SQL语句时，由于SQL语句写在XML文件，所以对于 `<` 而言是有报错的

所以，对于这些特殊字符的处理有以下两种方式

- 转义字符：将特殊字符进行转义
- CDATA区：输入CD自动创建CDATA区，在标签里写入特殊字符。

#### 条件查询

参数接收

1. 散装参数：如果方法中有多个参数，需要使用@Param("SQL参数占位符名称")
2. 对象参数：对象的属性名称要和参数占位符名称一致
3. map集合参数

##### 散装参数

接口中写入函数

```java
List<brand> selectByCondition(@Param("status")int status,@Param("companyName")String compantName,@Param("brandName")String brandName)
```

返回值类型为brand，每个参数之前标注注释@Param，后面跟随标签中SQL语句占位符的名称，然后写入默认传入的参数。

##### 对象参数

新建对象然后给该对象指定参数，传参只需传入该对象即可。

##### map集合参数

作为双列集合，使用put方法指定其键值对，随后传入map对象即可。

以上方法的缺陷是必须传入全部参数，缺少任意一个参数均会返回NULL。所以需要动态查询

#### 动态条件查询

以上方法对于查询时有缺点，解决方法为动态查询

##### 多条件动态查询

使用if标签和where标签，如

```html
<select id="selectByCondition" resultMap="brandResuleMap">
    select *
    from tb_user
    <where>
    	<if test="status != null">
            status = #{status}
        </if>
        <if test="company_name != null and company_name != '' ">
            company_name like #{company_name}
        </if>
        <if test="brandName != null and brandName != '' ">
            brandName like #{brandName}
        </if>
    </where>
</select>
```

注：对于xml语句来说，使用`and`表示并且。

##### 单条件查询

对于xml中的标签而言，choose相当于switch，when相当于case，otherwise相当于default。

使用where标签，则会自动纠错：比如在choose选择下无otherwise的情况，传入空参将报错。而使用where标签则会在此情况下将where省略。

### 添加

添加功能的标签为insert。如：

```html
<insert id="add">
    insert into tb_brand (brand_name,company_name,ordered,description,status)
    value (#{brandName},#{companyName},#{prderded},#{description},#{status});
</insert>
```

尽管调用了SQL标签，而且在终端显示成功添加了数据，但是在数据库中并没有数据的更新，原因在于：没有开启自动提交，代表事物的提交默认为手动，所以事物会回滚，即并没有添加数据。解决办法为：

- 设置自动提交事务：在获取Mapper接口的代理对象时，对SqlSession对象的getMapper方法传入参数true，即设置autocommit参数为true，以此开启自动提交

- 在执行完SQL语句的方法后添加提交事务的代码：即对SqlSession对象使用提交方法，如`sqlSession.commit();`

#### 返回添加数据的主键

对于插入到数据库的数据，有时需要立即获取它的主键（ID），此时需要给SQL标签添加以下内容，即可给返回的对象中添加刚插入数据的主键，随后调用对象的方法获取主键即可（一般为ID）。

```html
<insert useGeneratedKeys="true" keyProperty="id">
```

### 修改

#### 修改全部字段

HTML标签为`update`，如：

```html
<update id="update">
	update tb_user
    set
    	brand_name = #{brandName},
    	company_name = #{companyName},
    	ordered = #{ordered},
    	description = #{description},
    	status = #{status}
    where id = #{id};
</update>
```

使用该标签的方法之后，会有一个返回值：代表受影响的行数。

#### 动态修改

 解决方法：使用`set`标签替代set，内部需要修改的数据装入`if`标签即可。如：

```html
<update id="update">
	update tb_user
    <set>
    	<if test="brandName != null and brandName != ''">
        	brand_name = #{brandName},
        </if>
        <if test="company_name != null and company_name != ''">
        	company_name = #{companyName},
        </if>
        <if test="ordered != null">
        	ordered = #{ordered},
        </if>
        <if test="description != null and description != ''">
        	description = #{description},
        </if>
        <if test="status != null">
        	status = #{status}
        </if>
    </set>	
    where id = #{id};
</update>                        
```

### 删除

#### 单独删除

使用`delete`标签，如：

```html
<delete id="deleteById">
	delect from tb_brand where id=#{id};
</delete>
```

#### 批量删除

 对于批量删除，其需要同时删除多个ID，但因为占位符的个数所以很难统一，故使用XML标签中的遍历方法，如下：

```html
<delete id="deleteById">
	delect from tb_brand
    where id in
    <foreach collection="array" item="id" separator="," open="(" close=")">
    	#{id}
    </foreach>
</delete>
```

其中：`collection`标签代表了接收的数据的类型，在传递数组时，mybatis会将数组参数封装为一个Map集合，所以可默认写`array`。`item`标签代表传入数组的各个元素的名称，也就是将要填入下方标签的占位符。`separator`代表分隔符，`open`和`close`标签代表`foreach`标签产生的数据用什么包裹，因原始语句为：

```html
delect from tb_brand where id=(id,id,id);
```

所以分隔符使用逗号，起始符和结尾符使用括号即可。

注：可以使用@Param注解改变map集合的默认key的名称，这样就可以给`collection`标签填写数组的名称。

### 参数传递

传递多个参数的时候，Mybatis会将其封装为Map集合，默认名称为`arg`和param，arg从0开始，param从1开始。所以使用`@Param`注解的作用是替代Map集合中默认的arg键名

传递单个参数的时候：

- 传递POJO类型、Map集合、其他类型时可直接使用。
- 传递Collection、List、Array时会将其封装为Map集合。

### 注解开发

一些简单的功能可以使用注解开发来替代，如下：

```java
@Select("select * from tb_user where id= #{id}")
public User selectById(int id);
```

##  HTTP

### 请求

#### 数据格式

1. 请求行：请求数据的第一行。其中GET表示请求方式，/表示请求资源路径，HTTP/1.1表示协议版本。
2. 请求头：第二行开始，格式为key:value形式。
3. 请求体：POST请求的最后一部分，存放请求参数

例如：

GET请求的请求数据：

![image-20240101210531259](./assets/image-20240101210531259.png)

POST请求的请求数据：

![image-20240101211125661](./assets/image-20240101211125661.png)

#### 常见的HTTP请求头

- HOST：表示请求的主机名。
- User-Agent：浏览器版本。
- Accept：表示浏览器能接受的资源类型，如text/\*，image/\*，或者\*/\*表示所有。
- Accept-Language：表示浏览器偏好的语言，服务器可以据此返回不同语言的网页。
- Accept-Encoding：表示浏览器可以支持的压缩类型，例如gzip，deflate等。

#### GET和POST请求区别

1. GET请求的请求参数在请求行中，没有请求体；POST请求的请求参数在请求体中。
2. GET请求的请求参数有大小限制，POST没有。

### 响应

#### 数据格式

1. 响应行：响应数据的第一行。其中HTTP/1.1表示协议版本，200表示响应状态码，OK表示状态码描述。
2. 响应头：第二行开始，格式为key:value形式。
3. 响应体：最后一部分，存放响应数据。

例如：

![image-20240101211536040](./assets/image-20240101211536040.png)

#### 常见的HTTP响应头

- Content-Type：表示该响应内容的类型，例如text/html，image/jpeg。
- Content-Length：表示该响应内容的长度（字节数）；
- Content-Encoding：表示该响应压缩算法，例如gzip；
- Cache-Control：只是客户端应如何缓存，例如max-age=300表示可以最多缓存300秒
