---
layout: post
title:  "Java Redis pub/sub example with Jedis"
date:   2022-09-19 00:00:00 -0600
categories: java, redis
tags: java, redis, jedis
---

This is a Redis pub/sub example written in Java using Jedis.

- You can find the complete code for this here: [Java Redis pub/sub example][Java Redis pub/sub example].
- For more neat stuff that Redis can do look here: [Redis 2022][Redis 2022].

### Prerequisites

- [Download the code][Java Redis pub/sub example]
- Have an instance of [redis-stack][redis-stack] running.
- Apache Maven 3.8.6
- Java 17

### Libraries used

We are only using [jedis][jedis] in order to keep the example simple.

### Redis Commands used

#### subscribe

this is used in order to subscribe to one or more channels:

{% highlight shell %}
subscribe channel1 channel2... channelN
{% endhighlight shell %}

#### unsubscribe

unsubscribe current client to one or more channels:

{% highlight shell %}
unsubscribe channel1 channel2... channelN
{% endhighlight shell %}

#### psubscribe

instead of subscribing to a channel, this allows you to subscribe to one of more patterns:

{% highlight shell %}
psubscribe pattern1 pattern2... patternN
{% endhighlight shell %}

for example if you subscribe to `foo*` it means that you will get the messages meant for all the channels that start with `foo`.

#### punsubscribe

unsubscribe from one or more patterns:

{% highlight shell %}
punsubscribe pattern1 pattern2... patternN
{% endhighlight shell %}

#### publish

publish sends a message to connected clients, returns the number of clients that got the message.

{% highlight shell %}
publish aChannel 'some message'
{% endhighlight shell %}

#### ping

sends a ping from a client to the server, optionally you can send a message and the ping will echo it.

{% highlight shell %}
ping
{% endhighlight shell %}

or

{% highlight shell %}
ping a message
{% endhighlight shell %}

### Code walkthrough

We need to extend `JedisPubSub` in order to give our client functionality.

{% highlight java %}

import redis.clients.jedis.JedisPubSub;

public class LogPubSub extends JedisPubSub {

{% endhighlight java %}


To keep this example simple we are only going to add very basic functionality, in order to know which client is the one that got the message we are adding a name field.

{% highlight java %}
  private String name;

  public LogPubSub(String name) {
    this.name = name;
  }

{% endhighlight java %}

For `subscribe`, `unsubscribe`, `psubscribe`, `punsubscribe` and `pong` (which is the one that a successful ping triggers) we only print the information:

{% highlight java %}

  public void onSubscribe(String channel, int subscribedChannels) {
    System.out.printf(
        "name: %s method: %s channel: %s subscribedChannels: %d\n",
        name, "onSubscribe", channel, subscribedChannels);
  }

  public void onUnsubscribe(String channel, int subscribedChannels) {
    System.out.printf(
        "name: %s method: %s channel: %s subscribedChannels: %d\n",
        name, "onUnsubscribe", channel, subscribedChannels);
  }

  public void onPUnsubscribe(String pattern, int subscribedChannels) {
    System.out.printf(
        "name: %s method: %s patten: %s subscribedChannels: %d\n",
        name, "onPUnsubscribe", pattern, subscribedChannels);
  }

  public void onPSubscribe(String pattern, int subscribedChannels) {
    System.out.printf(
        "name: %s method: %s patten: %s subscribedChannels: %d\n",
        name, "onPSubscribe", pattern, subscribedChannels);
  }

  public void onPong(String message) {
    System.out.printf("name: %s method: %s message: %s\n", name, "onPong", message);
  }
}
{% endhighlight java %}

When receiving a message in the client apart from printing the information if the message is a ping then we will do a `ping`.
If receiving the String "exit" we will unsubscribe from that channel or pattern (in the case of client subscribed using psubscribe).

{% highlight java %}

  public void onMessage(String channel, String message) {
    System.out.printf(
        "name: %s method: %s channel: %s message: %s\n", name, "onMessage", channel, message);
    switch (message) {
      case "ping":
        this.ping();
        break;
      case "exit":
        this.unsubscribe(channel);
        break;
      default:
        if (message.indexOf("ping") == 0 && message.indexOf(" ") == 4) {
          this.ping(message.substring(5));
        }
    }
  }

  public void onPMessage(String pattern, String channel, String message) {
    System.out.printf(
        "name: %s method: %s pattern: %s channel: %s message: %s\n",
        name, "onPMessage", pattern, channel, message);
    switch (message) {
      case "ping":
        this.ping();
        break;
      case "exit":
        this.punsubscribe(pattern);
        break;
      default:
        if (message.indexOf("ping") == 0 && message.indexOf(" ") == 4) {
          this.ping(message.substring(5));
        }
    }
  }

{% endhighlight java %}

Then we only need to make a `JedisPooled` connection and used it to subscribe to a channel or pattern:

{% highlight java %}
JedisPooled jedis = new JedisPooled("localhost", 6379);
ExecutorService executor = Executors.newFixedThreadPool(4);

executor.execute(() -> jedis.subscribe(new LogPubSub("onlyOne"), "dev.one"));
executor.execute(() -> jedis.subscribe(new LogPubSub("oneAndTwo"), "dev.one", "dev.two"));
executor.execute(() -> jedis.psubscribe(new LogPubSub("pattern"), "dev.*"));
{% endhighlight java %}

We need to run the subscribe and psubscribe on a thread since those are blocking operations.

In this example we are creating three clients, which we will identify as "onlyOne", "oneAndTwo" and "pattern", this doesn't mean anything to redis, but it will be easier for us to keep track of whats happening.  

- "onlyOne" is subscribed to channel "dev.one".
- "oneAndTwo" is subscribed to channels "dev.one" and "dev.two".
- "pattern" is subscribed to the pattern "dev.*".

With this we could send messages using publish on our [RedisInsight CLI][RedisInsight CLI] which should be running on http://localhost:8001/redis-stack/browser if we installed the [redis-stack][redis-stack].

But we will also send messages using jedis.

{% highlight java %}
String message = "";
try (BufferedReader br = new BufferedReader(new InputStreamReader(System.in))) {
  do {
    message = br.readLine();
    int firstSpace = message.indexOf(' ');
    if (firstSpace > 1) {
      jedis.publish(message.substring(0, firstSpace), message.substring(firstSpace + 1));
    }
  } while (!"close".equals(message));
  jedis.close();
  System.exit(0);
} catch (IOException e) {
  throw new RuntimeException(e);
}
{% endhighlight java %}

For building the code run `mvn package` and a far jar will be generated on target and then you can run it like so `java -jar RedisPubSub-1.0.jar`.

Example run:

{% highlight shell %}
... target % java -jar RedisPubSub-1.0.jar
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
name: pattern method: onPSubscribe patten: dev.* subscribedChannels: 1
name: oneAndTwo method: onSubscribe channel: dev.one subscribedChannels: 1
name: oneAndTwo method: onSubscribe channel: dev.two subscribedChannels: 2
dev.one hello
name: pattern method: onPMessage pattern: dev.* channel: dev.one message: hello
name: oneAndTwo method: onMessage channel: dev.one message: hello
dev.five hello to you
name: pattern method: onPMessage pattern: dev.* channel: dev.five message: hello to you
dev.two also to you
name: pattern method: onPMessage pattern: dev.* channel: dev.two message: also to you
name: oneAndTwo method: onMessage channel: dev.two message: also to you
dev.six exit
name: pattern method: onPMessage pattern: dev.* channel: dev.six message: exit
name: pattern method: onPUnsubscribe patten: dev.* subscribedChannels: 0
dev.two exit
name: oneAndTwo method: onMessage channel: dev.two message: exit
name: oneAndTwo method: onUnsubscribe channel: dev.two subscribedChannels: 1
dev.one ping say my name
name: oneAndTwo method: onMessage channel: dev.one message: ping say my name
name: oneAndTwo method: onPong message: say my name
dev.two are you alive?
dev.five no one is listening here
dev.one this one is alive
name: oneAndTwo method: onMessage channel: dev.one message: this one is alive
dev.one exit
name: oneAndTwo method: onMessage channel: dev.one message: exit
name: oneAndTwo method: onUnsubscribe channel: dev.one subscribedChannels: 0
dev.one not anymore
close
... target %
{% endhighlight shell %}

Download the complete code for this here: [Java Redis pub/sub example][Java Redis pub/sub example].

[Java Redis pub/sub example]:https://github.com/jsedano/examples/tree/main/redis-pub-sub
[Redis 2022]:{% post_url 2022-09-17-redis-2022 %}
[jedis]:https://github.com/redis/jedis
[redis-stack]:https://redis.io/docs/stack/get-started/install/docker/
[RedisInsight CLI]:https://redis.io/docs/stack/insight/
