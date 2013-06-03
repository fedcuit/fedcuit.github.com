---
layout: post
title: FAQ of selenium
date: 2013-06-03 22:41
comments: true
categories: automation selenium
---

* How to simulate press ENTER key

```java
@And("^i press enter key$")
public void i_press_enter_key() throws Throwable {
  driver.findElement(By.id("gbqfq")).sendKeys(Keys.ENTER);
}
```

* How can wait until a element display or clickable.
```java
@Then("^i should see at least (\\d+) results related to my search keyword$")
public void i_should_see_at_least_results_related_to_my_search_keyword(int amount) throws Throwable {
  WebDriverWait wait = new WebDriverWait(driver, 10);
  wait.until(ExpectedConditions.elementToBeClickable(By.id("pnnext")));

  List<WebElement> results = driver.findElements(By.cssSelector("li.g"));

  assertThat(results.size()).isGreaterThan(amount);

  List<String> resultTexts = Lambda.extract(results, on(WebElement.class).getText());
  for (String text : resultTexts) {
    assertThat(text).containsIgnoringCase(keyword);
  }
}
```
