# Java 并发编程概念

## 进程 线程

1. **进程(Process):**
   
   - 进程是操作系统中的基本单元,是程序在执行过程中分配和管理资源的基本单位。
   - 每个进程都有自己独立的内存空间,拥有自己的数据栈、程序计数器和其他执行状态信息。
   - 进程是操作系统资源分配的基本单位,是操作系统管理和调度的基本单位。
2. **线程(Thread):**
   
   - 线程是进程中的一个执行单元,是CPU调度和分派的基本单位。
   - 一个进程可以包含多个线程,这些线程共享进程的资源,如内存空间、I/O设备等。
   - 线程是独立运行和独立调度的基本单位,线程之间可以并发执行。
   - 线程的切换比进程的切换代价要小得多,因为线程不需要进行内存空间的切换。

### 例子

1. **进程的例子:**
   
   - 打开一个浏览器软件,就会创建一个浏览器进程。每个浏览器标签页都可能是一个单独的进程。
   - 运行一个文字编辑器软件,会创建一个编辑器进程。
   - 启动一个视频播放器,会创建一个视频播放进程。
   - 编译一个程序源代码,会创建一个编译进程。
2. **线程的例子:**
   
   - 在浏览器进程中,可能会有多个线程负责处理网页加载、渲染、脚本执行等任务。
   - 在文字编辑器进程中,可能会有一个主线程负责界面交互,另一个线程负责自动保存文件。
   - 在视频播放器进程中,可能会有一个线程负责视频解码,另一个线程负责音频解码,还有一个线程负责视频渲染。
   - 在编译进程中,可能会有多个线程同时进行词法分析、语法分析、代码生成等编译步骤。
3. **进程和线程的结合:**
   
   - 一个 Web 服务器程序,可能会创建多个进程,每个进程中又有多个线程来处理并发的 HTTP 请求。
   - 一个图形编辑软件,可能会创建一个主进程,负责界面交互,同时创建多个子进程来执行图像处理任务,每个子进程中又有多个线程来并行处理不同的图像效果。

## 串行 并发 并行

1. **串行(Serial):**
   
   - 串行指的是任务或者操作是一个接一个地顺序执行,一个任务完成后才能开始下一个任务。
   - 在串行处理中,任务之间是相互独立的,不存在任何交互或依赖关系。
   - 串行处理的优点是简单直观,易于实现和控制。缺点是效率低,无法发挥多核CPU的并行优势。
2. **并发(Concurrent):**
   
   - 并发指的是多个任务或操作在同一时间段内交替执行,但任意时刻只有一个任务在执行。
   - 在并发处理中,任务之间可能存在交互或依赖关系,需要协调和同步。
   - 并发处理的优点是提高了资源利用率和系统响应性,但需要额外的同步机制来处理任务间的依赖关系。
3. **并行(Parallel):**
   
   - 并行指的是多个任务或操作在同一时间内同时执行,利用多个CPU核心或计算单元来同时处理不同的任务。
   - 在并行处理中,任务之间是相互独立的,不存在交互或依赖关系。
   - 并行处理的优点是可以大幅提高计算性能,充分利用多核CPU的计算能力。缺点是实现复杂,需要特殊的编程模型和同步机制。

## 同步 异步 阻塞 非阻塞

1. **同步(Synchronous)和异步(Asynchronous):**
   
   - 同步指的是调用者必须等待被调用方法的返回,才能继续执行后续操作。
   - 异步指的是调用者不需要等待被调用方法的返回,可以继续执行后续操作,被调用方法完成时会通知调用者。
   - 同步操作会阻塞调用者,直到操作完成,而异步操作不会阻塞调用者。
   - 异步操作通常使用回调函数、事件或者Promise/Async/Await等机制来实现。
2. **阻塞(Blocking)和非阻塞(Non-blocking):**
   
   - 阻塞指的是执行某个操作时,当前线程或进程会被挂起,直到操作完成。
   - 非阻塞指的是执行某个操作时,当前线程或进程不会被挂起,而是立即返回,继续执行后续操作。
   - 阻塞操作会导致线程或进程被挂起,无法执行其他任务,而非阻塞操作不会阻塞线程或进程。
   - 非阻塞操作通常使用轮询、回调或者事件通知等机制来实现。

**同步/异步和阻塞/非阻塞的区别:**

- 同步/异步描述的是调用方和被调用方之间的关系,而阻塞/非阻塞描述的是调用方自身的状态。
- 同步操作一定是阻塞的,但异步操作可以是阻塞的,也可以是非阻塞的。
- 异步非阻塞操作是最常见的高性能I/O模型,例如事件驱动的网络服务器。

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY2NDU1MzU5MV19
-->