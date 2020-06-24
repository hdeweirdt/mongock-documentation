# Using custom beans in changeSet methods

Mongock allows you to use  custom beans in your changeSet methods, so you are not restricted to use basic MongoDB\(or specific framework\) components, like MongoDatabase, MongoCollection, MongoTemplate, etc.\(actually you would be using their Mongock decorators, not the direct components\)

{% hint style="info" %}
**Custom beans must be interfaces**. The reason behind is explained in the [lock section](lock-1.md#how-is-the-lock-ensured-in-every-database-access)
{% endhint %}

```java
@ChangeLog(order = "1")
public class ClientInitializer {

    @ChangeSet(id = "data-initializer-with-repository", order = "001", author = "mongock")
    public void dataInitializer(ClientRepository clientRepository) {
        IntStream.range(0, 10)
                .mapToObj(i -> new Client("name-" + i, "email-" + i, "phone" + i, "country" + i))
                .collect(Collectors.toList())
                .stream()
                .forEach(clientRepository::save);
    }
}
```

## Proxy explanation

As explained in [lock section](lock-1.md#how-is-the-lock-ensured-in-every-database-access), custom beans are proxied to ensure  the database is accessed in a  synchronised manner using the lock. 

The Mongock's proxy instrumentation have two main goals:

1. Intercept the actual method you are calling to ensure the lock is acquired.
2. Return a proxied object  to ensure the lock is acquired in subsequent calls

{% hint style="info" %}
By default, Mongock won't return a proxied object if one of the following conditions is in place:  The returned object is not an interface or it's a primitive type, String, Class type, wrapper type or any object in a package prefixed by"java.", "com.sun.", "javax.", "jdk.internal." or "sun."
{% endhint %}

## Advance configuration: Prevent proxing my beans

Although it is conservative, the default Mongock's proxy behaviour it's the recommend option and most cases will be fine with it. However, sometimes you need a different behaviour. Luckily in Mongock almost everything is configurable and something as sensitive as proxies won't be an exception.

You can prevent proxing custom beans by using **@NonLockGuarded** annotation. You can apply it to your bean's type, a specific method or a changeSet parameter. 

### @NonLockGuarded in changeSet parameter

This will tell Mongock you don't want to proxy that bean in that specific changeSet method. 

This is useful when, in general you are fine proxing the bean, but there are some exceptions or when you don't want to use the annotation in your type, so you annotate all your custom bean parameters in all your changeSet methods.

```java
@ChangeLog(order = "1")
public class ClientInitializerChangeLog {

    @ChangeSet(id = "data-initializer-with-repository", order = "001", author = "mongock")
    public void dataInitializer(@NonLockGuarded ClientRepository clientRepository) {
        //...
    }
}
```

### @NonLockGuarded in custom bean's type

If you annotate your custom bean's type, Mongock won't never proxy any bean of that type. 

It's obviously useful when you know that type won't be accessing to database at all or you don't want to proxy any parameter of that type, for any other reason.

```java
@NonLockGuarded
public class MyCustomBeanImpl implements MyCustomBean {
    //...
}
```

### @NonLockGuarded in custom bean's method

When annotating a method, there are 3 options:

* You don't want to ensure the lock in the method's call but you do want to **return a proxy**
* You want to **ensure the lock in the method's call** but you don't want to return a proxy
* You neither want to ensure the lock in the method's call nor return a proxy

#### **Not ensuring the lock in the method's call but returning a proxy**

For this purpose you need to annotate your method providing the value **METHOD**. However, as METHOD is the default value, you don't need to provide any.

```java
public class MyCustomBeanImpl implements MyCustomBean {
    
    @NonLockGuarded
    public MyCustomBean accessToDatabaseAndReturnProxiableObject() {
        //...
        return new MyCustomBeanImpl();
    }
}
```

#### En**suring the lock in the method's call but not returning a proxy**

To achieve this you need to annotate your method with **RETURN** value.

```java
public class MyCustomBeanImpl implements MyCustomBean {
    
    @NonLockGuarded(NonLockGuardedType.RETURN)
    public MyCustomBean accessToDatabaseAndReturnProxiableObject() {
        //...
        return new MyCustomBeanImpl();
    }
```

#### Neither ensuring the lock in the method's call nor returning a proxy

For this purpose you need to annotate your method providing the value **NONE**

```java
public class MyCustomBeanImpl implements MyCustomBean {
    
    @NonLockGuarded(NonLockGuardedType.NONE)
    public MyCustomBean accessToDatabaseAndReturnProxiableObject() {
        //...
        return new MyCustomBeanImpl();
    }
}
```

