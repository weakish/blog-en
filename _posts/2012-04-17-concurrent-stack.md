---
layout: default
title: Concurrent Stack Does Not Exist
---

![](http://www.yinwang.org/images/rock-stack_22.jpg)

Some weeks ago I read this thought-provoking article by Nir Shavit Data Structures in the Multicore Age. The topic is about efficiency of concurrent data structures. There have been many thinkings going on after the reading, but the most interesting point to me is that the author started trying to use a "concurrent stack", but ended up happily using a pool.

> "In the end, we gave up the stack's LIFO ordering in the name of performance."

Now there can be several interesting questions to ask. Can we give up some property of a data structure if it is crucial to the correctness of the program? If we can give up the LIFO ordering, then why did we think we need a stack? But the most interesting question is probably:

> Does a "concurrent stack" really exist?

I think the answer is No -- a "concurrent stack" hasn't really existed and will never exist. Here is why:

1. If the threads are allowed to push and pop concurrently, then the "stack" can't really maintain a LIFO order, because the order of the "ins" and "outs" is then indeterminate and contingent on the relative execution speeds of the threads.

1. If the execution of two threads strictly interleave. Each time a "pusher" pushes an element, a "popper" immediately pops it out, then this is a FIFO order, a queue.

1. If a pusher pushes all the elements before a popper starts to pop all of them out, then this is indeed a LIFO order, a stack. But notice that there is no concurrency in this case -- the executions of the threads must be completely sequential, one after another.

1. If two threads interleave randomly, or there are more than two threads accessing the "stack" at the same time, then nothing can be said about the access order.

From the above, we can see that there is a fundamental conflict between the two notions, "concurrency" and a "stack".

> If a "stack" can be accessed concurrently, then there is no way we can maintain a LIFO order. On the other hand, if we enforce a LIFO order, then the stack cannot be accessed concurrently.

If we have realized that the essence of a stack is a continuation, and a continuation (by itself) is sequential, then it is no surprise we arrive at this conclusion.

Since a LIFO order is essential to a stack, we can't call the data structure a "stack" if we can't maintain a LIFO order.

We can't call a data structure a "stack" just because it has the methods named "push" and "pop" -- we have to look at what the methods actually do.

Even if we continue to think that we are using a stack, the threads are in effect just distributing messages, with the operations "push = send" and "pop = receive". So in essence this data structure is a pool. This exactly justifies the author's relaxation to a pool, although no actual relaxation happens -- the data structure has been a pool from the beginning. It was just disguised as a stack and less efficient.

So we see that the concept of a "concurrent stack" does not really exist. We also see that some data structures we have in the sequential world may not have concurrent counterparts.

