---
layout: post
title:  "Binary search trees"
date:   2021-05-27 00:00:00 -0600
categories: data-structures
tags: data-structures
---
A `binary search tree` is a `binary tree` where the left subtree has smaller elements and right subtree has larger elements.

You can find the complete code for this and other data structures here: [data structures and algorithms][data structures and algorithms]

## What is a tree anyway

A `tree` is an undirected graph that satisfies any of the following definitions:

- Acyclic connected graph.
{:refdef: style="text-align: center;"}
<img src="/assets/images/binary-search-trees/acyclic_connected_graph.jpg" width="250">
{: refdef}
- Connected graph with N nodes and N-1 edges.
{:refdef: style="text-align: center;"}
<img src="/assets/images/binary-search-trees/n_nodes_n-1_edges.jpg" width="250">
{: refdef}
- 2 vertices 1 path (A graph in which any 2 vertices are connected by just one path).
{:refdef: style="text-align: center;"}
<img src="/assets/images/binary-search-trees/2_vertices_one_path.jpg" width="250">
{: refdef}

## Stuff we have to know

- **Rooted tree:** Theres a root node and we hold a reference to it, we can choose any node as the root node.

- **Root node:** It has no parent,  but sometimes is useful to say that the parent of the root node is itself.

- **Child:** Node that extends from another node, a **Parent** is the inverse of this.

- **Leaf node:** Node with no children.

- **Subtree:** Tree contained in another tree.

{:refdef: style="text-align: center;"}
<img src="/assets/images/binary-search-trees/rooted_tree.jpg" width="500">
{: refdef}

- **Binary tree:** Every node has at most 2 children.

- **Binary search tree:** Every node has at most 2 children.

Satisfies the BST invariant: left subtree has smaller elements and right subtree has larger elements.

## Complexity of common operations

|Operation           |Average             | Worst              |
|--------------------|--------------------|--------------------|
|insert(element)     |O(log(n))           |O(n)                |
|remove(element)     |O(log(n))           |O(n)                |
|search(element)     |O(log(n))           |O(n)                |

## Insertion
## Searching
## Removal
## Traversals
### Pre-order
### In-order
### Post-order
### Level order / Breadth first search

Download the complete code for this and other data structures here: [data structures and algorithms][data structures and algorithms].

[data structures and algorithms]: https://github.com/jsedano/examples/tree/main/data-structures-and-algorithms
