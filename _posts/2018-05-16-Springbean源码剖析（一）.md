# Spring-bean源码解析 #
5/16/2018 3:43:16 PM 
## 一、Bean工厂结构分析 ##
![](https://i.imgur.com/AewGAct.jpg)
### 1、BeanFactory： 所有bean工厂的基类 ###
    所有的bean工厂根据各自实现不同的功能并都从BeanFactory派生
### 2.ListableBeanFacotry：实现可以枚举所有bean实例对象的bean工厂类 ###
    ListableBeanFacotry（BeanFactory的 第一级派生类），提供了预加载所有bean定义信息的
    功能，就像XML bean工厂.ListableBeanFacotry接口有两个实现类：
    DefaultListableBeanFactory和StaticListableBeanFactory。
    DefaultListableBeanFactory提供了在访问bean定义之前注册所有bean实例的功能，而
    StaticListableBeanFactory只用于管理已经创建的bean实例HierarchicalBeanFactory：
    实现BeanFacotry的层次化结构. 
    由bean工厂实现的子接口，它可以是层次化结果的一部分.在可配置的方式的bean工厂
    （ConfigurableBeanFactory）接口中可以找到 相应于bean工厂的setParentBeanFactory方
    法，它允许以可配置的方式设置父工厂。
     
### 4、AutowireCapableBeanFacotry：实现可以自动装配和公布实例的bean工厂. ###
      BeanFactory的 第一级派生类。bean工厂类实现当前这个扩展BeanFacotry的接口，可以获得自动装配，
	  前提是他们想要为已经存在的bean实例公布它的功能。
	  当前这个BeanFactory的子接口不意味着在通常的应用代码内使用。
	  其他框架的集成代码可以利用当前这个接口来连接和填充已经存在的实例bean，
	  并且Spring不控制他的生命周期。这个特别有用，例如：对于webWork Actions和Tapestry 页面对象。
	  注意：当前这个接口表面上不是由ApplicationContext实现的，因为他们在应用代码内几乎不被使用。
	 也就是说，他们在应用上下文内可以获得，通过ApplicationContext的getAutowireCapableBeanFactory方法

### 5、ConfigurableBeanFactory:实现可配置的bean工厂. ###
      继承于HierarchicalBeanFactory，仅限于在Spring框架内部使用。用于专门访问bean工厂配置方法。
	  大部分由bean工厂实现配置接口。提供一个可以配置bean工厂的工具，除了在BeanFacotry接口内的bean工厂的客户端方法
	  当前bean工厂接口不意味着可以被使用在正常的应用代码中，
	  坚持BeanFactory或者ListableBeanFactory使用在典型的需求。这个扩展接口仅仅意味着允许框架内部插件应用，
	  以专门访问bean工厂的配置方法

### 6、ConfigurableListableBeanFactory：提供了工具以分析和修改bean定义以及预实例化单例 ###
      继承于ListableBeanFactory, AutowireCapableBeanFactory, ConfigurableBeanFactory，
      由大部分可枚举bean实例的bean工厂实现。仅限于Spring框架内部使用。
	  由大部可以枚举的bean工厂实现配置接口。此外，ConfigurableBeanFactory
      它还提供了工具以分析和修改bean定义以及预实例化单例

### 7、AbstractBeanFactory：bean工厂的抽象实现类 ###
      实现ConfigurableBeanFactory接口，继承FactoryBeanRegistrySupport。
	  AbstractBeanFactory:实现BeanFactory接口的抽象基类，
      提供所有的ConfigurableBeanFactory SPI功能。
	  这个类提供了一个单例缓存（通过他的基类DefaultSingletonBeanRegistry进行单例/原型判定，通过FactoryBean处理别名
	  和子bean定义的bean定义合并以及bean 销毁通过DisposableBean接口自定义销毁方法）
	  此外，他可以管理一个bean工厂的层级关系（继承关系）（在未知bean的情况下，委托给parent），通过
	  实现HierarchicalBeanFactory接口。
	  主要的模板方法由子类实现，方法有：getBeanDefinition：根据给定的bean名称检索bean定义。
      createBean，根据给定的bean定义创建一个bean实例。这些操作默认的实现在DefaultListableBeanFactory类
      和AbstractAutowireCapableBeanFactory类中可以找到

### 8、AbstractAutowireCapableBeanFactory： ###
      继承AbstractBeanFactory，实现AutowireCapableBeanFactory接口。
	  AbstractAutowireCapableBeanFactory：抽象bean工厂基类，实现
	  默认的bean创建，带有RootBeanDefinition类规定的全部的功能。 
      实现AutowireCapableBeanFactory接口，除此之外，还实现了
      AbstractBeanFactory的createBean方法。
	  提供bean的创建（通过构造器解析），属性填充和注入（包括自动装配），
      以及初始化。处理运行时bean的引用，解析管理集合，调用初始化方法等等。
      支持构造器自动装配，通过名称和类型注入属性。
	  主要的模板方法由子类实现，方法有：resolveDependency(DependencyDescriptor, String, Set, TypeConverter)
      用于根据类型自动装配。
	  如果一个工厂可以搜索它的bean定义，则搜索匹配的的bean通常通过这样的搜索被实现。
      对于其他类型的工厂，可以实现的简单的匹配算法
	  注意这个类不会假定或实现bean定义注册的能力。详见实现ListableBeanFactory接口
      和BeanDefinitionRegistry接口的DefaultListableBeanFactory类，
      他们分别代表了这样一个工厂的API和SPI的的视角

### 9、DefaultListableBeanFactory：ListableBeanFacotry接口的默认实现类 ###
      继承AbstractAutowireCapableBeanFactory，
      实现ConfigurableListableBeanFactory，BeanDefinitionRegistry, Serializable接口
      一个基于bean定义对象的成熟的bean工厂接口ListableBeanFactory和BeanDefinitionRegistry的默认实现类。
	  典型的用法是在访问bean之前，首先注册所有的bean定义。
	  因此，在本地的bean定义表格中Bean定义查找操作是一个低廉的操作, 
      操作是在预构建的bean定义元数据对象上。也可以用来当做一个单例的bean工厂，
      或者作为一个自定义bean工厂的基类 
	  注意：特定的bean定义格式的读取通常都是单独实现的，而不是作为一个bean工厂的子类.

### 10.StaticListableBeanFactory：ListableBeanFacotry接口的静态实现类 ###
	 StaticListableBeanFactory为BeanFactory的静态实现类，
     允许以编码的方式注册存在的单例实例。不支持原型bean或者别名


## 二、bean定义结构分析 ##

1. **BeanMetadataElement**
	Bean元数据元素 由携带配置源对象的bean元数据元素实现的接口
2. **AttributeAccessor** 属性访问器 接口定义一个通用规范，用于附加和访问任意对象的元数据。
3. AttributeAccessor的实现类，提供一个所有方法的基本实现。由子类进行扩展
4. **BeanMetadataAttributeAccessor**扩展AttributeAccessorSupport类，将属性作为BeanMetadataAttribute对象，以便跟踪定义源
5. **BeanDefinition**   一个BeanDefinition描述了一个有属性值，构造器参数值以及由具体是西安提供的进一步信息bean实例  这是一个最小化接口：主要的目的是允许一个BeanFactoryPostProcessor比如：PropertyPlaceholderConfigurer，反射和修改属性值和其他bean的元数据
6. **AbstractBeanDefinition** 用于具体的、成熟的BeanDefinition类的基类，分解GenericBeanDefinition、RootBeanDefinition和ChildBeanDefinition的常见属性
7. **RootBeanDefinition** 一个根bean定义表示合并后的bean定义，它在运行时支持Spring BeanFactory中的特定bean。 它可能是从多个原始的bean定义中创建的，它们彼此继承，通常被注册为GenericBeanDefinition。 根bean定义实质上是“统一”在运行时视图bean定义。 在配置时期，根bean定义还可以用于注册个人的bean定义。然而，自Spring 2.5版本以来，以编码方式注册bean的定义的优先方式是GenericBeanDefinition类。 GenericBeanDefinition有优势，允许动态定义双亲依赖，而不是“硬编码”作为根bean定义的角色
8. 
	
