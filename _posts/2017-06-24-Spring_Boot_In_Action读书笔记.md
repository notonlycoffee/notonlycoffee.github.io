---
layout: post
category: Web
tag: Web_Tag
header-img: "img/home-bg.jpg"
---

# spring boot in action 读书笔记

关于这本书,我在阅读之前已经看过了spring boot的相关视频教程,所以对于书中的一些东西有些许的熟悉,以此为基础,看了这本书的大概内容.
其中没有看的是跟java无关的内容,比如第5章:"Groovy与Spring Boot CLI"和第六章:"在Spring Boot中使用Grails";

了解比较少的是关于Spring提供的集成测试模块;剩下的内容都看了一个遍,总体感觉Spring Boot的功能很强大,但是没有提供多少的新鲜的功能给我们.它只是将我们的开发模式上升到更加方便的一个层次,比如说给我们添加了**起步依赖**, 让我们不用理会其他第三方开发包之间的不兼容性问题;或者是给我们添加了**自动配置**功能,让我们在创建一个基本的项目应用的时候,不需要配置那么多的东西,因为Spring Boot已经使用了自己默认的配置方式;在这里,我来总结一下Spring Boot里面的一些东西:

Spring Boot in action里面主要讲解了四大块内容,其中包括:

1. 起步依赖
2. 自动配置
3. Spring Boot CLI
4. Actuator

## 起步依赖

**起步依赖** 的意思就是, 我们平常在创建一个普通的项目时候, 常常会引入第三方的开发包, 比如jdbc, mysql, activemq等等之类的jar包或者maven依赖, 在这里可能会因为我们引入的版本号问题出现依赖之间的冲突. 而且引入这些第三方开发包其实真的很麻烦, 也很费事情, 所以Spring Boot 想好要帮你做这个事情, 让你在开发一个项目的时候, 可以不用太担心第三方开发包的冲突问题和引入不完善等问题,这个就是我们说的起步依赖;
	起步依赖, 在pom.xml里面比较常见的引入方式是:`<artifactId>spring-boot-starter-*</artifactId>`, 这里的`*`, 一般表示我们要用到的第三方依赖, 可以是`aop`, 则起步依赖就是`<artifactId>spring-boot-starter-aop</artifactId>`

一般,使用起步依赖之后,起步依赖中会包含一系列的开发包,比如如果你使用`<artifactId>spring-boot-starter-web</artifactId>`这个起步依赖,则会包括我们使用的`logback`依赖,但是假如说,我们不需要使用起步依赖中的几个不必要的依赖,或者是我们需要使用其他的相似的依赖来代替起步依赖里面的功能,我们可以使用maven中的语法, 这里展示的是, 我们`spring-boot-starter-web`中使用了`logback`依赖, 但是我们不打算用logback, 打算直接使用`log4j2`, 则可以这样引入依赖:

	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
		<exclusions>
			<exclusion>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-starter-logging</artifactId>
			</exclusion>
		</exclusions>
	</dependency>

与此同时, 你可以在pom.xml里面看到我们所有的起步依赖都是没有明确声明版本号的, 这些都是因为我们使用了Spring Boot父亲pom.xml里面的默认版本号, Spring Boot会帮我们搞定这件事情




## 自动配置

**自动配置** 的意思就是, 比如我们在没有使用Spring Boot创建项目之前, 我们需要为一个项目, 比如web项目配置很多东西, 比如我们使用Spring, 需要引入Spring的配置文件, 在`web.xml`里面配置`spring`容器; 这是一个方面. 同样, 比如我们引入了`aop`的事务, 我们需要在`service`层里面配置需要的事务配置, 书写切点表达式等等. 这些配置都是很繁杂的东西. 然而在我们使用了`Spring Boot`之后, 这些配置都会默认给你配置好, 开启一个`Spring Boot`项目, 直接就可以跑起来, 使用内置的`web`容器, 比如`tomcat`, 或者`jetty`等等. 这样是不是很方便呢?

但是, `Spring Boot`在给你做完了一系列的自动配置之后, 并没有限制你自己的天马行空的配置和修改, `Spring Boot`还允许你使用java配置的形式, 覆盖这些默认的配置, 你可以自己写一个`java`的配置类, 加上`@Configuration`注解, 继承某些类, 完成对于相关配置的覆盖; 但是这样的配置有一些是完全的覆盖, 比如`Spring Boot`给我们配置了数据源的内容, 如果我们要改写这些配置, 我们就必须自己使用一个`java`配置来覆盖默认的配置, 可能我们只是要修改一个微不足道的配置, 但是却覆盖了那个庞大的配置内容, 这样是很不理智的, 所以`Spring Boot`给我们提供了将近**300多项** 配置的微调, 比如如果我们需要修改这个启动项目的端口, 我们可以使用`server.port=8899`, 自己定义一个启动的端口; 然而这些配置是配置在`Spring Boot`默认的配置文件中的, 配置文件的名字默认为:`application.properties`或者`application.yml`, 这两种配置文件都有各自的使用优势;


说到配置文件, 这里必须谈到的还有`profile`功能, `profile`功能提供给我们多重配置文件的选择形式, 比如我们开发环境, 测试环境, 生产环境中的配置内容肯定会有所不同. 举个例子, 在开发环境连接的数据库是开发环境的, 是自己本地的. 生产环境使用的数据库连接是正式的, 是真实的用户数据. 在这里, 我们可以把这些不同环境的配置都写入到我们的项目中的不同配置文件中, 通过`profile`功能, 可以在不同的运行环境中, 选择我们需要的配置文件, 运行整个项目;`application.properties` 和 `application.yml`对于`profile`功能的支持有些许不一样, 这里不详细说明;


## Spring Boot CLI

**Spring Boot CLI** 是`Spring Boot`提供的运行项目的命令行工具, 我们可以使用这个命令行工具,直接构建我们的项目, 可以使用`spring init`, 初始化一个项目, 也可以加入一些其他的命令, 对初始化进行定制, 比如命令:　`spring init -dweb,aop,security -p jar --build maven myapp`, 这个命令是说, 使用maven来构建这个项目, 加入起步依赖为`web`, `aop`, `security`, 打包方式为`jar`, 最后生成的项目解压到myapp文件夹里面去;


## Actuator

**Actuator** 是`Spring Boot`给我们生产环境监控提供的一个额外的功能, 让你可以在生产环境中, 查看项目运行的一系列参数. 根据你查看到的参数, 可以进行系统的调优, 内存机子的分配等等, 都可以根据`Actuator`的结果进行调整;

`Actuator`给我们定义了一系列的访问**端点**, 我们使用这些端点,就可以查看整个应用中的一些信息, 比如:

1. `/mappings` 端点, 可以查看可以访问的`controller`, 以及这个`controller`跟哪个`URL`映射到一起

2. `/evn` 端点, 可以用来查看系统配置的所有环境属性, 比如环境变量中的属性, `application.properties`里面的属性, `jvm`里面的属性, 都可以在这里查看的到

3. `/trace`端点, 提供`http`请求的基本跟踪信息

还有其他的端点信息,这里也不详细说明; 总的来说, `Actuator`就是在我们生产环境中, 用来查看系统对于资源的使用情况, 以及系统的各项配置查看的工具, 另外, 值得注意的是, 我们不单单可以查看`Spring Boot`推送给我们的内容, 我们还可以根据`Actuator`自己定义我们需要查看的运行参数, 比如我们需要查看每天系统的访问量, 我们可以自己定义一个系统访问量的变量, 然后记载系统访问的次数, 递增显示出来;

对于自定义`Actuator`, 这里会用到几个类, 比如如果你需要定义一个自己的递增的变量,  用来记载系统的访问量, 你可以使用`CounterService`的类, 将它注入到我们的逻辑中, 然后显示地调用, 比如:`counterService.increment("system.count");`, 这样就可以通过变量`system.count`查看系统的访问次数了;

自定义`Actuator`, 能用到的除了`CounterService`之外, 还有`GaugeService`, 或者将类实现接口`PublicMetrics`, 详细的可以查看`Spring Boot In Action`这本书;

说到端点, 里面还包括一个`/shutdown`端点, 这个端点比较特别,调用的时候必须以`post`请求进行调用, 而且调用这个端点的意思是直接停止我们的项目运行, 所以这个端点一般是没有启动的, 需要在`application.properties`里面配置之后才可以访问这个端点; 总的来说, 上面所有的端点中, 包括的有查看配置的端点, 用来度量系统资源的端点, 以及其他的端点, 比如关闭应用的端点, 这些端点都是开发人员用来查看系统参数的, 不需要对外开放, 而且开放之后可能也会导致一些问题, 比如被别人调用了`/shutdown`端点, 整个应用就直接结束了, 这是很致命的问题, 所以对于端点, 我们有一个保护的机制; 我们可以禁用某些端点, 或者我们给端点重新分配新的访问路径, 但最终解决这个端点不安全的问题, 我们采取的方法是使用`Spring Security`进行验证, 让有访问权限的人, 通过输入用户名密码登录,进行相关的操作, 没有访问权限的人直接拒绝访问;

对于端点, 我们还可以使用`shell`命令行进行调用访问和查看, 也可以使用`jdk`自带的`jconsole`,连接到我们的应用中, 实现`jmx`连接查看端点内容;

以上是我对`Spring Boot In Action`这本书的一点点总结, 应该会有所瑕疵, 但后续会不定期更新, 感谢自己;