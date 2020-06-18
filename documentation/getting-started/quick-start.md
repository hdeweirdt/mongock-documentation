# Quick start

## Instantiation approaches: Annotation vs builder

As discussed, Mongock provides to approaches: Annotation approach and builder approach.

We recommend annotation approach as it's more Spring friendly an easy while you don't miss anything. However, there are some scenarios where you should use the traditional builder approach. For example if you need more control over the bean creation or when you are not using Spring at all.

In this section you will see a quick introduction on how to start with both approaches easily.

## Common steps

Both approaches share the first 3 steps related to your pom file.

1- **Add the last version of Mongock bom to your pom file**

```markup
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.github.cloudyrock.mongock</groupId>
            <artifactId>mongock-bom</artifactId>
            <version>LAST_RELASE_VERSION_4</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

2- **Add runner and driver dependencies**. In this example we use the Mongock runner for Spring 5.x.x and Mongock driver for Spring Data MongoDB 3.x.x. For other version, please refer to [Version compatibility](../version-conpatibility.md).

```markup

<dependency>
    <groupId>com.github.cloudyrock.mongock</groupId>
    <artifactId>mongock-spring-v5</artifactId>
 </dependency>

<dependency>
    <groupId>com.github.cloudyrock.mongock</groupId>
    <artifactId>mongodb-springdata-v3-driver</artifactId>
</dependency>
```

3-  **Add your MongoDB and Spring Data dependencies**. Mongock doesn't import any MongoDB or Spring Data library in order to avoid transitive dependency issues. So you need to provide them.

## Annotation approach

Once you have successfully imported the necessary Mongock dependencies, you only need to  specify your changeLog package and tell Spring your are using Mongock. For this you only need the 2 following steps:

1- **Add your changeLog package path to your property file**. Minimal configuration requires at least one changeLog package\(it's an array, so you can add more than one\), but anything you can configure manually with builder, you can do as well with properties. However note that Mongock provides default values. Worthy noticing the lock, which is now enabled by default, unlike older versions where you need to explicitly enable it for backward compatibility.

```yaml
spring:
  mongock:
    change-logs-scan-package:
      - com.github.cloudyrock.mongock.integrationtests.spring5.springdata3.changelogs.client.initializer
```

2. **Annotate your SpringBootApplication with** _**@EnableMongock**_

```java
@EnableMongock
@SpringBootApplication
public class App {
  public static void main(String[] args) {
    new SpringApplicationBuilder().sources(App.class).run(args);
  }
}
```

## **Traditional** builder approach

While the annotation approach is more convenient, the traditional builder approach is useful when you need more control over your bean creation, or inevitable if you are not using Spring.

In this case you need to build the Mongock Instance yourself and , in case of Spring, provide the Mongock Bean.

{% tabs %}
{% tab title="Spring ApplicationRunner" %}
```java
public MongockApplicationRunner mongockApplicationRunner(ApplicationContext springContext,
                                                         MongoTemplate mongoTemplate) {
    return MongockSpring5.builder()
        .setDriver(new SpringDataMongo3Driver(mongoTemplate))
        .addChangeLogsScanPackage("your_changeLog_package_path")
        .setSpringContext(springContext)
        .buildApplicationRunner();
  }
```
{% endtab %}

{% tab title="Spring InitializingBean" %}
```java
public MongockApplicationRunner mongockApplicationRunner(ApplicationContext springContext,
                                                         MongoTemplate mongoTemplate) {
    return MongockSpring5.builder()
        .setDriver(new SpringDataMongo3Driver(mongoTemplate))
        .addChangeLogsScanPackage("your_changeLog_package_path")
        .setSpringContext(springContext)
        .buildInitializingBeanRunner();
  }
```
{% endtab %}

{% tab title="Standalone" %}
```java
MongoClient mongoClient = MongoClients.create("MongoDB connection string");
MongockStandalone.builder()
        .setDriver(new MongoSync4Driver(mongoCient.getDatabase("your_db"))
        .addChangeLogsScanPackage("your_changeLog_package_path")
        .buildRunner()
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Note that for standalone we have used MongoSync4Driver as it's a more common scenario. Please take a look to our [Version compatibility](../version-conpatibility.md) section.
{% endhint %}

