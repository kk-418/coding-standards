# Gradle构建规范

> **作者**: kk
> **创建日期**: 2025-10-01
> **适用范围**: Gradle 9.0+ 项目

---

## 目录
1. [Gradle版本要求](#gradle版本要求)
2. [项目结构规范](#项目结构规范)
3. [编译器配置](#编译器配置)
4. [依赖管理](#依赖管理)
5. [注解处理器配置](#注解处理器配置)
6. [自定义任务](#自定义任务)
7. [构建优化](#构建优化)
8. [发布配置](#发布配置)
9. [常用命令](#常用命令)
10. [最佳实践](#最佳实践)

---

## 1. Gradle版本要求

### 1.1 推荐版本
- **Gradle版本**: 9.0+
- **Gradle Wrapper**: 使用wrapper确保团队环境一致

### 1.2 Gradle Wrapper配置

```bash
# 升级Gradle Wrapper到9.0
gradle wrapper --gradle-version 9.0 --distribution-type bin
```

**gradle/wrapper/gradle-wrapper.properties**:
```properties
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-9.0-bin.zip
networkTimeout=10000
validateDistributionUrl=true
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```

---

## 2. 项目结构规范

### 2.1 单模块项目

```
my-project/
├── build.gradle
├── settings.gradle
├── gradle.properties
├── gradlew
├── gradlew.bat
├── gradle/
│   └── wrapper/
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
└── src/
    ├── main/
    │   ├── java/
    │   └── resources/
    └── test/
        ├── java/
        └── resources/
```

### 2.2 多模块项目

```
my-project/
├── build.gradle           # 根项目配置
├── settings.gradle        # 模块声明
├── gradle.properties      # 全局属性
├── gradlew
├── module-a/
│   ├── build.gradle
│   └── src/
└── module-b/
    ├── build.gradle
    └── src/
```

**settings.gradle**:
```gradle
rootProject.name = 'my-project'

include 'module-a'
include 'module-b'
```

---

## 3. 编译器配置

### 3.1 Java编译配置

```gradle
java {
    sourceCompatibility = JavaVersion.VERSION_21
    targetCompatibility = JavaVersion.VERSION_21

    // 生成源码jar
    withSourcesJar()

    // 生成JavaDoc jar(可选)
    // withJavadocJar()
}

tasks.withType(JavaCompile).configureEach {
    // 字符编码
    options.encoding = 'UTF-8'

    // 保留方法参数名(用于Spring等框架)
    options.compilerArgs += ['-parameters']

    // 显示废弃API警告
    options.compilerArgs += ['-Xlint:deprecation']

    // 显示未检查警告
    options.compilerArgs += ['-Xlint:unchecked']
}

compileJava {
    // 使用release标志替代source/target
    options.release.set(21)
}
```

### 3.2 Kotlin编译配置(如果使用Kotlin)

```gradle
tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach {
    kotlinOptions {
        jvmTarget = '21'
        freeCompilerArgs += ['-Xjsr305=strict']
    }
}
```

---

## 4. 依赖管理

### 4.1 版本管理(ext块)

```gradle
ext {
    // Spring Boot
    springBootVersion = '3.4.7'

    // 数据库
    mybatisFlexVersion = '1.11.1'

    // 工具库
    hutoolVersion = '5.8.38'
    lombokVersion = '1.18.34'
    mapstructPlusVersion = '1.4.8'

    // 测试
    junitVersion = '5.11.0'
}
```

### 4.2 依赖声明

```gradle
dependencies {
    // Spring Boot
    implementation "org.springframework.boot:spring-boot-starter-web:${springBootVersion}"

    // 编译时依赖
    compileOnly "org.projectlombok:lombok:${lombokVersion}"

    // 注解处理器
    annotationProcessor "org.projectlombok:lombok:${lombokVersion}"
    annotationProcessor "io.github.linpeilie:mapstruct-plus-processor:${mapstructPlusVersion}"

    // 测试依赖
    testImplementation "org.springframework.boot:spring-boot-starter-test:${springBootVersion}"
    testImplementation "org.junit.jupiter:junit-jupiter:${junitVersion}"
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}
```

### 4.3 依赖配置说明

| 配置 | 说明 | 使用场景 |
|------|------|----------|
| `implementation` | 编译和运行时依赖 | 大部分依赖使用 |
| `api` | 编译和运行时依赖(传递给依赖方) | 库项目对外暴露的API |
| `compileOnly` | 仅编译时依赖 | Lombok、注解等 |
| `runtimeOnly` | 仅运行时依赖 | JDBC驱动等 |
| `testImplementation` | 测试编译和运行时依赖 | 测试框架 |
| `testCompileOnly` | 测试仅编译时依赖 | 测试注解 |
| `testRuntimeOnly` | 测试仅运行时依赖 | 测试运行环境 |
| `annotationProcessor` | 注解处理器 | Lombok、MapStruct等 |

### 4.4 排除传递依赖

```gradle
dependencies {
    implementation('org.springframework.boot:spring-boot-starter-web') {
        // 排除默认的tomcat,使用jetty
        exclude group: 'org.springframework.boot', module: 'spring-boot-starter-tomcat'
    }

    implementation 'org.springframework.boot:spring-boot-starter-jetty'
}
```

### 4.5 统一依赖版本管理(BOM)

```gradle
dependencies {
    // Spring Boot BOM
    implementation platform("org.springframework.boot:spring-boot-dependencies:${springBootVersion}")

    // 无需指定版本,由BOM管理
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
}
```

---

## 5. 注解处理器配置

### 5.1 配置注解处理器路径

```gradle
dependencies {
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    annotationProcessor "io.github.linpeilie:mapstruct-plus-processor:${mapstructPlusVersion}"
}

compileJava {
    options.annotationProcessorPath = configurations.annotationProcessor
}
```

### 5.2 Lombok + MapStruct配置顺序

Lombok必须在MapStruct之前执行:

```gradle
dependencies {
    compileOnly 'org.projectlombok:lombok'

    // Lombok注解处理器必须在前
    annotationProcessor 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok-mapstruct-binding:0.2.0'

    // MapStruct注解处理器在后
    annotationProcessor "io.github.linpeilie:mapstruct-plus-processor:${mapstructPlusVersion}"
}
```

---

## 6. 自定义任务

### 6.1 VO类型校验任务

```gradle
tasks.register('validateVoTypes') {
    group = 'verification'
    description = '校验VO类中是否存在禁止使用的类型'

    doLast {
        // 扫描所有VO类
        def voFiles = fileTree(dir: 'src/main/java', include: '**/*VO.java')
        def violations = []

        voFiles.each { file ->
            def content = file.text
            def lineNumber = 0

            content.eachLine { line ->
                lineNumber++

                // 检查禁止的类型
                if (line =~ /\s+(BigDecimal|Long|LocalDateTime|LocalDate)\s+/) {
                    violations << "${file.path}:${lineNumber} - 发现禁止类型"
                }
            }
        }

        if (!violations.isEmpty()) {
            throw new GradleException("VO类型校验失败!\n" + violations.join('\n'))
        }
    }
}

// 编译前自动执行校验
compileJava.dependsOn validateVoTypes
```

### 6.2 代码格式化任务

```gradle
tasks.register('formatCode') {
    group = 'formatting'
    description = '格式化Java代码'

    doLast {
        exec {
            commandLine 'google-java-format', '--replace', '--aosp', 'src/main/java/**/*.java'
        }
    }
}
```

---

## 7. 构建优化

### 7.1 gradle.properties配置

```properties
# 编码
org.gradle.jvmargs=-Dfile.encoding=UTF-8

# 并行构建
org.gradle.parallel=true

# 按需配置
org.gradle.configureondemand=true

# 构建缓存
org.gradle.caching=true

# 配置缓存(Gradle 8.0+)
org.gradle.configuration-cache=true

# JVM内存设置
org.gradle.jvmargs=-Xmx2048m -XX:MaxMetaspaceSize=512m

# Gradle守护进程
org.gradle.daemon=true
```

### 7.2 启用完整堆栈跟踪

```gradle
// build.gradle
gradle.startParameter.showStacktrace = ShowStacktrace.ALWAYS_FULL
```

### 7.3 依赖缓存配置

```gradle
configurations.all {
    resolutionStrategy {
        // 缓存动态版本1小时
        cacheDynamicVersionsFor 1, 'hours'

        // 缓存快照版本10分钟
        cacheChangingModulesFor 10, 'minutes'
    }
}
```

---

## 8. 发布配置

### 8.1 Maven发布配置

```gradle
plugins {
    id 'maven-publish'
}

publishing {
    publications {
        maven(MavenPublication) {
            from components.java

            groupId = 'plus.sbt'
            artifactId = project.name
            version = project.version

            pom {
                name = project.name
                description = 'SBT Plus Framework - ' + project.name
                url = 'https://github.com/sbt-plus/sbt-plus'

                licenses {
                    license {
                        name = 'The Apache License, Version 2.0'
                        url = 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }

                developers {
                    developer {
                        id = 'kk'
                        name = 'kk'
                        email = 'kk@example.com'
                    }
                }

                scm {
                    connection = 'scm:git:git://github.com/sbt-plus/sbt-plus.git'
                    developerConnection = 'scm:git:ssh://github.com/sbt-plus/sbt-plus.git'
                    url = 'https://github.com/sbt-plus/sbt-plus'
                }
            }
        }
    }

    repositories {
        mavenLocal()

        // 可选: 发布到私有仓库
        // maven {
        //     url = 'https://nexus.example.com/repository/maven-releases/'
        //     credentials {
        //         username = project.findProperty('nexusUsername') ?: ''
        //         password = project.findProperty('nexusPassword') ?: ''
        //     }
        // }
    }
}

java {
    withSourcesJar()  // 生成源码包
}
```

---

## 9. 常用命令

### 9.1 编译相关

```bash
# 编译Java代码
gradle compileJava

# 编译测试代码
gradle compileTestJava

# 清理构建
gradle clean

# 完整构建
gradle build

# 跳过测试构建
gradle build -x test
```

### 9.2 依赖相关

```bash
# 查看依赖树
gradle dependencies

# 查看特定配置的依赖
gradle dependencies --configuration implementation

# 分析依赖冲突
gradle dependencyInsight --dependency spring-boot-starter-web
```

### 9.3 任务相关

```bash
# 列出所有任务
gradle tasks

# 列出所有任务(包括隐藏)
gradle tasks --all

# 执行自定义任务
gradle validateVoTypes
```

### 9.4 发布相关

```bash
# 发布到本地Maven仓库
gradle publishToMavenLocal

# 发布到远程仓库
gradle publish
```

### 9.5 调试相关

```bash
# 调试模式
gradle build --debug

# 显示详细信息
gradle build --info

# 显示警告信息
gradle build --warn

# 显示完整堆栈
gradle build --stacktrace
```

---

## 10. 最佳实践

### 10.1 版本管理

```gradle
// ✅ 推荐: 统一在ext块管理版本
ext {
    springBootVersion = '3.4.7'
}

dependencies {
    implementation "org.springframework.boot:spring-boot-starter-web:${springBootVersion}"
}

// ❌ 不推荐: 硬编码版本号
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web:3.4.7'
}
```

### 10.2 仓库配置顺序

```gradle
repositories {
    mavenLocal()      // 本地仓库优先
    mavenCentral()    // Maven中央仓库
    maven {           // 阿里云镜像加速
        url = 'https://maven.aliyun.com/repository/public/'
        name = 'aliyun'
    }
}
```

### 10.3 多模块项目配置

**根build.gradle**:
```gradle
subprojects {
    apply plugin: 'java'

    java {
        sourceCompatibility = JavaVersion.VERSION_21
        targetCompatibility = JavaVersion.VERSION_21
    }

    repositories {
        mavenLocal()
        mavenCentral()
    }

    dependencies {
        testImplementation 'org.junit.jupiter:junit-jupiter'
        testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    }

    tasks.withType(Test).configureEach {
        useJUnitPlatform()
    }
}
```

### 10.4 依赖冲突处理

```gradle
configurations.all {
    resolutionStrategy {
        // 强制使用特定版本
        force 'com.google.guava:guava:32.1.3-jre'

        // 失败时快速停止
        failOnVersionConflict()
    }
}
```

### 10.5 测试配置

```gradle
tasks.withType(Test).configureEach {
    useJUnitPlatform()

    testLogging {
        events "passed", "skipped", "failed"
        exceptionFormat "full"
        showStandardStreams = false
    }

    // 并行测试
    maxParallelForks = Runtime.runtime.availableProcessors().intdiv(2) ?: 1
}
```

---

## 参考资料

- [Gradle官方文档](https://docs.gradle.org/)
- [Gradle用户手册](https://docs.gradle.org/current/userguide/userguide.html)
- [Gradle插件门户](https://plugins.gradle.org/)

---

**最后更新**: 2025-10-01
**维护者**: kk
