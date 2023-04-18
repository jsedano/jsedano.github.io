---
layout: post
title:  "Plain Thymeleaf"
date:   2023-04-17 00:00:00 -0600
categories: java thymeleaf
tags: java thymeleaf
comments: true
---

`Thymeleaf` is a HTML ([and more][and more]) template engine for Java. This is the core in action with nothing else.

You can find the complete code for this here: [only-thymeleaf][only-thymeleaf].

All of this is just putting into a project what has being posted in this [stackoverflow answer][stackoverflow answer] which in turn was found on this [comment on github][comment on github].

We begin by getting only the core jar for Thymeleaf (the latest one at the time this post was written).

[pom.xml][pom.xml]
{% highlight xml %}
<dependency>
  <groupId>org.thymeleaf</groupId>
  <artifactId>thymeleaf</artifactId>
  <version>3.1.1.RELEASE</version>
</dependency>
{% endhighlight xml %}

Then we create our `template` inside resources on a directory apptly named `templates`.

[src/main/resources/templates/index.html][src/main/resources/templates/index.html]
{% highlight html %}
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8">
  <title>Thymeleaf hello world</title>
</head>
<body>

<p>First name: <span th:text="${first_name}?: '(no first name specified)'"/>.</p>
<p>Last name: <span th:text="${last_name}?: '(no last name specified)'"/>.</p>

</body>
</html>
{% endhighlight html %}

Inside the `span tag` we have `th` which is how all data attributes starts on Thymeleaf, you can see that it's declared as a namespace definition above on the html tag. We are also using a variable expression `${}` with an Elvis operator `?:` which is an special operator that only returns it's value when the evaluated expression returns null, which means if `first_name` is null then `(no first name specified)` will be returned.

Now to bring it all together:
[src/main/java/dev/jsedano/examples/onlythymeleaf/Main.java][src/main/java/dev/jsedano/examples/onlythymeleaf/Main.java]

{% highlight java %}
var resolver = new ClassLoaderTemplateResolver();
resolver.setTemplateMode(TemplateMode.HTML);
resolver.setCharacterEncoding("UTF-8");
resolver.setPrefix("/templates/");
resolver.setSuffix(".html");
{% endhighlight %}

Since we are not using Spring or nothing to helps us with dependency injection we will create objects the traditional way, setting up the prefix for where the templates will be stored and the suffix for them.

{% highlight java %}
var context = new Context();
if (args.length != 0) {
  context.setVariable("first_name", args[0]);
  if (args.length > 1) {
    context.setVariable("last_name", args[1]);
  }
}
{% endhighlight %}

This will be the context which will hold the objects that Thymeleaf will use to generate the html.

{% highlight java %}
var templateEngine = new TemplateEngine();
templateEngine.setTemplateResolver(resolver);

var result = templateEngine.process("index", context);
System.out.println(result);
{% endhighlight %}

Here we create a `TemplateEngine` object and pass the `resolver`, then we call `process` on the `templateEngine` object with the name of the template and the context we created above.

We can create our fat jar running `mvn verify` and then call it with `java -jar ./target/only-thymeleaf-1.0-jar-with-dependencies.jar` to print the result of the template processing, if we send variables when we executed we can see how they get replace:

{% highlight text %}
only-thymeleaf % java -jar ./target/only-thyme  leaf-1.0-jar-with-dependencies.jar
SLF4J: No SLF4J providers were found.
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#noProviders for further details.
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Thymeleaf hello world</title>
</head>
<body>

<p>First name: <span>(no first name specified)</span>.</p>
<p>Last name: <span>(no last name specified)</span>.</p>


</body>
</html>

only-thymeleaf % java -jar ./target/only-thymeleaf-1.0-jar-with-dependencies.jar juan sedano
SLF4J: No SLF4J providers were found.
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#noProviders for further details.
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Thymeleaf hello world</title>
</head>
<body>

<p>First name: <span>juan</span>.</p>
<p>Last name: <span>sedano</span>.</p>


</body>
</html>

{% endhighlight %}



For more information on Thymeleaf you can go directly to the [thymeleaf docs][thymeleaf docs].

Download the complete code for this here: [only-thymeleaf][only-thymeleaf].

[and more]:https://www.thymeleaf.org/doc/tutorials/3.1/usingthymeleaf.html#what-kind-of-templates-can-thymeleaf-process
[only-thymeleaf]:https://github.com/jsedano/examples/tree/main/only-thymeleaf
[pom.xml]:https://github.com/jsedano/examples/blob/main/only-thymeleaf/pom.xml
[src/main/resources/templates/index.html]:https://github.com/jsedano/examples/blob/main/only-thymeleaf/src/main/resources/templates/index.html
[src/main/java/dev/jsedano/examples/onlythymeleaf/Main.java]:https://github.com/jsedano/examples/blob/main/only-thymeleaf/src/main/java/dev/jsedano/examples/onlythymeleaf/Main.java
[comment on github]:https://github.com/thymeleaf/thymeleaf/issues/561#issuecomment-490648829
[stackoverflow answer]:https://stackoverflow.com/a/71995574/1845671
[thymeleaf docs]:https://www.thymeleaf.org/doc/tutorials/3.1/usingthymeleaf.html
