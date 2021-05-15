---
layout: post
title:  "Cycle in Linked list"
date:   2021-05-14 00:00:00 -0600
categories: data-structures
tags: data-structures
---
I'll show three different ways in which we can detect if theres a loop in a Linked List.

First consider this the class we will be using for the linked list:

{% highlight java %}
class Node<T> {
    public Node next;
    public T data;

    public Node(T data, Node<T> next) {
        this.next = next;
        this.data = data;
    }
}
{% endhighlight %}

- Using a set

A set is an abstract data type that stores unique values, in this approach we loop the linked list while storing the values on the set, the add operation for the HashSet returns false when we try to add an element that is already on the set.

{% highlight java %}
    public boolean hasCycleUsingHashSet(Node<Integer> head) {
        Set<Node> set = new HashSet<Node>();
        while(head != null) {
            if(set.add(head)){
                head = head.next;
            } else {
                return true;
            }
        }
        return false;
    }
{% endhighlight %}

This should be in O(n) time complexity since the add operation on the set is suppose to be O(1).
The space complexity is also O(n) since we need the extra space.

- Marking visited nodes

The idea is that each time we visit a node we mark it somehow as visited. Then as we are iterating the list if we find a node that is marked as visited we know theres a loop. In this approach we set the data for the visited nodes as null.

{% highlight java %}
    public boolean hasCycleMarkingVisited(Node<Integer> head) {
        while(head != null) {
            if(head.data == null){
                return true;
            }
            head.data = null;
            head = head.next;
        }
        return false;
    }
{% endhighlight %}

This runs nicely in O(n) but if one of the constraints is that we can not modify the original linked list then this is no good.

- Floyd's tortoise and hare algorithm.

A great algorithm that runs on O(n) and doesnt need extra space.
The idea behind this is that we have a pointer that goes one by one (the tortoise) and another pointer that will move twice as fast (the hare) and if theres a loop on the list they will meet in a node eventually.

{% highlight java %}
    public boolean floydTortoiseAndHare(Node<Integer> head) {
        if(head == null) {
            return false;
        }
        Node<Integer> tortoise = head;
        Node<Integer> hare = tortoise.next;

        while(tortoise != hare) {
            if(hare == null || hare.next == null) {
                return false;
            }
            tortoise = tortoise.next;
            hare = hare.next.next;
        }

        return true;
    }
{% endhighlight %}
