---
layout: post
title:  "Remove duplicates in ordered array Java"
date:   2021-07-05 00:00:00 -0600
categories: java
tags: java
---
Heres how to remove duplicates from an ordered primitive int array on Java.  

You can find the complete code for this here: [remove_dupes.jsh][remove_dupes.jsh].

Consider the following problem, you have to remove the duplicates from an ordered integer array `in place`.
Since you cant actually change the length of a primitive array in java without declaring a new one, you will return the new length the array would have without the duplicates.

In order to solve this in O(n) time and O(1) space we are going to use a `two pointer technique`.
We will have `j` that will increase by one in every iteration and `i` which will increase only if its different from the value which j points to, ignoring every duplicate found.

This doesnt account for a null, or less than a lenght 2 array (in which case we could start j at 0).

The pre-increment could be less readable but just remember that `++variable` means we are increasing first and then using the variable.
 
{% highlight java %}
int removeDupes(int arr[]) {
    int i = 0;
    for(int j = 1; j < arr.length; j++) {
        if(arr[i] != arr[j]) {
            arr[++i] = arr[j];
        }
    }
    return ++i;
}
{% endhighlight %}



Download the complete code from this post here: [remove_dupes.jsh][remove_dupes.jsh].



[remove_dupes.jsh]: https://github.com/jsedano/examples/blob/main/java-snippets/remove_dupes.jsh
