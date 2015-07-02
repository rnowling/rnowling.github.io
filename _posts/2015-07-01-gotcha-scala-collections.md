---
layout: post
title:  "Gotchas with Scala Mutable Collections and Large Data Sets"
date:   2015-07-01 12:13:19
categories: "software engineering"
tags: ["software engineering"]
---
I was recently working on Spark project where I had to build a very large list for each partition.  I ran into a few gotchas with common data structures.  Through analyzing my data structure choices, I was able to get my run time down from 45 minutes to 1.5 minutes and scale my application from ~4 GB of data to ~100 GB.

Vector
======
In my initial implementation, I used Scala's version of a mutable vector, [`ArrayBuffer`](http://www.scala-lang.org/api/2.11.5/scala/collection/mutable/ArrayBuffer.html).  Like Java's [`ArrayList`](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html) and [`Vector`](https://docs.oracle.com/javase/7/docs/api/java/util/Vector.html) and Python's [`list`](https://docs.python.org/2/library/stdtypes.html#typesseq), `ArrayBuffer` is backed by an array (contiguous block of memory).  Using an array enables `ArrayBuffer` to provide random access reads and writes in $$O(1)$$ operations but searching, insertions, and deletions require $$O(N)$$ operations.  `ArrayBuffer` also tends to be very cache friendly for iteration since adjacent elements are contiguous in memory.

If the array isn't full, appending a new element only takes $$O(1)$$ operations.  If the array is full, appending a new element is costly.  A new, larger array must be allocated and the contents of the old array must be copied to the new array.  To minimize the cost of allocating and copying the contents, `ArrayBuffer` doubles the size of the array with each new allocation and copy.  As a result, append costs are amortized and require only $$O(1)$$ operations on average. 

As I rediscovered, `ArrayBuffer` can be a poor choice when dealing with a large number (10k or more) of elements.  While expanding the array, vectors use $$O(3N)$$ memory.  Even worse, a vector can be cause significant heap fragmentation, leading to very expensive allocations that require garbage collecting and compacting the heap.

Consider a heap of 768 MB. An `ArrayBuffer` has an array spanning byte 0 to 128 MB.  When the `ArrayBuffer` expands, the next allocated array will be 256 MB in size and start at 128 MB + 1 byte, leaving 384 MB of free space at the end of the heap.  When the `ArrayBuffer` expands a second time, it will attempt to allocate a 512 MB array but there won't be a large enough contiguous block.  The garbage collector will have to deallocate the first array, resulting in 128 MB of free space before the array and 384 MB after the array.  The garbage collector will then have to move the 256 MB array to get a 512 MB contiguous block of heap space.   As a result, resizing the `ArrayBuffer` can put very heavy pressure on the garbage collector, resulting in very poor performance.

In my case, I was seeing very poor run times and wasn't able to read more than a few gigabytes of data into memory.

Linked List
===========
Since I didn't need random access, I was able to use a [`ListBuffer`](http://www.scala-lang.org/api/2.11.5/index.html#scala.collection.mutable.ListBuffer), which is backed by a [linked list](https://en.wikipedia.org/wiki/Linked_list).  Linked lists sacrifice constant-time random access and locality but don't require contiguous blocks of memory.   Using a `ListBuffer` improved my run times (but not considerably) and enabled me to read all ~100 GB of my data into memory.

I profiled my application with [YourKit](https://www.yourkit.com/) and realized that my application was spending significant amounts of time deleting/appending at the end and in calls to `size` in a loop.  I was used to Java's [LinkedList](http://docs.oracle.com/javase/7/docs/api/java/util/LinkedList.html) and Python's [deque](https://docs.python.org/2/library/collections.html#collections.deque) which provide several enhancements over Scala's `ListBuffer`.  Both implementations use doubly-linked lists and maintainer pointers both the first and last links, enabling constant time pushes, pops, and peeks on both ends whereas `ListBuffer` requires $$O(N)$$ operations for pushes, pops, and peeks at the end of the list.  Additionally, `LinkedList` and `deque` store a counter which is updated whenever elements are added or removed, enabling constant-time reporting of the list's length.  `ListBuffer` recomputes the length of the list every time `size()` is called, requiring $$O(N)$$ operations. As a result of their implementations, `LinkedList` and `deque` can be used as efficiently as lists, stacks, and queues whereas Scala's mutable collections library contains separate implementations for each.

In the end, I was able to restructure my calculations to work around `ListBuffer`'s inefficiencies.  I ended up using a [Stack](http://www.scala-lang.org/api/2.11.5/index.html#scala.collection.mutable.Stack) and built my list in reverse.  Once built, I called `reverse` to get the proper order.   I also moved the call to `size` outside of the loop.

Summary
=======
Profiling can reveal bone-headed mistakes and false assumptions about common data structure implementations that cause bad performance.  I found that array-backed lists tend to perform poorly when there isn't enough heap space.  I also found limitations in Scala's implementations of linked lists, which would be "gotchas" to programmers coming from Java, Python, and Clojure.

My next step would be to look into array-mapped tries like [Clojure's](http://hypirion.com/musings/understanding-persistent-vector-pt-1) and [Scala's](http://www.scala-lang.org/api/2.11.5/index.html#scala.collection.immutable.Vector) `vector` implementations.  By using tries with branching factors of 32, the trie nodes are optimized to fit into the CPU's cache, taking better advantage of locality than simple linked lists.
