# DevGuideline(APL) 別紙2.WSDL/XSDからのJavaソース生成手順
# 目次
- [1. はじめに](#1-はじめに)
  - [1.1 本書の目的](#11-本書の目的)
  - [1.2 本書の利用者](#12-本書の利用者)
- [2. 使用するツール](#2-使用するツール)
  - [2.1. ツールの概要](#21-ツールの概要)
  - [2.2. ツールのセットアップ](#22-ツールのセットアップ)
- [3. WSDLからのJavaソース生成手順](#3-wsdlからのjavaソース生成手順)
  - [3.1. build.gradleの編集](#31-buildgradleの編集)
    - [3.1.1. ソースフォルダの追加](#311-ソースフォルダの追加)
    - [3.1.2. WSDL/XSDからJavaのソースコードを生成するタスクの追加](#312-wsdlxsdからjavaのソースコードを生成するタスクの追加)
    - [3.1.3. 依存関係の追加](#313-依存関係の追加)
  - [3.2. DTOの生成](#32-dtoの生成)
- [4. 付録](#4-付録)

  

# 1. はじめに

## 1.1 本書の目的

SOAP Frontを利用したアプリケーションの開発の内、外部サービスとのI/Fに使用するDTOの作成方法を説明する。
    

## 1.2 本書の利用者

SOAP Frontを利用し、外部サービスとSOAP通信を行うアプリケーションの開発を行う方を対象とする。
    

# 2. 使用するツール
## 2.1. ツールの概要

WSDL/XSDからJavaのソースコードを生成する為に、XJC バインディングコンパイラ(以降xjcと記載)を使用する。  
xjcの実行は、Gradleのビルドタスクの中にxjc(Antタスク)を実行する処理を追加して、コンパイル時に実行される様にする。
    

## 2.2. ツールのセットアップ

特に無し。
    

# 3. WSDLからのJavaソース生成手順
## 3.1. build.gradleの編集

`build.gradle`に以下の変更を行う。

1. ソースフォルダの追加
2. WSDL/XSDからJavaのソースコードを生成するタスクの追加
3. 依存関係の追加

以下に、それぞれの変更内容を記載する。


### 3.1.1 ソースフォルダの追加

  xjcが出力するソースファイルをビルドの対象に加えるために、下記を追加する。
  ```groovy
  sourceSets {
    main {
      java {
        srcDir 'src/main/java'
        srcDir 'build/generated-sources/jaxb'
      }
    }
  }
  ```
  xjcが出力するソースファイルは、`build/generated-sources/jaxb` に格納する。  
  xjcによって自動生成したソースファイルであることが分かる様に、ソースの格納フォルダは変更しない事を推奨する。
    

### 3.1.2 WSDL/XSDからJavaのソースコードを生成するタスクの追加

  以下のxjcを実行するタスクを、`dependencies`の前に追加する。（dependenciesから参照する為）  
  下記の`IntAswPrintingService_01/IntAswPrintingService_01.wsdl`の部分は、変換対象のファイル名に読み替えること。
  ```groovy
  configurations {
    // xjcを特定するために構成を追加
    jaxb
  }

  task genJaxb {
    ext.sourcesDir = "${buildDir}/generated-sources/jaxb"
    ext.classesDir = "${buildDir}/classes/jaxb"
    // ※※※　変換対象のWSDLを設定　※※※
    ext.schema = "src/main/resources/IntAswPrintingService_01/IntAswPrintingService_01.wsdl"

    outputs.dir classesDir

    doLast() {
      project.ant {
        taskdef name: "xjc", classname: "com.sun.tools.xjc.XJCTask",
            classpath: configurations.jaxb.asPath
        mkdir(dir: sourcesDir)
        mkdir(dir: classesDir)

          xjc(destdir: sourcesDir, schema: schema, encoding: "UTF-8") {
              arg(value: "-wsdl")
            produces(dir: sourcesDir, includes: "**/*.java")
          }

          javac(destdir: classesDir, source: 11, target: 11, debug: true,
              debugLevel: "lines,vars,source", includeantruntime: false,
              classpath: configurations.jaxb.asPath, encoding: "UTF-8") {
            src(path: sourcesDir)
            include(name: "**/*.java")
            include(name: "*.java")
          }

          copy(todir: classesDir) {
              fileset(dir: sourcesDir, erroronmissingdir: false) {
              exclude(name: "**/*.java")
          }
        }
      }
    }
  }
  ```

  

### 3.1.3 依存関係の追加

  xjcが生成するソースファイルのコンパイルに必要なjar(前半３つ)と、xjcで生成したクラス、xjcの依存関係を追加する。
  ```groovy
  dependencies {

    // ・・・省略・・・

    // JAXB
    implementation 'org.springframework.ws:spring-ws-core:3.0.10.RELEASE'
    implementation "org.glassfish.jaxb:jaxb-runtime:2.3.3"
    implementation 'jakarta.activation:jakarta.activation-api:1.2.2'

    implementation(files(genJaxb.classesDir).builtBy(genJaxb))
    jaxb "org.glassfish.jaxb:jaxb-xjc:2.3.3"
  }
  ```

  

## 3.2 DTOの生成

  - 変換対象のファイル(WSDL/XSD)を下記のフォルダに格納する。
    
    > src/main/resources/サービス名
    
    “サービス名”の部分は、対象のWebサービスが分かる名前にし、WSDLファイルから参照されるXSDファイルも同フォルダに格納する。  
    WSDLファイルの格納パス、ファイル名は、[3.1.2](#312-wsdlxsdからjavaのソースコードを生成するタスクの追加) でbuild.gradle に記載した値と一致させる必要がある。
    
  - コマンドプロンプトで下記のコマンドを実行する。
    ```
    cd プロジェクトフォルダ(gradlewが格納されているフォルダ)
    gradlew compileJava
    ```
    STSを使用している場合は、Gradleタスクビューから"build"→"classes"を実行しても良い。
    
  - 生成されたソースファイルの確認  
    プロジェクトの`build/generated-sources/jaxb`配下に自動生成されたソースが存在することを確認する。

  

# 付録
- common-application-blank-projectのbuild.gradleを変更した例
```groovy
plugins {
    id 'org.springframework.boot' version '2.4.3'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
    id "com.github.spotbugs" version "4.2.3"
    id 'checkstyle'
    id 'jacoco'
    id 'maven-publish'
    id 'com.thinkimi.gradle.MybatisGenerator' version '2.3'
}

group = 'jp.co.ana.cas'
// developへマージした際に適用するバージョン情報を記載
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'
targetCompatibility = '11'

configurations {
    developmentOnly
    runtimeClasspath {
        extendsFrom developmentOnly
    }
    compileOnly {
        extendsFrom annotationProcessor
    }
    // SLF4Jを使わない設定
    all*.exclude module : 'spring-boot-starter-logging'
    // ▼▼▼MyBatisGenerator用設定▼▼▼
    mybatisGenerator
    // ▲▲▲MyBatisGenerator用設定▲▲▲
    
    [apiElements, runtimeElements].each {
            it.outgoing.artifacts.removeIf { it.buildDependencies.getDependencies(null).contains(jar) }
            it.outgoing.artifact(bootJar)
   }
}

repositories {
    // ▼▼▼ASYNET用設定▼▼▼
    maven {
        name 'spring'
        url = 'https://repo.spring.io/release'
    }
    // ▲▲▲ASYNET用設定▲▲▲
    mavenCentral()
}

// ★ ココから =========>
// 自動生成したソースファイルを組み込む
sourceSets {
    main {
        java {
            srcDir 'src/main/java'
            srcDir 'build/generated-sources/jaxb'
        }
    }
}

configurations {
    jaxb
}

task genJaxb {
  ext.sourcesDir = "${buildDir}/generated-sources/jaxb"
  ext.classesDir = "${buildDir}/classes/jaxb"
  // ※※※　変換対象のWSDLを設定　※※※
  ext.schema = "src/main/resources/IntAswPrintingService_01/IntAswPrintingService_01.wsdl"

  outputs.dir classesDir

  doLast() {
    project.ant {
      taskdef name: "xjc", classname: "com.sun.tools.xjc.XJCTask",
          classpath: configurations.jaxb.asPath
      mkdir(dir: sourcesDir)
      mkdir(dir: classesDir)

        xjc(destdir: sourcesDir, schema: schema, encoding: "UTF-8") {
            arg(value: "-wsdl")
          produces(dir: sourcesDir, includes: "**/*.java")
        }

        javac(destdir: classesDir, source: 11, target: 11, debug: true,
            debugLevel: "lines,vars,source", includeantruntime: false,
            classpath: configurations.jaxb.asPath, encoding: "UTF-8") {
          src(path: sourcesDir)
          include(name: "**/*.java")
          include(name: "*.java")
        }

        copy(todir: classesDir) {
            fileset(dir: sourcesDir, erroronmissingdir: false) {
            exclude(name: "**/*.java")
        }
      }
    }
  }
}
// ★ <=========　ココまで

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-jdbc'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-web-services'

    // MyBatis用
    implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.1.4'
    implementation group: 'org.mybatis.generator', name: 'mybatis-generator-core', version: '1.4.0'

    compileOnly 'org.projectlombok:lombok'
    runtimeOnly 'org.postgresql:postgresql'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }

    // Mockito用
    testImplementation 'org.mockito:mockito-junit-jupiter'
    testImplementation 'org.mockito:mockito-core:3.12.4'
    testImplementation 'org.mockito:mockito-inline:3.12.4'

    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'

    // Log4J2用設定
    implementation 'org.springframework.boot:spring-boot-starter-log4j2'

    // OpenAPI用
    implementation 'io.springfox:springfox-swagger2:2.8.0'
    implementation 'io.springfox:springfox-swagger-ui:2.8.0'
    implementation 'org.openapitools:jackson-databind-nullable:0.1.0'

    // AWS SDK
    implementation platform('com.amazonaws:aws-java-sdk-bom:1.11.1000')
    implementation group: 'com.amazonaws', name: 'aws-java-sdk-core', version: '1.12.22'
    implementation 'com.amazonaws:aws-java-sdk-s3'

    // JSON用
    implementation 'com.fasterxml.jackson.core:jackson-databind:2.12.3'
    implementation 'com.fasterxml.jackson.core:jackson-core:2.12.3'
    implementation 'com.fasterxml.jackson.core:jackson-annotations:2.12.3'

    // テストでlombokを使うための設定
    //testRuntime('org.junit.platform:junit-platform-launcher');
    testCompileOnly 'org.projectlombok:lombok'
    testAnnotationProcessor 'org.projectlombok:lombok'
    
    // DBUnit
    testImplementation 'org.dbunit:dbunit:2.7.0'
    testImplementation 'com.github.springtestdbunit:spring-test-dbunit:1.3.0'

// ★ ココから =========>
    // JAXB
    implementation 'org.springframework.ws:spring-ws-core:3.0.10.RELEASE'
    implementation "org.glassfish.jaxb:jaxb-runtime:2.3.3"
    implementation 'jakarta.activation:jakarta.activation-api:1.2.2'

    implementation(files(genJaxb.classesDir).builtBy(genJaxb))
    jaxb "org.glassfish.jaxb:jaxb-xjc:2.3.3"
// ★ <=========　ココまで
}

// 以降は変更しないので省略
```
