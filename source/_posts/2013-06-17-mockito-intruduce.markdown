---
layout: post
title: Mockito Advanced Usage
date: 2013-06-17 22:32
comments: true
categories: java mock mockery mockito
---

* Inject dependency in Mockito

```java
public class InjectMocksTest {

  @Mock
  private EJBBeanInjectedByContainerInRealCode dependencyBean;
  //Create a mock object with type EJBBeanInjectedByContainerInRealCode and inject it to a spy object.

  @InjectMocks
  @Spy
  private MyEJBBean ejbBean;
  //Create a spy object of MyEJBBean, all fields annotated with @Mock with injected into this spy object.

  @Before
  public void setUp() {
    initMocks(this);
    //This is required to create all the mock objects and spy objects declared in annotation way.
  }

  @Test
  public void testShouldXXX() throws Exception {

    when(dependencyBean.findSomeDataFromDB(anyString(), anyString(), anyString())).thenReturn(anyList());
    //Mock the behavior the the injected dependency.

    Object[] objects = ejbBean.myMethod();
    //Call the real method on the spy object.

    assertThat(objects).isNotEmpty();
  }
}
```

