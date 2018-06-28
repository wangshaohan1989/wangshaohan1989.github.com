# Spring-bean源码剖析之IOC容器基于XML的实现

## 一、综述 ##

## 二、具体实现实现过程 ##

### 1. 加载XML文档信息 ###
![](https://i.imgur.com/BRQTTqX.jpg)

### 2. 解析XML文档（Document) 
![](https://i.imgur.com/tQL5Eq0.jpg)

### 3. 解析alias、import标签 
![](https://i.imgur.com/iyMEuFh.jpg)


![](https://i.imgur.com/7tXx14D.jpg)	

### 4. 解析bean定义
![](https://i.imgur.com/ZqwmDXa.jpg)

### 5. 注册BeanDefinition 
![](https://i.imgur.com/VYJNab5.jpg)