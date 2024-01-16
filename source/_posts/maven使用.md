---
title: maven使用
top: false
cover: false
toc: true
mathjax: true
date: 2024-01-16 15:47:22
password:
summary: 工具篇
tags: 
- maven
- 模块设计
categories:
---

## Maven使用

### 前言

两年前写的一篇文章：`maven从入门到入坑`已经对maven的全貌做了详细介绍，这里不再赘述。仅从一个自然的思维顺序简要地描述maven的使用，力求做到与实际紧密结合

------

### 背景

最开始手动导入jar包面临以下问题：`各个模块都在其目录下导入会导致重复`、`多个包如何管理版本`、`包与模块设计的理念是否应该一致`

### 概述

Maven就是专门`为Java项目打造的管理和构建工具`，它的主要功能有：

- 提供了一套标准化的项目结构；
- 提供了一套标准化的构建流程（编译，测试，打包，发布……）；
- 提供了一套依赖管理机制。

### 具体使用

#### maven配置文件结构

以一个标准化地maven配置文件为例，简要介绍一些标签用途：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings>
<!--maven的本地仓库地址-->
<localRepository>/Users/zyz/.m2/repository</localRepository>
 <!--maven的镜像仓库 也就是盛名jar包从哪个仓库拉取 选取时考虑两个点：jar包是否全面、访问速度是否够快-->
    <mirrors>
        <mirror>
            <id>alimaven</id>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
          	<!--仓库类型：中央仓库 仓库分类：中央仓库、远程仓库、本地仓库-->
            <mirrorOf>central</mirrorOf>
        </mirror>
    </mirrors>
  <!--代理服务器：和VPN同理，让实际的ip地址变得透明，从外界来看，实际请求的发出方是配置的地址-->
  <proxy></proxy>
  <!--具体开发环境配置：比如开发环境、测试环境将jar包部署到各自的仓库地址-->
  <activeProfiles>nexus</activeProfiles>
  <profiles>
    <profile>
      <!--名为nexus的开发环境-->
       <id>nexus</id> 
        <repositories>
            <repository>
                <id>nexus</id>
                <name>local private nexus</name>
                <url>http://maven.oschina.net/content/groups/public/</url>
                <releases>
                    <enabled>true</enabled>
                </releases>
                <snapshots>
                    <enabled>false</enabled>
                </snapshots>
            </repository>
        </repositories>
        <!--插件仓库地址-->
        <pluginRepositories>
            <pluginRepository>
            <id>nexus</id>
            <name>local private nexus</name>
            <url>http://maven.oschina.net/content/groups/public/</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            </pluginRepository>
        </pluginRepositories>
    </profile></profiles>
</settings>
 
```

#### maven说明文件

```xml
<project ...>
	<modelVersion>4.0.0</modelVersion>
  <!--项目结构与版本-->
	<groupId>com.ke.nervecenter</groupId>
  <artifactId>utopia-nerve-center</artifactId>
  <version>1.2.16</version>
  <name>utopia-nerve-center</name>
  <description>Demo project for ke.com</description>
  <!--版本约定-->
	<properties>
      <java.version>1.8</java.version>
	</properties>
  <!--模块约定-->
  <modules>
        <module>utopia-nerve-center-api</module>
        <module>utopia-nerve-center-db</module>
        ...
  </modules>
  <!--具体依赖-->
	<dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-collections4</artifactId>
        <version>4.1</version>
  </dependency>
  <!--将jar包（正式/测试）部署到哪个仓库-->
  <distributionManagement>
        <repository>
            <id>releases</id>
            <name>Releases</name>
            <url></url>
        </repository>
        <snapshotRepository>
            <id>snapshots</id>
            <name>Snapshots</name>
            <url></url>
        </snapshotRepository>
  </distributionManagement>
  <!--代码格式的校验插件：需要声明id、规则文件地址、出现错误后的处理等-->
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-checkstyle-plugin</artifactId>
    <version>3.1.2</version>
    <executions>
      <execution>
        <id>checkstyle</id>
        <phase>validate</phase>
        <goals>
          <goal>check</goal>
        </goals>
        <configuration>
          <configLocation>checkstyle.xml</configLocation>
          <suppressionsLocation>checkstyle-suppression.xml</suppressionsLocation>
          <failOnViolation>true</failOnViolation>
          <failsOnError>true</failsOnError>
          <consoleOutput>true</consoleOutput>
          <includeTestSourceDirectory>true</includeTestSourceDirectory>
        </configuration>
      </execution>
    </executions>
    <dependencies>
      <dependency>
        <groupId>com.puppycrawl.tools</groupId>
        <artifactId>checkstyle</artifactId>
        <version>9.0.1</version>
      </dependency>
    </dependencies>
  </plugin>
</project>
```

#### maven常用命令

1. 修改各个模块的版本并部署

   ```sh
   mvn versions:set -DnewVersion=2.9.8-SNAPSHOT
   mvn -U clean deploy  -Dmaven.test.skip=true
   ```

2. 测试

   ```sh
   mvn test
   ```

#### IDEA中的maven插件

Maven-helper

![image-20240116164747068](https://cdn.jsdelivr.net/gh/YuZhe727/xiaozhe_img/img4/202401161647182.png)

------

> 日拱一卒，功不唐捐
