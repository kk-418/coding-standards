# Maven构建规范

> **作者**: kk
> **创建日期**: 2025-10-01
> **适用范围**: Maven 3.9+ 项目

---

## 目录
1. [Maven版本要求](#maven版本要求)
2. [项目结构规范](#项目结构规范)
3. [POM文件规范](#pom文件规范)
4. [依赖管理](#依赖管理)
5. [插件配置](#插件配置)
6. [多模块项目](#多模块项目)
7. [Profile配置](#profile配置)
8. [发布配置](#发布配置)
9. [常用命令](#常用命令)
10. [最佳实践](#最佳实践)

---

## 1. Maven版本要求

### 1.1 推荐版本
- **Maven版本**: 3.9+
- **Java版本**: 21

### 1.2 Maven安装配置

**settings.xml配置镜像**:
```xml
<settings>
    <mirrors>
        <!-- 阿里云Maven镜像 -->
        <mirror>
            <id>aliyun</id>
            <mirrorOf>central</mirrorOf>
            <name>Aliyun Maven</name>
            <url>https://maven.aliyun.com/repository/public</url>
        </mirror>
    </mirrors>

    <profiles>
        <profile>
            <id>jdk-21</id>
            <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>21</jdk>
            </activation>
            <properties>
                <maven.compiler.source>21</maven.compiler.source>
                <maven.compiler.target>21</maven.compiler.target>
                <maven.compiler.release>21</maven.compiler.release>
            </properties>
        </profile>
    </profiles>
</settings>
```

---

## 2. 项目结构规范

### 2.1 标准Maven目录结构

```
my-project/
├── pom.xml
├── src/
│   ├── main/
│   │   ├── java/          # Java源代码
│   │   ├── resources/     # 资源文件
│   │   └── webapp/        # Web资源(如果是Web项目)
│   └── test/
│       ├── java/          # 测试代码
│       └── resources/     # 测试资源
└── target/                # 构建输出目录
```

---

## 3. POM文件规范

### 3.1 基本POM结构

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 基本信息 -->
    <groupId>plus.sbt</groupId>
    <artifactId>my-project</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>My Project</name>
    <description>项目描述</description>
    <url>https://github.com/username/my-project</url>

    <!-- 属性定义 -->
    <properties>
        <java.version>21</java.version>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <maven.compiler.release>21</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>

        <!-- 依赖版本 -->
        <spring-boot.version>3.4.7</spring-boot.version>
        <mybatis-flex.version>1.11.1</mybatis-flex.version>
        <lombok.version>1.18.34</lombok.version>
    </properties>

    <!-- 父POM(可选) -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.4.7</version>
        <relativePath/>
    </parent>

    <!-- 依赖管理 -->
    <dependencyManagement>
        <!-- BOM依赖 -->
    </dependencyManagement>

    <!-- 依赖 -->
    <dependencies>
        <!-- 具体依赖 -->
    </dependencies>

    <!-- 构建配置 -->
    <build>
        <plugins>
            <!-- 插件配置 -->
        </plugins>
    </build>
</project>
```

### 3.2 版本号规范

**版本号格式**: `主版本.次版本.修订版本[-qualifier]`

- **SNAPSHOT**: 开发版本(如 `1.0.0-SNAPSHOT`)
- **RELEASE**: 正式发布版本(如 `1.0.0`)
- **RC**: 候选发布版本(如 `1.0.0-RC1`)
- **BETA**: 测试版本(如 `1.0.0-BETA`)

---

## 4. 依赖管理

### 4.1 依赖范围

| scope | 说明 | 使用场景 |
|-------|------|----------|
| `compile` | 编译和运行时依赖(默认) | 大部分依赖 |
| `provided` | 编译时依赖,运行时由容器提供 | Servlet API, Lombok |
| `runtime` | 运行时依赖 | JDBC驱动 |
| `test` | 测试依赖 | JUnit, Mockito |
| `system` | 系统依赖(不推荐) | 本地jar包 |
| `import` | 导入BOM(仅在dependencyManagement中) | Spring Boot BOM |

### 4.2 依赖声明

```xml
<dependencies>
    <!-- Spring Boot Starter -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
        <scope>provided</scope>
    </dependency>

    <!-- 测试依赖 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 4.3 排除传递依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <!-- 排除默认的tomcat -->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!-- 使用jetty替代 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

### 4.4 使用BOM统一版本管理

```xml
<dependencyManagement>
    <dependencies>
        <!-- Spring Boot BOM -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${spring-boot.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <!-- 无需指定版本,由BOM管理 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

---

## 5. 插件配置

### 5.1 Maven Compiler Plugin

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.13.0</version>
            <configuration>
                <source>21</source>
                <target>21</target>
                <release>21</release>
                <encoding>UTF-8</encoding>
                <!-- 保留方法参数名 -->
                <parameters>true</parameters>
                <!-- 编译参数 -->
                <compilerArgs>
                    <arg>-Xlint:deprecation</arg>
                    <arg>-Xlint:unchecked</arg>
                </compilerArgs>
                <!-- 注解处理器路径 -->
                <annotationProcessorPaths>
                    <path>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                        <version>${lombok.version}</version>
                    </path>
                    <path>
                        <groupId>io.github.linpeilie</groupId>
                        <artifactId>mapstruct-plus-processor</artifactId>
                        <version>${mapstruct-plus.version}</version>
                    </path>
                </annotationProcessorPaths>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### 5.2 Maven Surefire Plugin (测试)

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.2.5</version>
    <configuration>
        <!-- 跳过测试 -->
        <skipTests>false</skipTests>
        <!-- 测试失败是否中断构建 -->
        <testFailureIgnore>false</testFailureIgnore>
        <!-- 并行测试 -->
        <parallel>classes</parallel>
        <threadCount>4</threadCount>
    </configuration>
</plugin>
```

### 5.3 Spring Boot Maven Plugin

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>${spring-boot.version}</version>
    <executions>
        <execution>
            <goals>
                <goal>repackage</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <!-- 主类 -->
        <mainClass>plus.sbt.Application</mainClass>
        <!-- 排除Lombok -->
        <excludes>
            <exclude>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
            </exclude>
        </excludes>
    </configuration>
</plugin>
```

### 5.4 Maven Source Plugin (生成源码包)

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-source-plugin</artifactId>
    <version>3.3.1</version>
    <executions>
        <execution>
            <id>attach-sources</id>
            <goals>
                <goal>jar</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### 5.5 Maven Javadoc Plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-javadoc-plugin</artifactId>
    <version>3.6.3</version>
    <configuration>
        <encoding>UTF-8</encoding>
        <charset>UTF-8</charset>
        <docencoding>UTF-8</docencoding>
    </configuration>
    <executions>
        <execution>
            <id>attach-javadocs</id>
            <goals>
                <goal>jar</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

---

## 6. 多模块项目

### 6.1 父POM配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>plus.sbt</groupId>
    <artifactId>my-parent</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <!-- 子模块 -->
    <modules>
        <module>module-a</module>
        <module>module-b</module>
    </modules>

    <!-- 统一版本管理 -->
    <properties>
        <java.version>21</java.version>
        <spring-boot.version>3.4.7</spring-boot.version>
    </properties>

    <!-- 依赖管理 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <!-- 构建配置 -->
    <build>
        <pluginManagement>
            <plugins>
                <!-- 插件版本管理 -->
            </plugins>
        </pluginManagement>
    </build>
</project>
```

### 6.2 子模块POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 引用父POM -->
    <parent>
        <groupId>plus.sbt</groupId>
        <artifactId>my-parent</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../pom.xml</relativePath>
    </parent>

    <artifactId>module-a</artifactId>

    <dependencies>
        <!-- 依赖声明,版本由父POM管理 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
</project>
```

---

## 7. Profile配置

### 7.1 环境配置Profile

```xml
<profiles>
    <!-- 开发环境 -->
    <profile>
        <id>dev</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <env>dev</env>
        </properties>
    </profile>

    <!-- 测试环境 -->
    <profile>
        <id>test</id>
        <properties>
            <env>test</env>
        </properties>
    </profile>

    <!-- 生产环境 -->
    <profile>
        <id>prod</id>
        <properties>
            <env>prod</env>
        </properties>
    </profile>
</profiles>

<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
            <includes>
                <include>application.yml</include>
                <include>application-${env}.yml</include>
            </includes>
        </resource>
    </resources>
</build>
```

**使用Profile**:
```bash
# 激活test profile
mvn clean package -Ptest

# 激活prod profile
mvn clean package -Pprod
```

---

## 8. 发布配置

### 8.1 Maven Deploy Plugin

```xml
<distributionManagement>
    <!-- 快照版本仓库 -->
    <snapshotRepository>
        <id>nexus-snapshots</id>
        <url>https://nexus.example.com/repository/maven-snapshots/</url>
    </snapshotRepository>

    <!-- 正式版本仓库 -->
    <repository>
        <id>nexus-releases</id>
        <url>https://nexus.example.com/repository/maven-releases/</url>
    </repository>
</distributionManagement>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-deploy-plugin</artifactId>
            <version>3.1.1</version>
        </plugin>
    </plugins>
</build>
```

**settings.xml配置凭证**:
```xml
<servers>
    <server>
        <id>nexus-snapshots</id>
        <username>admin</username>
        <password>admin123</password>
    </server>
    <server>
        <id>nexus-releases</id>
        <username>admin</username>
        <password>admin123</password>
    </server>
</servers>
```

---

## 9. 常用命令

### 9.1 生命周期命令

```bash
# 清理
mvn clean

# 编译
mvn compile

# 编译测试代码
mvn test-compile

# 运行测试
mvn test

# 打包
mvn package

# 安装到本地仓库
mvn install

# 发布到远程仓库
mvn deploy
```

### 9.2 组合命令

```bash
# 清理并打包
mvn clean package

# 清理并安装到本地仓库
mvn clean install

# 跳过测试打包
mvn clean package -DskipTests

# 跳过测试并安装
mvn clean install -DskipTests

# 指定Profile打包
mvn clean package -Pprod
```

### 9.3 依赖相关

```bash
# 查看依赖树
mvn dependency:tree

# 分析依赖
mvn dependency:analyze

# 解决依赖冲突
mvn dependency:resolve

# 下载源码
mvn dependency:sources

# 下载JavaDoc
mvn dependency:resolve -Dclassifier=javadoc
```

### 9.4 其他常用命令

```bash
# 查看有效POM
mvn help:effective-pom

# 查看有效settings
mvn help:effective-settings

# 查看所有Profile
mvn help:all-profiles

# 查看活跃Profile
mvn help:active-profiles
```

---

## 10. 最佳实践

### 10.1 版本号管理

```xml
<!-- ✅ 推荐: 使用properties统一管理 -->
<properties>
    <spring-boot.version>3.4.7</spring-boot.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>${spring-boot.version}</version>
    </dependency>
</dependencies>

<!-- ❌ 不推荐: 硬编码版本号 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>3.4.7</version>
</dependency>
```

### 10.2 使用BOM管理依赖

```xml
<!-- ✅ 推荐: 使用BOM -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${spring-boot.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 10.3 多模块项目结构

```
my-project/
├── pom.xml                    # 父POM
├── my-project-common/         # 公共模块
│   └── pom.xml
├── my-project-api/            # API模块
│   └── pom.xml
└── my-project-service/        # 服务模块
    └── pom.xml
```

### 10.4 插件版本锁定

```xml
<build>
    <pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.13.0</version>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.2.5</version>
            </plugin>
        </plugins>
    </pluginManagement>
</build>
```

---

## 参考资料

- [Maven官方文档](https://maven.apache.org/)
- [Maven中央仓库](https://central.sonatype.com/)
- [Maven插件列表](https://maven.apache.org/plugins/)

---

**最后更新**: 2025-10-01
**维护者**: kk
