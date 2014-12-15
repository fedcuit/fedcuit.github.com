---
layout: post
title: 在功能测试中使用Togglz
date: 2014-12-15 21:15
comments: true
categories: togglz junit
---
## Togglz

因为目前的这个项目需要每个迭代(两周)都发布一次, 并且有很多的story都是需要做A/B testing的 (简单讲，A/B testing就是对同一个功能有两种不同实现或是设计，发布之后通过用户反馈来判断某种设计更受欢迎)，因此就有了这样的需求: 如果一个story在当前迭代中无法完成，那样需要给它加上toggle, 这样只需在生产环境将toggle关闭，不为担心未完成的功能被release出去；另一方面，如果发现新的实现、体验不被欢迎，那么只需将toggle关闭就可以快速地返回到旧的实现了。为了实现上面的需要了，我们选择了[togglz](http://www.togglz.org/)。

但是这种方式进行了一段时间后，问题开始出现了。功能测试中的有些测试方法是针对旧的体验书写的，另外还有一些测试方法是对同一功能的新的体验书写的，我们想同时保留针对新旧两种体验的测试方法，但是想这样做的话就需要手动来打开或是关闭对应的测试方法，比如说现在需要对`AwesomeFeature`做A/B testing, 我们添加了一个toggle叫 `NEW_DESIGN_FOR_AWESOME_FEATURE`, 对应的就有两种情况

* 当这个toggle开启的时候 `NEW_DESIGN_FOR_AWESOME_FEATURE = true`, 对应的测试需要修改为

``` java
@Ignore
@Test
public void shouldDoSomethingForAwesomeFeature() {}
		
@Test
public void shouldDoSomethingForNewDesignForAwesomeFeature() {}
```
		
* 当这个toggle关闭的时候 `NEW_DESIGN_FOR_AWESOME_FEATURE = false`, 对应的测试需要修改为
	
``` java
@Test
public void shouldDoSomethingForAwesomeFeature() {}
		
@Ignore
@Test
public void shouldDoSomethingForNewDesignForAwesomeFeature() {}
```

## RunIf

我们需要根据toggle的配置来动态来决定哪些测试方法需要被执行！

于是求助google, 发现了有这么个东西 [junit-ext](https://code.google.com/p/junit-ext/), 它很可能就是我们想想找的东西！

``` java
@RunWith(JunitExtRunner.class)
public class TestCasesOnDifferentOS {
    @Test
    @RunIf(value = OSChecker.class, arguments = OSChecker.MAC)
    public void shouldRunOnMac() throws Exception {
    }

    @Test
    @RunIf(value = OSChecker.class, arguments = OSChecker.WINDOWS)
    public void shouldRunOnWindows() throws Exception {
	}
}
```

我们只需要在@RunIf里面使用我们自己的Checker就可以很方便地控制测试方法的执行与否，于是脑海中出现了这样片段:

``` java
@RunWith(JunitExtRunner.class)
public class TestCasesOnDifferentDesign {
    @Test
    @RunIf(value = ToggleChecker.class, arguments = {"NEW_DESIGN_FOR_AWESOME_FEATURE", "false"})
    public void shouldDoSomethingForAwesomeFeature() throws Exception {
    }

    @Test
    @RunIf(value = ToggleChecker.class, arguments = {"NEW_DESIGN_FOR_AWESOME_FEATURE", "true"})
    public void shouldDoSomethingForNewDesignForAwesomeFeature() throws Exception {
	}
}
```
感觉不错，似乎已经成功一大半了，但是。。。

当我尝试给现有的测试类加上`@RunWith(JunitExtRunner.class)`时，才注意到他上面已经指定了一个Runner了(`@RunWith(Theories.class)`), 再问google后得到一个令人绝望的消息，一个测试类不能指定多个Runner!!!

## assumeThat

看来只能另辟蹊径了, 接着找可以控制测试方法执行的办法。

[assumeThat](http://junit.sourceforge.net/javadoc/org/junit/Assume.html#assumeThat\\(T, org.hamcrest.Matcher\\))似乎可以，它会在条件不满足的时候中止当前的测试方法并且ignore之, 而且这是junit自带的，不需要使用额外的runner。

应该通过类似下面的代码就可以如愿了:

``` java
public class TestCasesOnDifferentDesign {
    @Test
    public void shouldDoSomethingForAwesomeFeature() throws Exception {
    	assumeThat(NEW_DESIGN_FOR_AWESOME_FEATURE.isActive(), is(false));
    	
    	//...
    }

    @Test
    public void shouldDoSomethingForNewDesignForAwesomeFeature() throws Exception {
    	assumeThat(NEW_DESIGN_FOR_AWESOME_FEATURE.isActive(), is(true));
    	
    	///...
	}
}
```

## FeatureManagerProvider
现在形式一片大好，接下来要做的就是想办法读取到toggle的状态。当我们执行 `NEW_DESIGN_FOR_AWESOME_FEATURE.isActive()`的时候，实际上是去一个FeatureManager那里查询指定的toggle的状态，togglz在web应用里有对应的特定实现，即有现成的FeatureManager可以使用。而我们的功能测试是独立于web应用的另外一个module, 因此我们需要提供自己的FeatureManager才可以: 第一步，在mvn中通过copy-resource将定义在webapp module中的toggle配置信息 togglz.properties文件复制到功能测试模块中；第二步，自定义一个FeatureManager来读取toggle信息。

### TogglzConfig
``` java
public class MyTogglzConfig implements TogglzConfig {
  @Override
  public Class<? extends Feature> getFeatureClass() {
    return MyFeature.class;
  }

  @Override
  public StateRepository getStateRepository() {
    URL toggleProperties = getClass().getClassLoader().getResource("togglz.properties");
    assert toggleProperties != null;
    try {
      return new FileBasedStateRepository(new File(toggleProperties.toURI()));
    } catch (URISyntaxException e) {
      e.printStackTrace();
      return null;
    }
  }

  @Override
  public UserProvider getUserProvider() {
    return new NoOpUserProvider();
  }
}
```
在我们自己实现的这个togglzConfig中我们主要做了两件事情，一是指定了从哪里读取toggle配置信息，还有就是指定了`Feature Enum`.

### FeatureManagerProvider
``` java
@BeforeClass
public static void beforeClass() {
  FeatureManager featureManager = new FeatureManagerBuilder().togglzConfig(new MyTogglzConfig()).build();
  ThreadLocalFeatureManagerProvider.bind(featureManager);
}
```
在上面的这段代码里，我们使用`FeatureManagerBuilder`构建了一个FeatureManager, 并且把它注册/绑定到`ThreadLocalFeatureManagerProvider`, 这两行代码我们是放在了最外层的TestSuite类的`BeforeClass`方法中，这样保证了在所有测试方法被执行之前toggle信息已经被初始化好了。

Done!