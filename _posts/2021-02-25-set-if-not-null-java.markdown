---
layout: post
title:  "Set if not null Java"
date:   2021-02-22 00:00:00 -0600
categories: java
tags: java
comments: true
---
Simple ways to check if a value is null before setting it to some object in Java.

You can find the complete code for this here: [SetIfNotNull.java][SetIfNotNull.java]

Consider you have a class `Movie` with an attribute `title` along with the correspondent setter, and you want to set some new value for the `title` but only if such value is not null.

- A very traditional way:

{% highlight java %}
if (title != null) {
    movie.setTitle(title);
}
{% endhighlight %}

- Using ternary operator, in the case `title` is null you would be placing the same value on the Object which may look a little ugly.

{% highlight java %}
movie.setTitle(Objects.nonNull(title) ? title : movie.getTitle());
{% endhighlight %}

- Very elegant way, although you are creating a new Object with the `ofNullable` call, but I guess it's ok.

{% highlight java %}
Optional.ofNullable(title).ifPresent(movie::setTitle);
{% endhighlight %}

- Creating a little helper method which receives the value and a `Consumer` that sets the value in the object.

{% highlight java %}
public static <V> void setIfNotNull(V value, Consumer<V> setter) {
    if (Objects.nonNull(value)) {
        setter.accept(value);
    }
}
{% endhighlight %}

and you call it like this:

{% highlight java %}
setIfNotNull(title, movie::setTitle);
{% endhighlight %}

That last one only makes sense if you are using it a lot.


Download the complete code from this post here [SetIfNotNull.java][SetIfNotNull.java].



[SetIfNotNull.java]: https://github.com/jsedano/examples/blob/main/java-snippets/SetIfNotNull.java
