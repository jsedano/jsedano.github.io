---
layout: post
title:  "Dynamic array"
date:   2021-03-21 00:00:00 -0600
categories: data-structures
tags: data-structures
---
My notes on `Dynamic array` with an implementation in Java.

You can find the complete code for this and other data structures here: [data structures and algorithms][data structures and algorithms]

A `dynamic array` is a `data structure` that uses a normal array but allows to increase the size (and in some cases decrease it) to hold more elements.


Common Operations for an array list includes:

|Operation           |Complexity          |
|--------------------|--------------------|
|get(indexOfElement) |O(1)                |
|add(value)          |O(1)                |
|insert(index, value)|O(n)                |
|delete(value)       |O(n)                |


This is the add operation

{% highlight java %}
/**
* adds a value at the end of the array
* - puts a value at size and then increases size by 1
* - if size is equals to the inner array size we need to grow the inner
*   array, we achieve this by instanciating a new array doubling the size
*   of the previous array and we copy the elements to the array.
* O(1) amortised (because we dont enlarge at every insertion)
**/
public void add(T value) {
     if (size == innerArray.length) {
         T newArray[] =  (T[]) new Object[innerArray.length * 2];
         System.arraycopy(innerArray, 0, newArray, 0, innerArray.length);
         innerArray = newArray;
     }
     innerArray[size] = value;
     size++;
}
{% endhighlight %}

And the remove operation

{% highlight java %}
/**
* removes a value at any valid index and moves other values one position
* O(n)
**/
public void remove(int index) {
    if (index < 0 || index >= size) {
        throw new IndexOutOfBoundsException();
    }
    for(int i = index + 1; i <= size; i++) {
        innerArray[i - 1] = innerArray[i];
    }
    size--;
}
{% endhighlight %}

Other operations are very similar to a normal array.

Download the complete code for this and other data structures here: [data structures and algorithms][data structures and algorithms]

[data structures and algorithms]: https://github.com/jsedano/examples/tree/main/data-structures-and-algorithms
