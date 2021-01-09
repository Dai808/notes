#### 1、SpringBoot

@AutoConfigurationPackage注解

自动配置包，把主程序所在的包及子包扫描到。

所以springboot的启动文件要和cntroller放在同一个包内。



#### 2、查看springcould和springboot详细版本

https://start.spring.io/actuator/info



#### 3、在项目中配置热部署

##### （1）在子工程中添加配置

```xml
<!--热部署-->
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-devtools</artifactId>
     <scope>runtime</scope>
     <optional>true</optional>
</dependency>
```

##### （2）在父类聚合总工程中添加build

```xml
<build>
  <plugins>
     <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
           <fork>true</fork>
           <addResources>true</addResources>
        </configuration>
     </plugin>
  </plugins>
</build>
```

##### （3）在设置中开启选项

##### （4）选中父项目按下快捷键ctrl+shift+alt+/后点击registry(未生效尝试重启idea)

#### 4、关于使用的注解

查询使用:   @GetMapping("")

删除使用：@DeleteMapping("")

修改使用：@PutMapping("")

添加使用：@PostMapping("")，post主要传递的是实体参数。

#### 5、RestTemplate的使用

首先向spring容器中注入一个RestTemplate对象。在业务中直接使用容器中的该对象。

使用方法如下：

