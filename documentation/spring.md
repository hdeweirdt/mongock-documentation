# Driver

We have already mention the concept driver in [Main concepts](main-concepts.md#driver). Drivers are the part of Mongock in charge of dealing with MongoDB.

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
      <th style="text-align:left"><b>Mongock driver</b>
      </th>
      <th style="text-align:left"><b>Java library</b>
      </th>
      <th style="text-align:left"><b>Mongock runners compatible </b>
      </th>
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
          <li><b>mongock-standalone:4.x</b>
          </li>
          <li><b>mongock-spring-v5: 4.x</b>
          </li>
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
          <li><b>mongock-standalone: 4.x</b>
          </li>
          <li><b>mongock-spring-v5: 4.x</b>
          </li>
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
          <li><b>mongock-standalone: 4.x</b>
          </li>
          <li><b>mongock-spring-v5: 4.x</b>
          </li>
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
          <li><b>mongock-standalone: 4.x</b>
          </li>
          <li><b>mongock-spring-v5: 4.x</b>
          </li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

## Building time: Driver

When use it the annotation approach, you just need to import the required Mongock driver dependency, annotate your SpringBootApplication with **@EnableMongock** and everything is done for you.  All the configuration should be provided via properties file.

However, if you opt for the manual builder approach, you need to create the driver yourself and give it to the Mongock builder.

#### Configuration

Driver configuration is very simple, but there are still a couple of properties you can configure related to the collections and lock parameters

<table>
  <thead>
    <tr>
      <th style="text-align:left">Configuration parameter</th>
      <th style="text-align:left">Default value</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">
        <p>&lt;b&gt;&lt;/b&gt;</p>
        <p><b>changeLogCollectionName</b>
        </p>
      </td>
      <td style="text-align:left">mongockChangeLock</td>
      <td style="text-align:left">
        <p></p>
        <p>It&apos;s the name of the collection where the changeLog history is stored</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>lockCollectionName</b>
      </td>
      <td style="text-align:left">mongockLock</td>
      <td style="text-align:left">
        <p></p>
        <p>The name of the collection where the pessimistic lock is stored</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>LockAcquiredForMinutes</b>
      </td>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p></p>
        <p>Number of minutes mongock will acquire the lock for. It will refresh the
          lock when is close to be expired anyway</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>maxWaitingForLockMinutes</b>
      </td>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p></p>
        <p>Max minutes mongock will wait for the lock in every try</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>maxTries</b>
      </td>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p></p>
        <p>Max tries when the lock is held by another mongock instance</p>
      </td>
    </tr>
  </tbody>
</table>

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

{% tab title="v3-driver" %}
```java
MongoCore3Driver driver = MongoCore3Driver.withDefaultLock(mongoDatabase)
//or .withLockSetting(mongoDatabase, lockAcquiredForMinutes, maxWaitingForLockMinutes, maxTries);
driver.setChangeLogCollectionName("newChangeLogCollectionName");
driver.setLockCollectionName("newLockCollectionName");
```
{% endtab %}

{% tab title="sync-v4-driver" %}
```java
MongoSync4Driver driver = MongoSync4Driver.withDefaultLock(mongoDatabase)
//or .withLockSetting(mongoDatabase, lockAcquiredForMinutes, maxWaitingForLockMinutes, maxTries);
driver.setChangeLogCollectionName("newChangeLogCollectionName");
driver.setLockCollectionName("newLockCollectionName");

```
{% endtab %}

{% tab title="springdata-v2-driver" %}
```java
SpringDataMongo2Driver driver = SpringDataMongo2Driver.withDefaultLock(mongoTemplate)
//or .withLockSetting(mongoTemplate, lockAcquiredForMinutes, maxWaitingForLockMinutes, maxTries);
driver.setChangeLogCollectionName("newChangeLogCollectionName");
driver.setLockCollectionName("newLockCollectionName");
```
{% endtab %}

{% tab title="springdata-v3-driver" %}
```java
SpringDataMongo3Driver driver = SpringDataMongo3Driver.withDefaultLock(mongoTemplate)
//or .withLockSetting(mongoTemplate, lockAcquiredForMinutes, maxWaitingForLockMinutes, maxTries);
driver.setChangeLogCollectionName("newChangeLogCollectionName");
driver.setLockCollectionName("newLockCollectionName");
```
{% endtab %}
{% endtabs %}

