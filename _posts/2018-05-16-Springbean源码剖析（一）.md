# Spring-bean源码解析之类结构分析

## 一、Bean工厂类结构分析
![](https://i.imgur.com/AewGAct.jpg)

### 1、BeanFactory： 所有bean工厂的基类 

	所有的bean工厂根据各自实现不同的功能并都从BeanFactory派生

### 2.ListableBeanFacotry：实现可以枚举所有bean实例对象的bean工厂类
	ListableBeanFacotry（BeanFactory的 第一级派生类），提供了预加载所有bean定义信息的
    功能，就像XML bean工厂.ListableBeanFacotry接口有两个实现类：
    DefaultListableBeanFactory和StaticListableBeanFactory。
	DefaultListableBeanFactory提供了在访问bean定义之前注册所有bean实例的功能，而
	StaticListableBeanFactory只用于管理已经创建的bean实例HierarchicalBeanFactory：实现
    BeanFacotry的层次化结构. 
    由bean工厂实现的子接口，它可以是层次化结果的一部分.在可配置的方式的bean工厂（ConfigurableBeanFactory）
	接口中可以找到 相应于bean工厂的setParentBeanFactory方法，它允许以可配置的方式设置父工厂。
     
### 3、AutowireCapableBeanFacotry：实现可以自动装配和公布实例的bean工厂. 
    BeanFactory的 第一级派生类。bean工厂类实现当前这个扩展BeanFacotry的接口，可以获得自动装配，
	前提是他们想要为已经存在的bean实例公布它的功能。
	当前这个BeanFactory的子接口不意味着在通常的应用代码内使用。
	其他框架的集成代码可以利用当前这个接口来连接和填充已经存在的实例bean，
	并且Spring不控制他的生命周期。这个特别有用，例如：对于webWork Actions和Tapestry 页面对象。
	注意：当前这个接口表面上不是由ApplicationContext实现的，因为他们在应用代码内几乎不被使用。
	也就是说，他们在应用上下文内可以获得，通过ApplicationContext的getAutowireCapableBeanFactory方法

### 4、ConfigurableBeanFactory:实现可配置的bean工厂. 
    继承于HierarchicalBeanFactory，仅限于在Spring框架内部使用。用于专门访问bean工厂配置方法。
	大部分由bean工厂实现配置接口。提供一个可以配置bean工厂的工具，除了在BeanFacotry接口内的bean工厂的客户
    端方法。当前bean工厂接口不意味着可以被使用在正常的应用代码中，坚持BeanFactory或者ListableBeanFactory
    使用在典型的需求。这个扩展接口仅仅意味着允许框架内部插件应用，以专门访问bean工厂的配置方法

### 5、ConfigurableListableBeanFactory：提供了工具以分析和修改bean定义以及预实例化单例 
    继承于ListableBeanFactory, AutowireCapableBeanFactory, ConfigurableBeanFactory，
    由大部分可枚举bean实例的bean工厂实现。仅限于Spring框架内部使用。
	由大部可以枚举的bean工厂实现配置接口。此外，ConfigurableBeanFactory
    它还提供了工具以分析和修改bean定义以及预实例化单例

### 6、AbstractBeanFactory：bean工厂的抽象实现类 
    实现ConfigurableBeanFactory接口，继承FactoryBeanRegistrySupport。
	AbstractBeanFactory:实现BeanFactory接口的抽象基类，
    提供所有的ConfigurableBeanFactory SPI功能。
	这个类提供了一个单例缓存（通过他的基类DefaultSingletonBeanRegistry进行单例/原型判定，通过
	FactoryBean处理别名和子bean定义的bean定义合并以及bean 销毁通过DisposableBean接口自定义销毁方法）
	此外，他可以管理一个bean工厂的层级关系（继承关系）（在未知bean的情况下，委托给parent），
    通过实现HierarchicalBeanFactory接口。主要的模板方法由子类实现，方法有：getBeanDefinition：根据给定
    的bean名称检索bean定义。createBean，根据给定的bean定义创建一个bean实例。这些操作默认的实现在
    DefaultListableBeanFactory类和AbstractAutowireCapableBeanFactory类中可以找到

### 7、AbstractAutowireCapableBeanFactory： 
    继承AbstractBeanFactory，实现AutowireCapableBeanFactory接口。
	AbstractAutowireCapableBeanFactory：抽象bean工厂基类，实现
	默认的bean创建，带有RootBeanDefinition类规定的全部的功能。 
    实现AutowireCapableBeanFactory接口，除此之外，还实现了
    AbstractBeanFactory的createBean方法。
	供bean的创建（通过构造器解析），属性填充和注入（包括自动装配），
    以及初始化。处理运行时bean的引用，解析管理集合，调用初始化方法等等。
    支持构造器自动装配，通过名称和类型注入属性。
	主要的模板方法由子类实现，
	方法有resolveDependency(DependencyDescriptor, String, Set, TypeConverter)
    用于根据类型自动装配。如果一个工厂可以搜索它的bean定义，则搜索匹配的的bean通常通过这样的搜索被实现。
    对于其他类型的工厂，可以实现的简单的匹配算法
	注意这个类不会假定或实现bean定义注册的能力。详见实现ListableBeanFactory接口
    和BeanDefinitionRegistry接口的DefaultListableBeanFactory类，
    他们分别代表了这样一个工厂的API和SPI的的视角

### 8、DefaultListableBeanFactory：ListableBeanFacotry接口的默认实现类 
    继承AbstractAutowireCapableBeanFactory，
    实现ConfigurableListableBeanFactory，BeanDefinitionRegistry, Serializable接口
    一个基于bean定义对象的成熟的bean工厂接口ListableBeanFactory和BeanDefinitionRegistry的默认实现类。
	典型的用法是在访问bean之前，首先注册所有的bean定义。
	因此，在本地的bean定义表格中Bean定义查找操作是一个低廉的操作, 
    操作是在预构建的bean定义元数据对象上。也可以用来当做一个单例的bean工厂，
    或者作为一个自定义bean工厂的基类 
	注意：特定的bean定义格式的读取通常都是单独实现的，而不是作为一个bean工厂的子类.

### 9.StaticListableBeanFactory：ListableBeanFacotry接口的静态实现类 
	 StaticListableBeanFactory为BeanFactory的静态实现类，
     允许以编码的方式注册存在的单例实例。不支持原型bean或者别名


## 二、bean定义类结构分析 

1. **BeanMetadataElement**
	Bean元数据元素 由携带配置源对象的bean元数据元素实现的接口
2. **AttributeAccessor** 属性访问器 接口定义一个通用规范，用于附加和访问任意对象的元数据。
3. AttributeAccessor的实现类，提供一个所有方法的基本实现。由子类进行扩展
4. **BeanMetadataAttributeAccessor**扩展AttributeAccessorSupport类，将属性作为BeanMetadataAttribute对象，以便跟踪定义源
5. **BeanDefinition**   一个BeanDefinition描述了一个有属性值，构造器参数值以及由具体是西安提供的进一步信息bean实例  这是一个最小化接口：主要的目的是允许一个BeanFactoryPostProcessor比如：PropertyPlaceholderConfigurer，反射和修改属性值和其他bean的元数据
6. **AbstractBeanDefinition** 用于具体的、成熟的BeanDefinition类的基类，分解GenericBeanDefinition、RootBeanDefinition和ChildBeanDefinition的常见属性
7. **RootBeanDefinition** 一个根bean定义表示合并后的bean定义，它在运行时支持Spring BeanFactory中的特定bean。 它可能是从多个原始的bean定义中创建的，它们彼此继承，通常被注册为GenericBeanDefinition。 根bean定义实质上是“统一”在运行时视图bean定义。 在配置时期，根bean定义还可以用于注册个人的bean定义。然而，自Spring 2.5版本以来，以编码方式注册bean的定义的优先方式是GenericBeanDefinition类。 GenericBeanDefinition有优势，允许动态定义双亲依赖，而不是“硬编码”作为根bean定义的角色
8. 
	


## 三、bean包装类结构分析 ##


## 四、资源类结构分析 ##


## 五、bean读取类结构分析 ##


## 六、bean定义解析类结构分析 ##




## 七、bean异常类结构分析 ##

![](https://i.imgur.com/v44kLzu.jpg)

###1、Spring-bean异常类的基本结构###
  
1. **NestedRuntimeException**：运行时异常类

2. **BeansException**：spring-bean内异常类的基类，派生于NestedRuntimeException运行时异常类。所以
   spring-bean内的异常都是运行时异常类。

3. **BeansException子类的分类**：<br/>
  （1）**按照是否为致命异常划分** ： 也就是继承于FatalBeanException的子类均为致命异常类<br/>
  （2）**按照异常发生位置划分**：<span style='color:#CCBC14'><strong>属性异常</strong>（如上图中的黄色标签）</span>和<span style="color:blue"><strong>bean异常</strong>（上图中的蓝色标签）</span>

![](https://i.imgur.com/PBhYjJT.jpg)
4. **非致命属性异常类：**

   ***PropertyBatchUpdateException***：组合异常，由个别的PropertyAccessException实例所构成。	
    类对象在绑定处理过程的是被创建，并且如果必要的话添加这些错误。当遇到应用程序层级的
    PropertyAccessExceptions异常，这个绑定处理过程继续，应用那些可以应用的变化并且存储该类对象中的拒绝更改
	
   ***PropertyAccessException***：属性访问相关的异常的基类

   ***TypeMismatchException***：当视尝试设置一个bean属性时抛出的一个类型不匹配异常

   ***MethodInvocationException***：当一个bean属性的getter或者setter方法抛出一个异常的时候抛出

   ***ConversionNotSupportedException***：当一个bean的属性发现没有合适的编辑器或者转换器的时候抛出的异常

5. **致命属性异常类**：
 
    ***InvalidPropertyException***:当引用一个无效的bean属性的时候抛出的异常

    ***NotWritablePropertyException***:试图设置一个不可写的，没有setter方法的属性的异常

    ***NullValueInNestedPathException***:当导航一个无效的嵌套的属性路径时候遇到一个空指针异常

    ***NotReadablePropertyException***:试图获得一个属性值的异常 这是不可读的，因为没有getter方法

6. **非致命bean异常类**：

	***BeanNotOfRequiredTypeException***: 当一个bean不匹配期望的类型的时候抛出的异常

	***BeanIsNotAFactoryException***:当一个bean不是一个工厂，但是一个用户试图获取根据给定的bean名称获取这个工
	厂的是抛出的异常.一个bean是否是一个工厂取决于它是否实现FactoryBean这个接口。
	继承于BeanIsNotAFactoryException接口

	***NoSuchBeanDefinitionException***:当一个BeanFactory调用一个bean实例化的时候没有发现一个定义的时候抛出的
	异常这可能意味着一个不存在的bean，一个不唯一的bean或者一个手动注册的单例实例化没有关联bean定义的bean

	***NoUniqueBeanDefinitionException***:当一个bean工厂BeanFactory调用一个ban实例的时候被发现有匹配的有多个
	候选然而只需要匹配一个期望的时候。继承于NoSuchBeanDefinitionException
	

7. **致命bean异常类**：

	**BeanCreationException**:在一个BeanFactory bean工厂当试图从一个bean定义创建一个bean的时候遇到一个
错误抛出的异常
	***BeanCurrentlyInCreationException***:在引用一个当前正在创建的bean的时候抛出的异常,通常发生在构造器自动装
	配与当前构建的bean匹配时.继承于BeanCreationException

	**BeanCreationNotAllowedException**：尽管bean的创建目前不允许bean被请求（例如，在bean工厂的关闭阶段，但是在bean被请求的情况下抛出异常。.继承于BeanCreationException

    ***BeanInstantiationException***:当实例化一个bean是失败的时候抛出。携带有实例化失败的bean类

	***BeanExpressionException***:表示表达式评估尝试失败的异常

	***FactoryBeanNotInitializedException***:如果一个bean还没有完成的初始化（比如因为他调用一个循环引用）的时候
 	调用FactoryBean的getObject方法的时候抛出的异常.注意：与FactoryBean的循环引用不能通过急切地缓存单例实例来
	解决，就像普通bean一样.原因是每个FactoryBean在他返回创建bean之前需要一个完整的初始化，而仅仅只需要初始化特殊
	的正常bean。也就说，如果一个合作bean实际在初始化的时候调用他们 而不是仅仅保存引用

	***CannotLoadBeanClassException***:当bean工厂BeanFactory不能加载一个指定bean类的信息的时候抛出的异常

    **BeanDefinitionStoreException**：当bean工厂遇到一个无效的bean定义时抛出的异常