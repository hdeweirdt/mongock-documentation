# Spring specific features

## MongockTemplate

As mentioned in  [ChangeLogs](untitled.md) section, when using Spring, you must use **MongockTemplate,** instead of Spring MongoTemplate. **MongockTemplate** is just a decorator/wrapper providing exactly the same API than MongoTemplate, but ensuring your changes are correctly synchronised. 

You can find the technical reason behind in the [Lock](lock-1.md) section. 

```java
@ChangeSet(order = "005", id = "changeWithMongockTemplate", author = "mongock")
public void changeWithMongockTemplate(MongockTemplate mongockTemplate) {
  mongockTemplate.save(new MyEntity());
}
```

## Profiles

**Mongock** accepts Spring's `org.springframework.context.annotation.Profile` annotation. If a changeLog or changeSet class is annotated with `@Profile`, then it is activated for current application profiles.

{% hint style="success" %}
Mongock will automatically pick the active profiles from the Spring `ApplicationContext` . Then you only need to annotate your changeLogs and changeSets.
{% endhint %}

**Annotating ChangeLogs and ChangeSets with Profile**

_**Example 1**_: annotated changeSet will be invoked for a `dev` profile

```java
@Profile("dev")
@ChangeSet(author = "mongock", id = "changeSetForDevOnly", order = "01")
public void changeSetForDevOnly(MongoDatabase db){
  // ...
}
```

_**Example 2**_: all change sets in a changeLog will be invoked for a `test` profile

```java
@ChangeLog(order = "1")
@Profile("test")
public class changeLogForTestOnly{

  @ChangeSet(author = "mongock", id = "changeSetForTestOnly", order = "01")
  public void changeSetForTestOnly(MongoDatabase db){
    // ...
  } 
}
```

{% hint style="info" %}
Mongock will support in next versions the new Profile expression approach from Spring. Please check our [roadmap](../resources/product-roadmap.md)
{% endhint %}

## ApplicationRunner vs InitializingBean

When using Spring runner, you choose what type of bean you want to build; Spring [ApplicationRunner](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/ApplicationRunner.html) bean or a [InitializingBean](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/InitializingBean.html). 

With annotation approach:

{% tabs %}
{% tab title="ApplicationRunner" %}
```yaml
  mongock:
    change-logs-scan-package:
      - com.github.cloudyrock.mongock.integrationtests.spring5.springdata3.changelogs.client.initializer
    runner-type: applicationrunner
```
{% endtab %}

{% tab title="InitializingBean" %}
```yaml
spring:
  mongock:
    change-logs-scan-package:
      - com.github.cloudyrock.mongock.integrationtests.spring5.springdata3.changelogs.client.initializer
    runner-type: initializingbean
```
{% endtab %}
{% endtabs %}

