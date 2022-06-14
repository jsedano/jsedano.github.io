---
layout: post
title:  "Currying in Java"
date:   2022-06-13 00:00:00 -0600
categories: java
tags: java
---
Let's see some examples of how to achieve currying in Java.  

You can find the complete code for this here: [curry_example.jsh][curry_example.jsh].

Currying is taking a function with `n` parameters and returning a function with one parameter that returns a function with one parameter until you reach the `n` parameters of the original function and then you get the result.

The following method gets the curried version of a method with the signature:

{% highlight java %}
int methodName(int a, int b)
{% endhighlight %}

And returns the curried version:

{% highlight java %}
public static Function<Integer, Function<Integer, Integer>> toCurry(
    BiFunction<Integer, Integer, Integer> function) {
  return v -> v1 -> function.apply(v, v1);
}
{% endhighlight %}

Then we can use it to get curried versions of such methods:

{% highlight java %}
var curriedAdd = toCurry(Math::addExact);
var curriedMultiply = toCurry(Math::multiplyExact);
{% endhighlight %}

And use them like this:

{% highlight java %}
System.out.format("curriedAdd(2)(10): %d\n",curriedAdd.apply(2).apply(10));
System.out.format("curriedMultiply(2)(10): %d\n",curriedMultiply.apply(2).apply(10));
{% endhighlight %}

In the following example we use the curried versions to first add 1 and then multiply by 2 all the elements of a stream

{% highlight java %}
var add1 = toCurry(Math::addExact).apply(1);
var multiplyBy2 = toCurry(Math::multiplyExact).apply(2);
List.of(0, 1, 2, 3, 4).stream().map(add1).map(multiplyBy2).forEach(System.out::println);
{% endhighlight %}

We can use currying as a form of `partial application`, imagine we have the following method:

{% highlight java %}
public static String wget(
    int connectionTimeout,
    int readTimeout,
    boolean followRedirects,
    String requestMethod,
    String address) {
  try {
    URL url = new URL(address);
    HttpURLConnection con = (HttpURLConnection) url.openConnection();
    con.setRequestMethod(requestMethod);
    con.setConnectTimeout(connectionTimeout);
    con.setReadTimeout(readTimeout);
    con.setInstanceFollowRedirects(followRedirects);
    return address + " " + con.getResponseCode();
  } catch (Exception e) {
    StringWriter sw = new StringWriter();
    PrintWriter pw = new PrintWriter(sw);
    e.printStackTrace(pw);
    String stackTrace = sw.toString();
    return address + " " + stackTrace.substring(0, stackTrace.indexOf("\n"));
  }
}
{% endhighlight %}

This method returns the `http status` or part of the exception if any was thrown.

If we use it like that we would have to be setting all the parameters everytime we want to use the function, but if we have a curried version:

{% highlight java %}
public static Function<Integer, Function<Boolean, Function<String, Function<String, String>>>>
    cwget(int v) {
  return v1 -> v2 -> v3 -> v4 -> wget(v, v1, v2, v3, v4);
}
{% endhighlight %}

We could then set the parameters we have:

{% highlight java %}
var get = cwget(100).apply(100).apply(false).apply("GET");
{% endhighlight %}

And then just fill the renaming parameter when using the function:

{% highlight java %}
List.of(
        "https://www.google.com",
        "https://www.wikipedia.org",
        "asdf",
        "https://docs.oracle.com/javase/10/docs/api/java/net/package-summary.html",
        "https://jsedano.dev",
        "https://raw.githubusercontent.com/center-key/clabe-validator/main/clabe.ts")
    .parallelStream()
    .map(get)
    .forEach(System.out::println);
{% endhighlight %}

Download the complete code from this post here: [curry_example.jsh][curry_example.jsh].



[curry_example.jsh]: https://github.com/jsedano/examples/blob/main/java-snippets/curry_example.jsh
