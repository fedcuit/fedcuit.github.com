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
  //Mockito will try to inject this dependency to target object in this order: Constructor, Property, Field.

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

* Verify method called with expected arguments
````java
ArgumentCaptor<String> argument = ArgumentCaptor.forClass(String.class);
verify(errors, atLeastOnce()).add(argument.capture(), any(ActionMessage.class));
//Verify errors.add(_, _) called with expected arguements, mockito will store captured arguemnt into the ArgumentCaptor object.

List<String> values = argument.getAllValues();
//If method called only once, use getValue() to get the value passed in, if caleed multi times, use getAllVales() to get value list.

assertTrue(values.contains("exception.message"));
assertTrue(values.contains("exception.detail"));
```

