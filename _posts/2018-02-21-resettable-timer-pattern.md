---
layout: post
title: "resettable timer pattern"
description: ""
category: "Algorithms"
---

Recently I undertook the task of refactoring batch processing at work. The jobs followed a similar pattern of records get staged, messages are sent and the stage status is updated for each message. This causes numerous DB connections to be opened up for every message and doing updates in separate threads seemed like a waste of time to me.

This made me think about batch updates and a pattern where a single thread does all the updates when the process finishes. The important point here is knowing when the process has finished. For me that was when no message has been sent for an arbitrary amount of time. Implementing the below interface did the trick for me:

Spring AOP configuration:

{% highlight java %}
public abstract class ResettableTimer implements Runnable {

    protected Object lock = new Object();
    private long timeout_ms;
    private long last;
    volatile boolean shutdown = false;

    public ResettableTimer(long timeout_ms) {
        this.timeout_ms = timeout_ms;
    }

    public void reset() {
        synchronized (lock) {
            last = System.currentTimeMillis();
            lock.notify();
        }
    }

    @Override
    public void run() {
        try {
            synchronized (lock) {
                while(true) {
                    lock.wait(timeout_ms);
                    long delta = System.currentTimeMillis() - last;
                    if (delta >= timeout_ms) {
                        timeout();
                        lock.notify();
                    }
                }
            }
        } catch (InterruptedException e) {
        }
    }

    protected abstract void timeout();
}
{% endhighlight %}
