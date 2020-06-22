# Runner

In [Main concepts](main-concepts.md#driver) we already mentioned the concept of **runners**. Runners are the ones dealing with the process logic and framework.

## Motivation

While Mongock standalone could be run with any framework, it would be nice that the user has some kind of adaption to take advantage of the underlying frameworks, for any relevant framework in the market. For example, in Spring, been able to use the Spring context, profiles or property files, etc. And once having that, it would be ideal for the user to be able to use version of Mongock adapted to the specific framework's version he is using and once a new version come up and he decide to not upgrade, still having support for the legacy version.

This is the goal of Mongock runners. Currently Mongock only provides support for standalone\(which can be run in any framework\) and Spring 5, but the objective is to provide support for any relevant framework in the market.  


## How it works

Technically, as drivers, runners are simple implementations of the same interface which, providing a bridge between the drivers and the process executor.

The idea is to provide a specific runner for every relevant framework in the market. Open source users will be able to add runners, following the runner specification.

## Runners available

Currently Mongock provides two runners: Mongock standalone and Spring 5. 

### Mongock standalone runner

It does not used any framework support for its execution. Ideally when running standalone Java applications, you want to have all the control of the Mongock instance and when it's executed or when you are using a framework for which there is no specific Mongock runner, you can use Mongock standalone while we develop it.

### Mongock Spring v5 runner

Based on Spring 5 and Spring Boot 2, its objetive to provide the easiest way to run Mongock with it. It provide support for Spring profiles, context, properties, annotation approach based on Spring configuration, etc.

Unlike Mongock standalone, it provides two building approach: the traditional builder approach and annotation approach\(**@EnabbleMongock**\)

## Building time: Runner

When use it the annotation approach, you just need to import the required Mongock runner dependency, annotate your SpringBootApplication with **@EnableMongock** and everything is done for you.  All the configuration should be provided via properties file.

However, if you opt for the manual builder approach, you need to build the runner yourself by using Mongock builder

#### Configuration

* all the configuration parameters



{% tabs %}
{% tab title="properties" %}
```yaml
spring:
  mongock:
    define all properties: asdads
```
{% endtab %}

{% tab title="mongock-spring-v5" %}
```java
MongoCore3Driver driver = new MongoCore3Driver(mongoDatabase);
driver.setChangeLogCollectionName("newChangeLogCollectionName");
driver.setLockCollectionName("newLockCollectionName");
driver.setLockSettings(3, 4, 3);
```
{% endtab %}

{% tab title="standalone" %}
```

```
{% endtab %}
{% endtabs %}





## Runners types and compatibility table

<table>
  <thead>
    <tr>
      <th style="text-align:left"><b>Mongock runner</b>
      </th>
      <th style="text-align:left"><b>Framework</b>
      </th>
      <th style="text-align:left"><b>Mongock drivers compatible</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>mongock-standalone</b>
      </td>
      <td style="text-align:left">none</td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li><b>mongodb-v3-driver: 4.x</b>
          </li>
          <li><b>mongodb-sync-v4-driver: 4.x</b>
          </li>
          <li><b>mongodb-springdata-v2-driver: 4.x</b>
          </li>
          <li><b>mongodb-springdata-v3-driver: 4.x</b>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>mongock-spring-v5</b>
      </td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li>springframework: 5.2.6.RELEASE</li>
          <li>springboot: 2.2.6.RELEASE</li>
        </ul>
      </td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li><b>mongodb-v3-driver: 4.x</b>
          </li>
          <li><b>mongodb-sync-v4-driver: 4.x</b>
          </li>
          <li><b>mongodb-springdata-v2-driver: 4.x</b>
          </li>
          <li><b>mongodb-springdata-v3-driver: 4.x</b>
          </li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

##  

