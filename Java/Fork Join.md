# Fork/Join

Java 的 Fork/Join 框架是针对可以递归拆分成小任务的工作提供了一种高效、异步执行的方式。它利用多线程并行计算来提升应用程序的响应性和性能。下面我将详细解释一下 Fork/Join 框架的工作原理和使用方法。

## **Fork/Join 框架概念**

- **工作窃取算法(Work-Stealing)**:这是 Fork/Join 框架的核心思想。每个线程会维护一个双端队列,用来存放需要执行的任务。如果当前线程的任务都执行完毕了,这个线程会去窃取其他工作线程的任务来执行。
- **ForkJoinTask**:这是 Fork/Join 框架中的一个重要抽象类,所有需要并行执行的任务都必须继承这个类或者其子类。它定义了在任务中执行 fork() 和 join() 操作所需的机制。
- **RecursiveAction**:继承了 ForkJoinTask 的抽象类,用于定义没有返回结果的任务。
- **RecursiveTask**:继承了 ForkJoinTask 的抽象类,用于定义有返回结果的任务。

## **Fork/Join 框架工作原理**

1. 首先,我们需要创建一个 ForkJoinPool 实例,它是真正干活的线程池。
2. 然后,创建一个继承了 ForkJoinTask (通常是继承 RecursiveTask 或 RecursiveAction)的任务对象。
3. 通过调用 ForkJoinTask 对象的 fork() 方法来要求 ForkJoinPool 实例异步地执行这个任务。
4. Fork/Join 框架会通过每个任务内部的计算结果,知道是否需要将任务一分为二。对于规模较小的任务,将直接执行该任务。
5. 对于规模较大的任务,Fork/Join 框架会将任务一分为二,生成新的子任务交给双端队列,然后重复步骤4。
6. 通过 join() 方法来等待子任务执行完毕,并收集子任务的计算结果。
7. 当所有任务完成后 ForkJoinPool 实例就可以关闭了。

## **Fork/Join 框架示例**

下面通过一个简单的例子来展示 Fork/Join 框架的使用:

```
public class Test05 {

    /**
     * 使用Fork/Join计算1-10000的和，
     * 当一个任务的计算数量大于3000的时候拆分任务。
     * 数量小于3000的时候就计算
     *
     * @param args
     */
    public static void main(String[] args) {
        // 记录开始时间
        long         start = System.currentTimeMillis();
        ForkJoinPool pool  = new ForkJoinPool();
        // 创建一个任务 1-10000 的和
        SumRecursiveTask task = new SumRecursiveTask(1, 10000l);
        // 执行任务   invoke方法会阻塞，直到任务执行完成返回结果
        Long result = pool.invoke(task);
        // 打印结果
        System.out.println("result=" + result);
        long end = System.currentTimeMillis();
        System.out.println("总的耗时:" + (end - start));

    }
}

// 使用Fork/Join计算1-10000的和，
class SumRecursiveTask extends RecursiveTask<Long> {

    // 定义一个拆分的临界值
    private static final long THRESHOLD = 3000l;

    private final long start;

    private final long end;

    public SumRecursiveTask(long start, long end) {
        this.start = start;
        this.end   = end;
    }

    @Override
    protected Long compute() {
        long length = end - start;
        // 这个类是拿来递归的这个判断中终止条件
        if (length <= THRESHOLD) {
            // 任务不用拆分，可以计算
            long sum = 0;
            for (long i = start; i <= end; i++) {
                sum += i;
            }
            System.out.println("计算：" + start + "-->" + end + ",的结果为：" + sum);
            return sum;
        } else {
            // 拆分任务
            long middle = (start + end) / 2;
            System.out.println("拆分:左边 " + start + "-->" + middle + ", 右边" + (middle + 1) + "-->" + end);
            // 下发任务
            SumRecursiveTask left = new SumRecursiveTask(start, middle);
            left.fork();
            // 下发任务
            SumRecursiveTask right = new SumRecursiveTask(middle + 1, end);
            right.fork();
            return left.join() + right.join();
        }
    }
}
//拆分:左边 1-->5000, 右边5001-->10000
//拆分:左边 5001-->7500, 右边7501-->10000
//拆分:左边 1-->2500, 右边2501-->5000
//计算：1-->2500,的结果为：3126250
//计算：2501-->5000,的结果为：9376250
//计算：7501-->10000,的结果为：21876250
//计算：5001-->7500,的结果为：15626250
//result=50005000
//总的耗时:3
```

```
+-----------------------+
                |     SumRecursiveTask  |
                |         compute()     |
                +-----------+------------+
                            |
                +----------+------------+
                |                       |
          +-----+------+            +---+---+
          | length <=  |            | length > |
          | THRESHOLD? |            | THRESHOLD?|
          +-----+------+            +-----+-----+
                |                         |
        +-------+--------+         +-------+--------+
        |  计算sum并返回  |         |   拆分任务      |
        +-------+--------+         +-------+--------+
                |                         |
                +------------------------+-+
                                         | |
                            +------------+  +-----------+
                            |                           |
                  +--------+--------+           +-------+-------+
                  | 创建左子任务left |           | 创建右子任务right|  
                  | left.fork()     |           | right.fork()  |
                  |   (异步执行)     |           |    (异步执行)   |
                  +--------+--------+           +-------+-------+
                            |                           |
                            +-----------+---------------+
                                        |
                                 +------+-------+
                                 | left.join() | <-----+ 阻塞点
                                 | right.join()| <----+| 阻塞点
                                 +------+-------+      |
                                        |              |
                                 +------+-------+      |
                                 |  返回结果     |      |
                                 | left+right  |<------+
                                 +---------------+
```

在上面的示例中:

1. 定义了一个 SumRecursiveTask 类继承自 RecursiveTask,目标是统计给定数组中所有元素的总和。
2. 在 compute() 方法中,如果任务数量小于 3000,则直接计算;否则将任务一分为二,并行执行子任务。
3. 在 main 函数中,创建了一个 ForkJoinPool,然后提交了一个 SumRecursiveTask 任务并调用其 invoke() 方法,等待执行结果。

Fork/Join 框架适合处理可以并行、拆分和合并的任务,充分利用现代服务器多核 CPU 的计算能力,能够有效提高应用程序的响应性能。

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4ODc1MDM1MTddfQ==
-->