# Driver

We have already mention the concept driver in [Main concepts](getting-started/main-concepts.md#driver). Drivers are the part of Mongock in charge of dealing with MongoDB.

## Motivation

In order to provide support to different MongoDB versions, Java MongoDB drivers and libraries, we need to separate this connector from the rest of Mongock, so the user can choose the right alternative for him\(for example, using directly the Java MongoDB driver or Spring Data library\)

It's also important when comes to old version. Users may don't want to upgrade to the last version of MongoDB or driver, but they still need Mongock to be supported for their production deployments.

## How it works

Technically drivers are simple implementations of the same interface which provides the contract between the driver itself and the [runner](standalone.md).

The idea is to provide a specific driver for every Java MongoDB driver, library or need. Open source users will be able to add drivers, following the driver specification.

Mongock drivers use two MongoDB collections. The **changeLogCollection**\(called mongockChangeLog by default\), where the changeLog history will be stored, and the **lockCollection**\(called mongockLock by default\), used for pessimistic synchronisation between Mongock executions.

{% hint style="warning" %}
It's important that **all the Mongock executions** that need to be synchronised\(different services or instances using the same MongoDB database\) **use the same lockCollection**. This is the only way they can be synchronised
{% endhint %}

{% hint style="warning" %}
Also **ensure that the right changeLogCollection** is used in order **to prevent** Mongock from **re-running migrations undesirably**. If you need to migrate from another changeLogCollection or from another legacy migration framework, please refer to [Legacy migration](legacy-migration.md) for more information.
{% endhint %}

## Driver types and compatibility table

Currently Mongock provides the following drivers:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Mongock driver</th>
      <th style="text-align:left">Java library</th>
      <th style="text-align:left">Mongock Runners compatible</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>mongodb-v3-driver</b>
      </td>
      <td style="text-align:left">org.mongodb: mongo.java.driver: 3.11.0</td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li>mongock-standalone:4.x</li>
          <li>mongock-spring-v5</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>mongodb-sync-v4-driver</b>
      </td>
      <td style="text-align:left">org.mongodb: mongodb-driver-sync: 4.0.2</td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li>mongock-standalone: 4.x</li>
          <li>mongock-spring-v5: 4.x</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>mongodb-springdata-v2-driver</b>
      </td>
      <td style="text-align:left">org.springframework.data: spring-data-mongodb: 2.2.4.RELEASE</td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li>mongock-standalone: 4.x</li>
          <li>mongock-spring-v5: 4.x</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>mongodb-springdata-v3-driver</b>
      </td>
      <td style="text-align:left">org.springframework.data: spring-data-mongodb: 3.0.0.RELEASE</td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li>mongock-standalone: 4.x</li>
          <li>mongock-spring-v5: 4.x</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

## How to use it

When use it the annotation approach, you just need to import the required Mongock driver dependency, annotate your SpringBootApplication with **@EnableMongock** and everything is done for you.  All the configuration should be provided via properties file.

However, if you opt for the manual builder approach, you need to create the driver yourself and give it to the MongockBuilder.

#### Configuration

Driver configuration is very simple, but still there are a couple of properties you can configure related to the collections used and lock parameters

* **changeLogCollectionName**: It's the name of the collection where the changeLog history is stored
* **lockCollectionName**: The name of the collection where the pessimistic lock is stored
* **lockAcquiredForMinutes**: asd
* **maxWaitingForLockMinutes**: asda
* **maxTries**: asd

{% tabs %}
{% tab title="properties" %}
```yaml
spring:
  mongock:
    change-log-collection-name: newChangeLogCollectionName
    lock-collection-name: newLockCollectionName
    lock-acquired-for-minutes: 3
    max-waiting-for-lock-minutes: 4
    max-tries: 3
```
{% endtab %}

{% tab title="v3" %}
```java
MongoCore3Driver driver = new MongoCore3Driver(mongoDatabase);
driver.setChangeLogCollectionName("newChangeLogCollectionName");
driver.setLockCollectionName("newLockCollectionName");
driver.setLockSettings(3, 4, 3);
```
{% endtab %}

{% tab title="sync-v4" %}
```java
MongoCore3Driver driver = new MongoSync4Driver(mongoDatabase);
driver.setChangeLogCollectionName("newChangeLogCollectionName");
driver.setLockCollectionName("newLockCollectionName");
driver.setLockSettings(3, 4, 3);
```
{% endtab %}

{% tab title="spring-data-v2" %}
```java
MongoCore3Driver driver = new SpringDataMongo2Driver(mongoTemplate);
driver.setChangeLogCollectionName("newChangeLogCollectionName");
driver.setLockCollectionName("newLockCollectionName");
driver.setLockSettings(3, 4, 3);
```
{% endtab %}

{% tab title="spring-data-v3" %}
```java
MongoCore3Driver driver = new SpringDataMongo3Driver(mongoTemplate);
driver.setChangeLogCollectionName("newChangeLogCollectionName");
driver.setLockCollectionName("newLockCollectionName");
driver.setLockSettings(3, 4, 3);
```
{% endtab %}
{% endtabs %}

