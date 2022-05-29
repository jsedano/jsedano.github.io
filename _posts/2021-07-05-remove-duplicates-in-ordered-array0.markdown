---
layout: post
title:  "Remove duplicates in unordered array with Java using streams"
date:   2022-05-28 00:00:00 -0600
categories: java
tags: java
---
Heres how to remove duplicates from an unordered primitive int array on Java using streams.  

You can find the complete code for this here: [remove_dupes_unordered_the_smart_way.jsh][remove_dupes_unordered_the_smart_way.jsh].

Consider the following straight forward problem, you receive a primitive int array and you need to return a new array only containing the unique elements.

Because we are using `streams` since 2014 and we know that a `Set` does not accept duplicates, we only need to collect the array into a set and back into an array.  

{% highlight java %}
int[] removeDupes(int arr[]) {
    return Arrays.stream(arr)
        .boxed()
        .collect(Collectors.toSet()).stream()
        .mapToInt(Integer::intValue).toArray();
}
{% endhighlight %}

The `boxed` method just returns an Integer stream instead of a primitive one.



Download the complete code from this post here: [remove_dupes_unordered_the_smart_way.jsh][remove_dupes_unordered_the_smart_way.jsh].



[remove_dupes_unordered_the_smart_way.jsh]: https://github.com/jsedano/examples/blob/main/java-snippets/remove_dupes_unordered_the_smart_way.jsh
