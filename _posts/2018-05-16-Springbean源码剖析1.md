# Spring-bean源码解析 #
## 一、Bean工厂（Bean Factory）结构分析 ##
![Bean 工厂结构图](https://i.imgur.com/TSTHfYe.jpg)

### 1、BeanFactory： 所有bean工厂的基类
      所有的bean工厂根据各自实现不同的功能并都从BeanFactory派生。
### 2、ListableBeanFacotry： 实现可以枚举所有bean实例对象的bean工厂类
      ListableBeanFacotry（BeanFactory的 第一级派生类），提供了预加载所有bean定义信息的功能，就像XML bean工厂.
      ListableBeanFacotry接口有两个实现类：DefaultListableBeanFactory和StaticListableBeanFactory。
      DefaultListableBeanFactory提供了在访问bean定义之前注册所有bean实例的功能，而StaticListableBeanFactory只用于管理已经创建的bean实例
  
### 3、HierarchicalBeanFactory：实现BeanFacotry的层次化结构
      BeanFactory的 第一级派生类     
### 4、AutowireCapableBeanFacotry：实现可以自动装配和公布实例的bean工厂.
       BeanFactory的 第一级派生类
### 5、ConfigurableBeanFactory:实现可配置的bean工厂.
      继承于HierarchicalBeanFactory，仅限于在Spring框架内部使用。用于专门访问bean工厂配置方法。
   
### 6、ConfigurableListableBeanFactory：提供了工具以分析和修改bean定义以及预实例化单例
      继承于ListableBeanFactory, AutowireCapableBeanFactory, ConfigurableBeanFactory，
      由大部分可枚举bean实例的bean工厂实现。仅限于Spring框架内部使用
### 7、AbstractBeanFactory：bean工厂的抽象实现类
      实现ConfigurableBeanFactory接口，继承FactoryBeanRegistrySupport
### 8、AbstractAutowireCapableBeanFactory：
      继承AbstractBeanFactory，实现AutowireCapableBeanFactory接口。
### 9、DefaultListableBeanFactory：ListableBeanFacotry接口的默认实现类
      继承AbstractAutowireCapableBeanFactory，
      实现ConfigurableListableBeanFactory，BeanDefinitionRegistry, Serializable接口
      一个基于bean定义对象的成熟的bean工厂接口ListableBeanFactory和BeanDefinitionRegistry的默认实现类。
	  典型的用法是在访问bean之前，首先注册所有的bean定义。因此，在本地的bean定义表格中Bean定义查找操作是一个低廉的操作, 操作是在预构建的bean定义元数据对象上。也可以用来当做一个单例的bean工厂，或者作为一个自定义bean工厂的基类 注意：特定的bean定义格式的读取通常都是单独实现的，而不是作为一个bean工厂的子类.