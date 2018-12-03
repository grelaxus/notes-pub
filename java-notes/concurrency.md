### Reentrancy

"Reentrancy means that locks are acquired on a per-thread rather than per-invocation basis. Reentrancy is implemented by 
associating with each lock an acquisition count and an owning thread. When the count is zero, the lock is considered unheld. 
When a thread acquires a previously unheld lock, the JVM records the owner and sets the acquisition count to one. If that same 
thread acquires the lock again, the count is incremented, and when the owning thread exits the synchronized block, 
the count is decremented. When the count reaches zero, the lock is released." ([\[1\]](#ref01), p.26)    
Because Intrinsic locks are _reentrant_, if a thread tries to acquire a lock that _it_ already holds, the request succeed

With a reentrant lock / locking mechanism, the attempt to acquire the same lock will succeed, and will increment an internal counter belonging to the lock. The lock will only be released when the current holder of the lock has released it twice.

Here's a example in Java using primitive object locks / monitors ... which are reentrant ([\[2\]](#ref02)):

```java
Object lock = new Object();
...
synchronized (lock) {
    ...
    doSomething(lock, ...)
    ...
}

public void doSomething(Object lock, ...) {
    synchronized (lock) {
        ...
    }
}
```

In other words, for the same thread, acquiring same lock second (thrid or whatever) time won't fail. For other thread - it will.
The alternative to reentrant is non-reentrant locking, where it would be an error for a thread to attempt to acquire a lock 
that it already holds.  

The advantage of using reentrant locks is that you don't have to worry about the possibility of failing due to accidentally 
acquiring a lock that you already hold. The downside is that you can't assume that nothing you call will change the state of 
the variables that the lock is designed to protect. However, that's not usually a problem. Locks are generally used to protect 
against concurrent state changes made by _other_ threads.

#### ReentrantLock
[[1], p.278](#ref01) Intrinsic locking works fine in most situations, but has some functional limitations - it is not possible to interrupt a 
thread waiting to acquire a lock, or to attempt to acquire a lock without being willing to wait for it forever. 
Intrinsic locks also must be released in the same block of code in which they are acquired; this simplifies coding and 
interacts nicely with exception handling, but makes non-blockstructured locking disciplines impossible. None of these are 
reasons to abandon _synchronized_, but in some cases a more flexible locking mechanism offers better liveness or performance.  
Canonical form of using a Lock:  
```java
Lock lock = new ReentrantLock();
...
lock.lock();
try {
  // update object state
  // catch exceptions and restore invariants if necessary
} finally {
  lock.unlock();
}
```
Failing to use finally to release a Lock is a ticking time bomb. when it goes off, you will have a hard time tracking down 
its origin as there will be no record of where or when the Lock should have been released.  


# Refferences

<a id="ref01">[1] - Java Concurrency in Practice  
<a id="ref02">[2] - ['Reentrancy' in java](https://stackoverflow.com/questions/16504231/reentrancy-in-java)
