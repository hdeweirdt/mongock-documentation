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

* Started event: This event takes place just before starting the migration, after the validation.
* Success event: Is raised after the migration runs, if successful. This means, that no exception occurred, they were handled or the failed changeLogs were marked with failFast to false.
* Failure event: In contrast, if any of the changeLogs fails and it's not handled, as explained in the previous point, this event will be raised.

Please notice that there is no scenario in which both events, success and failure, are raised together.

### Working with Spring

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

### DbMigrationSuccessEvent

This events is thrown when the migration has finished successfully.

## How to use it

### With spring runner

With Spring Mongock uses the buildup events framework provides by Spring. So there are two requirements for events to work with Mongock: Provide the Spring ApplicationEventPublisher and register the listener for the events you want to manage.

### With standalone

In this case you don't have a framework around you to provide the events ecosystem, so you need to provide two handles for both events, success and fail migration events...



 

\*\*\*\*

