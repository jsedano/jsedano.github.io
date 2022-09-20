---
layout: post
title:  "Java @Annotations"
date:   2022-06-18 00:00:00 -0600
categories: java
tags: java
comments: true
---
If you have been using Java for about a week (probably even less) chances are you have already seen some annotations.
Let's review some stuff about them.

As explained on [https://docs.oracle.com/javase/tutorial/java/annotations/] we can use annotations to:

- **Give information to the compiler**: so we can detect errors or even suppress warnings.
- **Compile-time and deployment-time processing**: we can generate code, xml files and so on.
- **Runtime processing**: some annotations can be reviewed at runtime (using reflection).

Annotations can be used anywhere you use a type.

One interesting fact is that annotations can potentially generate code, but they **can not modify it**.
If you use tools like [lombok] you know that they actually modify the code, but they usually achieve this by
accessing internal libraries.

Annotations are defined with the **@interface** keyword like so:

{% highlight java %}
public @interface Version {
  int mayor();
  int minor();
}
{% endhighlight %}

They can receive any primitive types, enums, other annotations and arrays of those previously mentioned. Let's define an enum:

{% highlight java %}
public enum Usage {
  UTILITY,
  DTO,
  DAO,
  SERVICE
}
{% endhighlight %}

Then let's create another annotation:

{% highlight java %}
public @interface Info {
  String[] authors();
  Usage usage();
  Version version();
}
{% endhighlight %}

Now we use it:

{% highlight java %}
@Info(
    authors = {"jsedano", "weirderror"},
    usage = Usage.UTILITY,
    version = @Version(mayor = 1, minor = 0))
public class VeryUtil {}
{% endhighlight %}

You can specify if the annotation needs to be retained by the virtual machine at run time
or not; or even if they are to be discarded by the compiler. This is achieved by using a `meta-annotation`,
meta-annotations are annotations that are applied to another annotations.

You can also define where the annotation can be used, for example in methods, in constructors and so on.

Let's look at how the **@Override** annotation is defined (@Override is a predefined annotation that is use to
mark methods that are using method overriding):

{% highlight java %}
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
{% endhighlight %}

As you can see the meta-annotation **@Target** is used to say that this particular annotation can only be used
above a method declaration and the **@Retention** annotation is saying that this annotation will not be recorded in
the class file and will not be available at runtime.

In order to actually define functionality for an annotation type we need to implement an [AbstractProcessor][AbstractProcessor].



[https://docs.oracle.com/javase/tutorial/java/annotations/]:[https://docs.oracle.com/javase/tutorial/java/annotations/]
[lombok]:[https://projectlombok.org]
[AbstractProcessor]: {% post_url 2022-06-26-a-custom-annotation-to-apply-currying-in-java %}
