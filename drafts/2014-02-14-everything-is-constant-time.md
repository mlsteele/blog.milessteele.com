---
title: Everything is Constant Time
author: Miles Steele
---

Any algorithmic analysis can yield $O(1)$ worst case runtime.

There is a two dimensional surface on which are $n$ points.

Your algorithm is tasked with finding all the distances between points.
It should return a list of distances, one for each pair of points.
Here's an example of a (pretty slow) implementation in python.

~~~python
def find_distances(list_of_points):
    results = []
    for point_a in list_of_points:
        for point_b in list_of_points:
            results.append(distance_between(point_a, point_b))
    return results
~~~

So, the runtime of this is $n^2$.
But let's examine where that notion came from.
The outer loop will execute $n$ times where is the length of the list.
So we'll go ahead and multiply whatever comes next inside the loop by $n$.
The inner loop is the same, multiply another $n$.
Then inside the inner loop there's the `distance_between` and `append`.
Let's say those take constant time $c$.

The resulting runtime for this algorithm is $O(c n^2)$. But we can eliminate $c$ because
it is "just a constant". Just a number which, if we assume even
the worst possible case, is bounded by some reasonable upper bound.
Nearly everything could go wrong, and $c$ could end up being seconds long, but
that wouldn't change the worst case runtime analysis of this algorithm.

That leads us to $O(n^2)$. But why stop there?
$n$ is just a constant too!
It is reasonable to say that this program is only going to be around for 10 years if we're lucky. And no way will it be pitted against a list of more than say 3,117,536,870,912 elements in that time. Call that the worst case, where n is some constant up there in the millions of millions. Now our algorithm is $O(1)$. That looks much better.

