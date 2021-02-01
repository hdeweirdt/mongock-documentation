---
description: (in construction)
---

# Events

{% hint style="info" %}
Feature in beta version and documentation in construction
{% endhint %}

#### **Since version 4.2.1.BETA**

Mongock uses events as a way to notify the main application in which state is the Mongock process and eventually the result of the execution.

There is a big different in the approach, depending on which type of runner is used. For spring applications, Mongock takes advantage of the ApplicationEventPublisher, which is provided to Mongock at building time. On the other hand, for standalone applications, Mongock requires a an explicit handler at building time.

## Type of Events

Mongock provides three types of events:

* **Start event**: This event takes place just before starting the migration, after the validation.
* **Success event**: Is raised after the migration runs, if successful. This means, that no exception occurred, they were handled or the failed changeLogs were marked with failFast to false.
* **Failure event**: In contrast, if any of the changeLogs fails and it's not handled, as explained in the previous point, this event will be raised.

Please notice that there is no scenario in which both events, success and failure, are raised together.

## Working with Spring runners

Spring Mongock uses the events framework provided by Spring. This means there are two requirements for events to work with Mongock: 

1. **Provide the Spring ApplicationEventPublisher to the builder\(**_Only needed in case of traditional builder approach. Ignore this if you are using the annotation approach_\)
2. **Register the ApplicationListeners for the events you wish to subscribe.** 

{% tabs %}
{% tab title="Started event" %}
```java
import com.github.cloudyrock.spring.util.events.SpringMigrationStartedEvent;
import org.springframework.context.ApplicationListener;
import org.springframework.stereotype.Component;

@Component
public class MongockStartedEventListener 
implements ApplicationListener<SpringMigrationStartedEvent> {

    @Override
    public void onApplicationEvent(SpringMigrationStartedEvent event) {
        //TODO implment your business logic
    }
}
```
{% endtab %}

{% tab title="Success event" %}
```java
import com.github.cloudyrock.spring.util.events.SpringMigrationSuccessEvent;
import org.springframework.context.ApplicationListener;
import org.springframework.stereotype.Component;

@Component
public class MongockSuccessEventListener
implements ApplicationListener<SpringMigrationSuccessEvent> {

    @Override
    public void onApplicationEvent(SpringMigrationSuccessEvent event) {
        //TODO implment your business logic
    }
}
```
{% endtab %}

{% tab title="Failure event" %}
```java
import com.github.cloudyrock.spring.util.events.SpringMigrationFailureEvent;
import org.springframework.context.ApplicationListener;
import org.springframework.stereotype.Component;

@Component
public class MongockFailEventListener 
implements ApplicationListener<SpringMigrationFailureEvent> {

    @Override
    public void onApplicationEvent(SpringMigrationFailureEvent event) {
        //TODO implment your business logic
    }
}
```
{% endtab %}
{% endtabs %}

## Working with Standalone runners

As explained, if you are using the standalone runner, you need to provide explicit handlers for each event at building time.

However, the handler are slightly different depending on which type of event you are subscribing to.

* **Start event**: It requires a Runnable, which is invoked just before the migration and after the Mongock's validation.
* **Success event**: To handle this event, you need to provide to the Mongock builder a Consumer for `StandaloneMigrationSuccessEvent`.
* **Failure event**: To handle this event, you need to provide to the Mongock builder a Consumer for `StandaloneMigrationFailureEvent`.

{% tabs %}
{% tab title="Started event" %}
```java
import com.github.cloudyrock.standalone;

MongockStandalone.builder
//...
.setMigrationStartedListener(()-> {/** TODO your business logic  **/ })
```
{% endtab %}

{% tab title="Success event" %}
```java
import com.github.cloudyrock.standalone;
import com.github.cloudyrock.standalone.event.StandaloneMigrationSuccessEvent;

MongockStandalone.builder
//...
.setMigrationSuccessListener(successEvent-> {/** TODO your business logic  **/ })
```
{% endtab %}

{% tab title="Failure event" %}
```java
import com.github.cloudyrock.standalone;
import com.github.cloudyrock.standalone.event.StandaloneMigrationFailureEvent;

MongockStandalone.builder
//...
.setMigrationFailureListener(failureEvent-> {/** TODO your business logic  **/ })
```
{% endtab %}
{% endtabs %}







