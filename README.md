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

