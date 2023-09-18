---
title: SpringBoot 启动流程
date: 2023-09-08 10:18:50
tags:
- JavaWeb
- Spring
- SpringBoot
categories: SpringBoot
---

每个<quote>SpringBoot</quote>程序都有一个主入口，就是<quote>main()</quote>方法，在<quote>main()</quote>方法中调用<code>SpringApplication.run()</code>来启动整个程序。

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }
}
```

`SpringApplication.class`

```java
public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
  return run(new Class[]{primarySource}, args);
}
```

```java
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
  this.resourceLoader = resourceLoader;
  Assert.notNull(primarySources, "PrimarySources must not be null");
  this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
  // 根据classpath的类推断并设置webApplicationType
  this.webApplicationType = WebApplicationType.deduceFromClasspath();
  this.bootstrapRegistryInitializers = new ArrayList<>(getSpringFactoriesInstances(BootstrapRegistryInitializer.class));
  setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
  setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
  this.mainApplicationClass = deduceMainApplicationClass();
}
```

1. 调用静态`run()`方法时，我们首先创建一个`SpringApplication`的对象实例。在创建实例时，进行了一些基本的初始化操作。大体如下:
   1. 根据`classpath`的类推断并设置`webApplicationType`，根据源码可以看到包含三种容器`REACTIVE`、`NONE`、`SERVLET`，默认用的是`WebApplicationType.SERVLET`容器
   2. 从`META-INF/spring.factories`中获取`BootstrapRegistryInitializer`并放入集合`bootstrapRegistryInitializers`中
   3. 加载所有的`ApplicationContextInitializer`并放入集合`initializers`中
   4. 加载所有的`ApplicationListener`并放入集合`listeners`中
   5. 推断`mainApplicationClass`并赋值给`this.mainApplicationClass`

初始化完成后，执行`run()`方法。

```java
public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
  return run(new Class<?>[] { primarySource }, args);
}

public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
  return new SpringApplication(primarySources).run(args);
}
```

```java
public ConfigurableApplicationContext run(String... args) {
  long startTime = System.nanoTime();
  DefaultBootstrapContext bootstrapContext = createBootstrapContext();
  ConfigurableApplicationContext context = null;
  configureHeadlessProperty();
  // 查找并加载所有的SpringApplicationRunListener
  SpringApplicationRunListeners listeners = getRunListeners(args);
  // 通知所有的listeners程序启动
  listeners.starting(bootstrapContext, this.mainApplicationClass);
  try {
    ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
    ConfigurableEnvironment environment = prepareEnvironment(listeners, bootstrapContext, applicationArguments);
    Banner printedBanner = printBanner(environment);
    context = createApplicationContext();
    context.setApplicationStartup(this.applicationStartup);
    prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
    refreshContext(context);
    afterRefresh(context, applicationArguments);
    Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
    if (this.logStartupInfo) {
      new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), timeTakenToStartup);
    }
    listeners.started(context, timeTakenToStartup);
    callRunners(context, applicationArguments);
  }
  catch (Throwable ex) {
    if (ex instanceof AbandonedRunException) {
      throw ex;
    }
    handleRunFailure(context, ex, listeners);
    throw new IllegalStateException(ex);
  }
  try {
    if (context.isRunning()) {
      Duration timeTakenToReady = Duration.ofNanos(System.nanoTime() - startTime);
      listeners.ready(context, timeTakenToReady);
    }
  }
  catch (Throwable ex) {
    if (ex instanceof AbandonedRunException) {
      throw ex;
    }
    handleRunFailure(context, ex, null);
    throw new IllegalStateException(ex);
  }
  return context;
}
```

2. `run()`方法中首先调用方法`getRunListeners()`查找并加载所有的`SpringApplicationRunListener`（监听器），放入到`SpringApplicationRunListeners`这个集合类里面来进行统一管理。然后调用他们的`starting()`来通知所有的`listeners`（监听器）程序启动。

**getRunListeners方法:**

`SpringAppcation.java`

```java
private SpringApplicationRunListeners getRunListeners(String[] args) {
  ArgumentResolver argumentResolver = ArgumentResolver.of(SpringApplication.class, this);
  argumentResolver = argumentResolver.and(String[].class, args);
  List<SpringApplicationRunListener> listeners = getSpringFactoriesInstances(SpringApplicationRunListener.class,
  argumentResolver);
  SpringApplicationHook hook = applicationHook.get();
  SpringApplicationRunListener hookListener = (hook != null) ? hook.getRunListener(this) : null;
  if (hookListener != null) {
  listeners = new ArrayList<>(listeners);
  listeners.add(hookListener);
  }
  return new SpringApplicationRunListeners(logger, listeners, this.applicationStartup);
}


private <T> List<T> getSpringFactoriesInstances(Class<T> type, ArgumentResolver argumentResolver) {
  return SpringFactoriesLoader.forDefaultResourceLocation(getClassLoader()).load(type, argumentResolver);
}
```

`SpringFactoriesLoader.class`

```java
public static SpringFactoriesLoader forDefaultResourceLocation(@Nullable ClassLoader classLoader) {
	return forResourceLocation("META-INF/spring.factories", classLoader);
}
```

`getRunListeners()`方法从`spring.factories`中获取运行监听器。

我们`debug`跟踪一下。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/springboot%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B/getRunListeners.png)

可以看到，注册为`SpringApplicationRunListener`的实现类只有一个，就是`EventPublishingRunListener`。用来在SpringBoot的整个启动流程中的不同时间点发布不同类型的应用事件(`SpringApplicationEvent`)。`EventPublishingRunListener` 是 `SpringApplicationRunListener` 的子类，它会在应用程序启动期间发布多个事件。当应用程序上下文创建时，`EventPublishingRunListener` 会发布 `ApplicationStartingEvent` 事件；当应用程序运行时，它会发布 `ApplicationStartedEvent` 事件。这些事件可以被其他组件监听，例如自定义的事件监听器。通过监听这些事件，您可以在应用程序启动期间执行自定义的逻辑。

-----------------

我们接着回到`Run`方法中

```java

  try {
    ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
    ConfigurableEnvironment environment = prepareEnvironment(listeners, bootstrapContext, applicationArguments);

```

```java
private ConfigurableEnvironment prepareEnvironment(SpringApplicationRunListeners listeners,
      DefaultBootstrapContext bootstrapContext, ApplicationArguments applicationArguments) {
   // Create and configure the environment
   // 创建并配置environment
   ConfigurableEnvironment environment = getOrCreateEnvironment();
   // 配置 property sources 和 profiles
   configureEnvironment(environment, applicationArguments.getSourceArgs());
   // 将environment.getPropertySources()放在第一个位置
   ConfigurationPropertySources.attach(environment);
   // 运行监听器通知所有监听器环境准备完成
   listeners.environmentPrepared(bootstrapContext, environment);
   // 将'defaultProperties' property source移到最后
   DefaultPropertiesPropertySource.moveToEnd(environment);
   Assert.state(!environment.containsProperty("spring.main.environment-prefix"),
         "Environment prefix cannot be set via properties.");
  	// 将 environment 绑定到 {@link SpringApplication}
   bindToSpringApplication(environment);
  	// 环境转换成StandardEnvironment
   if (!this.isCustomEnvironment) {
      EnvironmentConverter environmentConverter = new EnvironmentConverter(getClassLoader());
      environment = environmentConverter.convertEnvironmentIfNecessary(environment, deduceEnvironmentClass());
   }
   ConfigurationPropertySources.attach(environment);
  // 返回环境配置对象ConfigurableEnvironment
   return environment;
}
```

```java
private ConfigurableEnvironment getOrCreateEnvironment() {
   if (this.environment != null) {
      return this.environment;
   }
   ConfigurableEnvironment environment = this.applicationContextFactory.createEnvironment(this.webApplicationType);
   if (environment == null && this.applicationContextFactory != ApplicationContextFactory.DEFAULT) {
      environment = ApplicationContextFactory.DEFAULT.createEnvironment(this.webApplicationType);
   }
   return (environment != null) ? environment : new ApplicationEnvironment();
}
```

3. 通过`prepareEnvironment()`方法进行环境的准备（包括配置`property`和对应的`profile`信息，将其放入`environment`变量），然后返回可配置环境`environment`

继续查看`run()`方法:

```java
try {
  ......
  Banner printedBanner = printBanner(environment);
  context = createApplicationContext();
  context.setApplicationStartup(this.applicationStartup);
  prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
  refreshContext(context);
  afterRefresh(context, applicationArguments);
  Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
  if (this.logStartupInfo) {
    new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), timeTakenToStartup);
  }
  listeners.started(context, timeTakenToStartup);
  callRunners(context, applicationArguments);
}
```

4. 调用`printBanner()`方法打印 <quote>banner</quote>信息

```java
private Banner printBanner(ConfigurableEnvironment environment) {
  if (this.bannerMode == Mode.OFF) {
    return null;
  } else {
    ResourceLoader resourceLoader = this.resourceLoader != null ? this.resourceLoader : new DefaultResourceLoader((ClassLoader)null);
    SpringApplicationBannerPrinter bannerPrinter = new SpringApplicationBannerPrinter((ResourceLoader)resourceLoader, this.banner);
    return this.bannerMode == Mode.LOG ? bannerPrinter.print(environment, this.mainApplicationClass, logger) : bannerPrinter.print(environment, this.mainApplicationClass, System.out);
  }
}

protected ConfigurableApplicationContext createApplicationContext() {
  return this.applicationContextFactory.create(this.webApplicationType);
}
```

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/springboot%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B/springbootbanner.png)

我们可以通过在类路径下添加`banner.txt`文件自定义<quote>banner</quote>打印信息或将`spring.banner.location`属性设置为此类文件的位置来更改启动时打印的横幅。如果文件的编码不是 UTF-8，您可以设置`spring.banner.charset`。

自定义`banner`示例：

`banner.txt`

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/springboot%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B/bannertxt.png)

打印效果:

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/springboot%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B/bannereffect.png)

继续查看`run()`方法:

```java
try {
  ......
  // 创建应用程序上下文对象
  context = createApplicationContext();
  // 设置下上下文对象的应用程序启动器
  context.setApplicationStartup(this.applicationStartup);
  // 准备上下文，配置容器的基本信息
  prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
  refreshContext(context);
  afterRefresh(context, applicationArguments);
  Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
  if (this.logStartupInfo) {
    new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), timeTakenToStartup);
  }
  listeners.started(context, timeTakenToStartup);
  callRunners(context, applicationArguments);
}
```

```java
private void prepareContext(DefaultBootstrapContext bootstrapContext, ConfigurableApplicationContext context, ConfigurableEnvironment environment, SpringApplicationRunListeners listeners, ApplicationArguments applicationArguments, Banner printedBanner) {
  // 设置容器的环境变量
  context.setEnvironment(environment);
  // 设置容器的ResourceLoader、ClassLoader、ConversionService
  this.postProcessApplicationContext(context);
  // 获取所有初始化器调用initialize()初始化
  this.applyInitializers(context);
  // 触发所有 SpringApplicationRunListener监听器的contextPrepared事件方法
  listeners.contextPrepared(context);
  bootstrapContext.close(context);
  // 打印启动日志和启动应用的Profile
  if (this.logStartupInfo) {
    this.logStartupInfo(context.getParent() == null);
    this.logStartupProfileInfo(context);
  }
	// Add boot specific singleton beans 添加启动特定的单例bean
  ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
  // 向beanFactory注册单例bean：命令行参数bean
  beanFactory.registerSingleton("springApplicationArguments", applicationArguments);
  if (printedBanner != null) {
    // 向beanFactory注册单例bean：banner bean
    beanFactory.registerSingleton("springBootBanner", printedBanner);
  }

  if (beanFactory instanceof AbstractAutowireCapableBeanFactory) {
    ((AbstractAutowireCapableBeanFactory)beanFactory).setAllowCircularReferences(this.allowCircularReferences);
    if (beanFactory instanceof DefaultListableBeanFactory) {
      ((DefaultListableBeanFactory)beanFactory).setAllowBeanDefinitionOverriding(this.allowBeanDefinitionOverriding);
    }
  }

  if (this.lazyInitialization) {
    context.addBeanFactoryPostProcessor(new LazyInitializationBeanFactoryPostProcessor());
  }
  context.addBeanFactoryPostProcessor(new PropertySourceOrderingBeanFactoryPostProcessor(context));
  // Load the sources，加载所有的资源
  Set<Object> sources = this.getAllSources();
  // 断言资源必须非空
  Assert.notEmpty(sources, "Sources must not be empty");
  // 将bean加载到应用上下文中
  this.load(context, sources.toArray(new Object[0]));
  // 触发所有SpringApplicationRunListener监听器contextLoaded方法
  listeners.contextLoaded(context);
}
```

继续回到`run()`方法:

```java
try {
  ......
  refreshContext(context);
  afterRefresh(context, applicationArguments);
  Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
  if (this.logStartupInfo) {
    new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), timeTakenToStartup);
  }
  listeners.started(context, timeTakenToStartup);
  callRunners(context, applicationArguments);
}
```

`refreshContext`

配置完容器基本信息后，刷新容器上下文`refreshContext`方法

```java
private void refreshContext(ConfigurableApplicationContext context) {
  if (this.registerShutdownHook) {
    shutdownHook.registerApplicationContext(context);
  }

  this.refresh(context);
}
```

```java
public final void refresh() throws BeansException, IllegalStateException {
    try {
        super.refresh();
    } catch (RuntimeException var3) {
        WebServer webServer = this.webServer;
        if (webServer != null) {
            webServer.stop();
        }

        throw var3;
    }
}
```

```java
    
public void refresh() throws BeansException, IllegalStateException {
        
  synchronized(this.startupShutdownMonitor) {
    StartupStep contextRefresh = this.applicationStartup.start("spring.context.refresh");
    // 上下文准备刷新
    this.prepareRefresh();
    // 刷新bean工厂，并返回bean工厂
    ConfigurableListableBeanFactory beanFactory = this.obtainFreshBeanFactory();
    // 准备bean工厂，以便进行上下文的使用
    this.prepareBeanFactory(beanFactory);
    try {
      // 允许在上下文子类中对 bean 工厂进行后置处理
      this.postProcessBeanFactory(beanFactory);
      StartupStep beanPostProcess = this.applicationStartup.start("spring.context.beans.post-process"); 
      // 在bean创建之前调用BeanFactoryPostProcessors后置处理方法
      this.invokeBeanFactoryPostProcessors(beanFactory); 
      // 注册BeanPostProcessor
      this.registerBeanPostProcessors(beanFactory);
      beanPostProcess.end();   
      // 注册DelegatingMessageSource
      this.initMessageSource();
      // 注册一个applicationEventMulticaster的广播器
      this.initApplicationEventMulticaster(); 
      // 在特定上下文子类中初始化其他特殊 bean
      this.onRefresh();
      // 注册监听器Listeners
      this.registerListeners(); 
      // 实例化所有剩余的（非懒加载）单例。
      this.finishBeanFactoryInitialization(beanFactory);
      // 发布对应事件
      this.finishRefresh();  
    } catch (BeansException var10) {      
      if (this.logger.isWarnEnabled()) {
        this.logger.warn("Exception encountered during context initialization - cancelling refresh attempt: " + var10);         
      }
      this.destroyBeans();
      this.cancelRefresh(var10);
      throw var10;
    } finally {
      this.resetCommonCaches();        
      contextRefresh.end();       
    }     
  } 
}
```

刷新容器上下文`refreshContext`方法之后看到`afterRefresh`是一个空方法，主要用于开发者拓展使用。

`run()`:

```java
try {
  ......
  refreshContext(context);
  afterRefresh(context, applicationArguments);
  Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
  if (this.logStartupInfo) {
    new StartupInfoLogger(this.mainApplicationClass).logStarted(getApplicationLog(), timeTakenToStartup);
  }
  listeners.started(context, timeTakenToStartup);
  callRunners(context, applicationArguments);
}
```

容器配置都完成之后，这时监听应用上下文启动完成所有的运行监听器调用 `started()` 方法，发布监听应用的启动事件

```java
void started(ConfigurableApplicationContext context, Duration timeTaken) {
  this.doWithListeners("spring.boot.application.started", (listener) -> {
    listener.started(context, timeTaken);
  });
}
```

后续继续执行`callRunners`方法遍历所有`runner`，调用`run`方法。

上述都完成之后到了最后一步，执行`listener.running`方法

```java
try {
  Duration timeTakenToReady = Duration.ofNanos(System.nanoTime() - startTime);
  // 所有的运行监听器调用running()方法,监听应用上下文
  listeners.ready(context, timeTakenToReady);
  return context;
} catch (Throwable var11) {
  this.handleRunFailure(context, var11, (SpringApplicationRunListeners)null);
  throw new IllegalStateException(var11);
}
```

运行所有运行监听器，该方法执行以后`SpringApplication.run()`也就算执行完成了，那么`SpringBoot`的`ApplicationContext`也就启动完成了。

流程图：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/springboot%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B/springbootrun.svg)
