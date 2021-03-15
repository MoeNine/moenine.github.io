---
title: blocking_query
date: 2021-02-15 10:06:12
tags: java,Thread
---

# 阻塞队列

| 方法类型 | 抛出异常  | 特殊值   | 阻塞   | 超时               |
| -------- | --------- | -------- | ------ | ------------------ |
| 插入     | add(e)    | offer(e) | put(e) | offer(e,time,unit) |
| 移除     | remove()  | poll()   | take() | poll(time, unit)   |
| 检查     | element() | peek()   | 不可用 | 不可用             |



| 抛出异常 | 当阻塞队列满时,再往队列里add插入元素会抛 IllegalStateException: Queue full<br />当阻塞队列空时,再往队列里 remove移除元素会抛NoSuchElementException |
| -------- | :----------------------------------------------------------- |
| 特殊值   | 插入方法,成功ture失败false<br />移除方法,成功返回出队列的元素,队列里面没有就返回nu‖ |
| 一直阻塞 | 当阻塞队列满时,生产者线程继续往队列里put元素,队列会一直阻塞生产线程直到put数据or响应中断退出<br />当阻塞队列空时,消费者线程试图从队列里take元素,队列会一直阻塞消费者线程直到队列可用。 |
| 超时退出 | 当阻塞队列满时，队列会阻塞生产者线程一段时间，超过限时后生产者线程会退出 |



```java
public class ArrayBlockingQueueDemo {
    BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);

    @Test
    public void testTimeout() throws InterruptedException {
        System.out.println(blockingQueue.offer("A"));
        System.out.println(blockingQueue.offer("B"));
        System.out.println(blockingQueue.offer("C"));
        System.out.println(blockingQueue.offer("D", 3, TimeUnit.SECONDS));

        System.out.println(blockingQueue.poll(3, TimeUnit.SECONDS));
        System.out.println(blockingQueue.poll(3, TimeUnit.SECONDS));
        System.out.println(blockingQueue.poll(3, TimeUnit.SECONDS));
        System.out.println(blockingQueue.poll(3, TimeUnit.SECONDS));
    }

    @Test
    public void testBlock() throws InterruptedException {
        blockingQueue.put("A");
        blockingQueue.put("B");
        blockingQueue.put("C");
//        block
//        blockingQueue.put("D");

        blockingQueue.take();
        blockingQueue.take();
        blockingQueue.take();
//        block
//        blockingQueue.take();
    }

    @Test
    public void testSpec(){
        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("b"));
        System.out.println(blockingQueue.offer("c"));
//        false
        System.out.println(blockingQueue.offer("d"));

        System.out.println(blockingQueue.peek());

        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
//        null
        System.out.println(blockingQueue.poll());
    }

    @Test
    public void testThrow(){
        blockingQueue.add("a");
        blockingQueue.add("b");
        blockingQueue.add("c");

        System.out.println(blockingQueue.element());

//        java.lang.IllegalStateException: Queue full
//        blockingQueue.add("d");
        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());
//        java.util.NoSuchElementException
//        System.out.println(blockingQueue.remove());
    }
}

```



```java
public class SynchronousQueueDemo {

    public static void main(String[] args) {
        BlockingQueue<String> blockingQueue = new SynchronousQueue<>();

        new Thread(() -> {

            try {
                System.out.println(Thread.currentThread().getName()+"\t put 1");
                blockingQueue.put("1");
                System.out.println(Thread.currentThread().getName()+"\t put 2");
                blockingQueue.put("2");
                System.out.println(Thread.currentThread().getName()+"\t put 3");
                blockingQueue.put("3");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "AAA").start();
        new Thread(() -> {
            try {
                TimeUnit.SECONDS.sleep(5);
                System.out.println(Thread.currentThread().getName()+"\t take 				"+blockingQueue.take());
                TimeUnit.SECONDS.sleep(5);
                System.out.println(Thread.currentThread().getName()+"\t take "+blockingQueue.take());
                TimeUnit.SECONDS.sleep(5);
                System.out.println(Thread.currentThread().getName()+"\t take "+blockingQueue.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "BBB").start();
    }
}

```

