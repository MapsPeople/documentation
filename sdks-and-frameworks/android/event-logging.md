# Event Logging

The MapsIndoors SDK records anonymous usage statistics and diagnostic events per default and sends the logged data to a server at MapsPeople.

Logging may be disabled entirely by calling:

{% tabs %}
{% tab title="Java" %}

```java
MapsIndoors.disableEventLogging(true);
```

{% endtab %}

{% tab title="Kotlin" %}

```kotlin
MapsIndoors.disableEventLogging(true)
```

{% endtab %}
{% endtabs %}
