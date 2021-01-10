---
layout: post
title:  "N-Queens backtraking"
date:   2021-01-10 00:00:00 -0600
categories: algorithms
tags: java algorithms
---
This is the `N-Queens` problem solution with a `backtraking` approach, look here for the [N-Queens brute force approach][N-Queens brute force].

If you are not familiar with the `N-Queens` problem [look here for an examplation][N-Queens brute force].

You can find the complete code for this post here: [NQueensBacktracking.java][NQueensBacktracking.java].

The main difference from this one and the brute force approach is that we will check if a current permutation is a viable solution every time we add a queen, and if it's not we will not continue pursuing that particular permutation:

{% highlight java %}
static void findSolutions(LinkedHashSetWrapper perm, int n){
    if(perm.size() == n) {
        System.out.println(perm);
        return;
    }
    for(int i = 0; i < n; i++){
        if(!perm.contains(i)) {
            perm.add(i);
            if(isSolution(perm.copyInnerArray())){
                findSolutions(perm, n);
            }
            perm.removeLastElement();
        }
    }
}
{% endhighlight %}


In the next image you can see a solution for N = 4 (clic to enlarge)


{:refdef: style="text-align: center;"}
[![backtracking solution](/assets/images/n-queens-backtracking/backtracking.png)](/assets/images/n-queens-backtracking/backtracking.png)
{: refdef}


Download the complete code from this post here [NQueensBacktracking.java][NQueensBacktracking.java].



[N-Queens brute force]: {% post_url 2020-12-30-n-queens-brute-force %}
[NQueensBacktracking.java]: https://github.com/jsedano/examples/blob/main/java-snippets/NQueensBacktracking.java
