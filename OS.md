



### 1 进程和线程

- **概念**

进程和线程是操作系统中的两个重要概念，它们可以用来执行任务并实现并发和并行。

进程是操作系统中的一个执行实体，是计算机程序运行时的一次执行过程。进程由程序、数据和进程控制块（PCB）组成。每个进程拥有独立的地址空间、文件描述符、环境变量、堆栈等资源，因此进程之间的数据是相互隔离的。进程通过操作系统的调度，可以并行或交替执行，进程之间相互独立。

线程是进程的一个执行单元，是进程内的一个独立的控制流程，共享同一个地址空间和系统资源。线程由程序计数器、寄存器集合和堆栈组成。线程是操作系统调度的最小单位，可以独立执行任务，并与其他线程并发执行。不同的线程可以在同一进程中共享数据和上下文，通过共享内存进行通信和同步。

- **区别**

  - **资源开销**：在同一进程中，线程共享同一个进程的资源，如内存空间、打开的文件描述符等，因此创建、销毁和切换线程的开销比进程小。

  - **并发性**：进程可以并行执行，即多个进程在多个 CPU 上同时执行。线程则是在同一个进程中并发执行，即多个线程共享进程的资源和上下文，并在同一个 CPU 上交替执行。

  - **通信和同步**：进程之间通信需要使用进程间通信（IPC）机制，如管道、消息队列等。而线程可以通过共享内存、信号量等直接进行通信和同步。

- **实现**

  - 多进程实现：可以通过调用操作系统提供的 fork() 系统调用来创建子进程，子进程与父进程拥有相同的代码和数据。通过在 fork() 后修改子进程的执行路径，可以实现在不同子进程中执行不同的任务，实现并行处理。

  ```c++
  #include <iostream>
  #include <unistd.h>
  #include <sys/types.h>
  #include <sys/wait.h>
  void task(int num) {
      std::cout << "Child process " << num << " with PID " << getpid() << " started" << std::endl;
  }
  
  int main() {
      std::cout << "Parent process with PID " << getpid() << " started" << std::endl;
      
      for (int i = 0; i < 3; i++) {
          pid_t pid = fork();
          if (pid == 0) {
              // child process
              task(i);
              _exit(0);
          }
      }
      
      // wait for all child processes to finish
      for (int i = 0; i < 3; i++) {
          wait(NULL);
      }
      
      std::cout << "All child processes completed" << std::endl;
      
      return 0;
  }
  ```

  

  - 多线程实现：可以使用线程库（如 pthread）提供的函数来创建和管理线程。通过创建多个线程并使用共享内存等机制进行通信和同步，实现并发处理。

  ```c++
  #include <iostream>
  #include <thread>
  
  void task(int num) {
      std::cout << "Thread " << num << " started" << std::endl;
  }
  
  int main() {
      std::cout << "Main thread started" << std::endl;
      std::thread threads[3];
      
      for (int i = 0; i < 3; i++) {
          threads[i] = std::thread(task, i);
      }
      
      // wait for all threads to finish
      for (int i = 0; i < 3; i++) {
          threads[i].join();
      }
      
      std::cout << "All threads completed" << std::endl;
  
      return 0;
  }
  ```

  



---





### 2 缓存算法

缓存算法是计算机科学中用于管理缓存中数据存储和替换的算法。缓存是一种用于临时存储数据的高速存储器，它可以减少对慢速存储器（如磁盘或网络）的访问次数，从而提高数据访问速度。

常见的缓存算法包括：

1. 先进先出（FIFO）：按照数据进入缓存的先后顺序进行替换。最早进入缓存的数据会被最先替换。
2. 最近最少使用（LRU）：根据数据项最近被使用的时间进行替换。最近使用较少的数据项会被优先替换。
3. 最不经常使用（LFU）：根据数据项被使用的频率进行替换。使用频率较低的数据项会被优先替换。
4. 最近使用的（MRU）：与LRU相反，根据数据项最近被使用的时间进行替换。最近使用的数据项会被优先替换。
5. 随机替换（Random）：随机选择要替换的数据项。



---



### 3 进程内存布局



32位进程的内存布局主要包含以下几个块：

[内核空间](https://www.zhihu.com/search?q=内核空间&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})：供内核使用，存放的是内核代码和数据

stack：这就是我们经常所说的栈，用来存储[自动变量](https://www.zhihu.com/search?q=自动变量&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})([automatic variable](https://www.zhihu.com/search?q=automatic variable&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260}))

mmap:也成为[内存映射](https://www.zhihu.com/search?q=内存映射&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})，用来在进程[虚拟内存](https://www.zhihu.com/search?q=虚拟内存&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})地址空间中分配地址空间，创建和[物理内存](https://www.zhihu.com/search?q=物理内存&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})的映射关系

heap:就是我们常说的堆，动态内存的分配都是在堆上

bss:包含所有未初始化的全局和[静态变量](https://www.zhihu.com/search?q=静态变量&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})，此段中的所有变量都由0或者空[指针初始化](https://www.zhihu.com/search?q=指针初始化&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})，程序加载器在加载程序时为BSS段分配内存

data:初始化的数据块

- 包含显式初始化的[全局变量](https://www.zhihu.com/search?q=全局变量&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})和静态变量
- 此段的大小由[程序源代码](https://www.zhihu.com/search?q=程序源代码&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})中值的大小决定，在运行时不会更改
- 它具有读写权限，因此可以在运行时更改此段的变量值
- 该段可进一步分为[初始化只读区](https://www.zhihu.com/search?q=初始化只读区&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})和初始化读写区

text：也称为文本段

- 该段包含已编译程序的[二进制文件](https://www.zhihu.com/search?q=二进制文件&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})。
- 该段是一个只读段，用于防止程序被意外修改
- 该段是可共享的，因此对于[文本编辑器](https://www.zhihu.com/search?q=文本编辑器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2973245260})等频繁执行的程序，内存中只需要一个副本



---

### 4 I/O多路复用

##### 什么是多路复用

- 多路: 指的是多个socket网络连接;
- 复用: 指的是复用一个线程、使用一个线程来检查多个文件描述符（Socket）的就绪状态
- 多路复用主要有三种技术：select，poll，epoll。epoll是最新的, 也是目前最好的多路复用技术；

##### IO多路复用概念

I/O多路复用是一种基于事件驱动的编程模型，允许一个进程同时监听多个输入和输出流，并在可用时对其进行处理，提高系统的性能和吞吐量。它是通过将多个I/O操作集中管理，从而降低了系统的开销和资源消耗。

在传统的阻塞IO模型中，每个IO操作都会导致进程阻塞，直到数据就绪才能进行下一步操作。而在I/O多路复用模型中，通过使用专门的系统调用（如select、poll、epoll），可以同时监视多个流的状态，当流中有数据可读或可写时，系统会通知应用程序进行相应的处理，而不需要将进程阻塞在等待数据上。

##### I/O多路复用的优点

1. 高效利用资源：通过一次注册多个流，只需要一个线程或进程就可以同时处理多个I/O操作，从而减少了线程或进程的数量和开销，提高了系统的资源利用率。
2. 提高响应速度：当有多个流需要进行I/O操作时，可以同时监听它们的状态，一旦有流有数据可读或可写，就可以立即进行相应的处理，避免了阻塞和等待的过程，提高了系统的响应速度。
3. 简化编程模型：使用I/O多路复用模型可以将多个流的事件集中处理，避免了多线程或多进程之间的同步和通信问题，使得编程更加简单和清晰。

##### 常用的I/O多路复用机制

**select**

一种较为简单的多路复用机制，通过将要监听的文件描述符放入一个集合中，然后调用select函数进行监听，一旦有事件就绪，返回就会返回可读或可写的文件描述符集合，然后进行相应的处理。

```c++
#include <sys/select.h>
#include <sys/time.h>
int select(int maxfdp1, fd_set *readset, fd_set *writeset, fd_set *exceptset,
          const struct timeval *timeout);
// return：表示此时有多少个监控的描述符就绪，若超时则为0，出错为-1。
```

select 函数监视的文件描述符分三类，分别是 writefds、readfds 和 exceptfds。

select 调用过程

1）用户进程需要监控某些资源 fds，在调用 select 函数后会阻塞，操作系统会将用户线程加入这些资源的等待队列中。

2）直到有描述符就绪（有数据可读、可写或有 except）或超时（timeout 指定等待时间，如果立即返回设为 null 即可），函数返回。

3）select 函数返回后，中断程序唤起用户线程。用户可以遍历 fds，通过 FD_ISSET 判断具体哪个 fd 收到数据，并做出相应处理。

**poll**

与select类似，也是一种多路复用机制，但相较于select，poll使用链表来存储文件描述符，这样不会受到文件描述符数量限制的影响。

```c++
int poll(struct pollfd* fds, int nfds, int timeout);
/*
struct pollfd{
	int fd;        // 感兴趣fd
	short events;  // 监听事件
	short revents; // 就绪事件
};
*/
// return：表示此时有多少个监控的描述符就绪，若超时则为0，出错为-1。

```

**epoll**

是Linux系统提供的一种高性能的多路复用机制。它使用红黑树和时间轮的数据结构来存储和管理文件描述符，可以支持大规模的连接并发，具有高效的触发方式和低延迟。

​	epoll的接口非常简单，一共就三个函数：

1）epoll_create：创建一个epoll句柄

```c++
int epoll_create(int size);
```

- **功能：**该函数生成一个 epoll 专用的文件描述符。
- **参数size:** 用来告诉内核这个监听的数目一共有多大，参数 size 并不是限制了 epoll 所能监听的描述符最大个数，只是对内核初始分配内部数据结构的一个建议。自从 linux 2.6.8 之后，size 参数是被忽略的，也就是说可以填只有大于 0 的任意值。
- **返回值：**如果成功，返回poll 专用的文件描述符，否者失败，返回-1。

2）epoll_ctl：向 epoll 对象中添加/修改/删除要管理的连接

```c++
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event); 
```

- **功能：**epoll 的事件注册函数，它不同于 select() 是在监听事件时告诉内核要监听什么类型的事件，而是在这里先注册要监听的事件类型。

- **参数epfd:** epoll 专用的文件描述符，epoll_create()的返回值

- **参数op:** 表示动作，用三个宏来表示：

  - EPOLL_CTL_ADD：注册新的 fd 到 epfd 中；

  - EPOLL_CTL_MOD：修改已经注册的fd的监听事件；

  - EPOLL_CTL_DEL：从 epfd 中删除一个 fd；

- **参数fd:** 需要监听的文件描述符
- **参数event:** 告诉内核要监听什么事件，struct epoll_event 结构如:
- **events****可以是以下几个宏的集合：**
  - EPOLLIN ：表示对应的文件描述符可以读（包括对端 SOCKET 正常关闭）；
  - EPOLLOUT：表示对应的文件描述符可以写；
  - EPOLLPRI：表示对应的文件描述符有紧急的数据可读（这里应该表示有带外数据到来）；
  - EPOLLERR：表示对应的文件描述符发生错误；
  - EPOLLHUP：表示对应的文件描述符被挂断；
  - EPOLLET ：将 EPOLL 设为边缘触发(Edge Trigger)模式，这是相对于水平触发(Level Trigger)来说的。
  - EPOLLONESHOT：只监听一次事件，当监听完这次事件之后，如果还需要继续监听这个 socket 的话，需要再次把这个 socket 加入到 EPOLL 队列里
- **返回值：**0表示成功，-1表示失败。

3）epoll_wait：等待其管理的连接上的 IO 事件

```cpp
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout); 
```

- **功能：**等待事件的产生，收集在 epoll 监控的事件中已经发送的事件，类似于 select() 调用。

- **参数epfd:** epoll 专用的文件描述符，epoll_create()的返回值

- **参数events:** 分配好的 epoll_event 结构体数组，epoll 将会把发生的事件赋值到events 数组中（events 不可以是空指针，内核只负责把数据复制到这个 events 数组中，不会去帮助我们在用户态中分配内存）。

- **参数maxevents:** maxevents 告之内核这个 events 有多少个 。

- **参数timeout:** 超时时间，单位为毫秒，为 -1 时，函数为阻塞。

- **返回值：**

  - 如果成功，表示返回需要处理的事件数目

  - 如果返回0，表示已超时

  - 如果返回-1，表示失败

|              | select                                                       | poll                                                         | epoll                                             |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------- |
| 性能         | 随着连接数的增加，性能急剧下降，处理成千上万的并发连接数时，性能很差 | 随着连接数的增加，性能急剧下降，处理成千上万的并发连接数时，性能很差 | 随着连接数的增加，性能基本没有变化                |
| 连接数       | 一般1024                                                     | 无限制                                                       | 无限制                                            |
| 内存拷贝     | 每次调用select拷贝                                           | 每次调用poll拷贝                                             | fd首次调用epoll_ctl拷贝，每次调用epoll_wait不拷贝 |
| 数据结构     | bitmap                                                       | 数组                                                         | 红黑树                                            |
| 内在处理机制 | 线性轮询                                                     | 线性轮询                                                     | FD挂在红黑树，通过事件回调callback                |
| 时间复杂度   | O(n)                                                         | O(n)                                                         | O(1)                                              |



---

### 5 堆和栈的区别

堆和栈是计算机内存中两种不同的内存分配方式，主要用于管理变量和数据的存储。

1. **分配方式：**
   - 栈：栈是由编译器自动管理的，用于存储局部变量和函数调用的相关信息。在函数调用时，会自动在栈上分配内存。栈的分配和释放是自动进行的，遵循"后进先出"的原则。
   - 堆：堆是程序员手动分配和释放的，用于存储动态内存分配的数据。程序员需要显式地申请内存，并在不使用时显示地释放内存。
2. **管理方式：**
   - 栈：栈是一种高效的内存管理方式，由于它是按照固定的顺序分配和释放内存，管理起来非常简单。栈的分配和释放速度快，开销低。
   - 堆：堆的管理相对复杂，需要程序员手动控制内存的分配和释放。由于堆是动态分配的，可能会产生内存碎片，造成内存的浪费。堆的分配和释放速度相对较慢。
3. **内存空间：**
   - 栈：栈的大小是有限的，一般在编译时就已经确定。栈中存储的变量通常是局部变量和函数调用的相关信息。栈的大小由操作系统决定，过多的栈帧可能导致栈溢出（stack overflow）。
   - 堆：堆的大小一般比栈大得多，并且不受操作系统的限制，大小由程序员根据需要进行动态分配。堆中存储的数据多为动态分配的对象和全局变量。
4. **生命周期：**
   - 栈：栈的生命周期由变量的作用域决定，在变量超出作用域后，栈自动释放变量的内存。
   - 堆：堆的生命周期由程序员手动控制，需要显式地申请内存和释放内存。在不再使用堆中的数据时，程序员需要手动释放内存，否则可能会造成内存泄漏。

总结：栈用于管理局部变量和函数调用相关信息，由编译器自动管理；堆用于动态分配内存，需要程序员手动控制分配和释放。栈的分配速度快，开销低，但大小有限；堆的分配速度相对慢，大小较大，需手动管理。

> 在Windows中，栈的默认大小为1MB；在Unix/Linux系统中，栈的默认大小为8MB



---

### 6 虚拟地址空间是什么

##### 概述

虚拟地址空间是操作系统为每个进程分配的一块内存区域，它提供了一个抽象的视图，让每个进程认为自己独占了整个系统的物理内存。虚拟地址空间的大小是固定的，但不同操作系统的虚拟地址空间大小可能有所不同。

##### 组成

虚拟地址空间通常被划分为几个不同的区域，包括：

1. 代码区：也称为文本区，存放程序的可执行代码，通常是只读的。
2. 数据区：存放程序的全局变量、静态变量和常量数据。
3. 堆区：由程序员动态分配和释放的内存，用于存放程序运行时产生的可变大小的数据。
4. 栈区：用于存放函数调用过程中的局部变量、参数和函数调用的上下文信息。
5. 共享库区：存放系统共享库的代码和数据，不同进程可以共享同一份共享库的虚拟内存。
6. 内核区：用于存放操作系统的内核代码和数据，通常不允许进程直接访问。

##### 作用

1. 内存隔离：每个进程都拥有独立的虚拟地址空间，进程间的内存互不干扰，从而确保数据的安全性和隐私性。
2. 内存管理：操作系统根据需要，动态地为每个进程分配和回收内存。虚拟地址空间使得程序开发人员不需要关注具体物理内存的分配和回收，只需关心虚拟地址的使用。
3. 内存映射：虚拟地址空间允许将磁盘文件映射到内存中，通过读写内存，实现对文件的读写操作。这样可以提高文件操作的效率，简化程序的编写。
4. 内存保护：虚拟地址空间允许操作系统对每个进程的内存进行保护，防止进程越界访问其他进程的内存或者操作系统内核的内存。如果进程发生越界访问，操作系统会立即终止该进程，防止其对系统产生不良影响。
5. 虚拟内存：虚拟地址空间还可以实现虚拟内存技术，将内存和磁盘空间结合起来，允许操作系统将进程暂时不用的数据或代码保存到磁盘，并在需要时再恢复到内存中。这样可以有效地扩展可用内存大小，增加系统的性能和稳定性。



---

### 7 进程间通信方式有哪些？

#### **什么是进程通信**

进程通信（ InterProcess Communication，IPC）就是指**进程之间的信息交换**。

#### **进程通信的背景**

（1）进程在操作系统内核当中都是独立的进程控制块（PCB），也就是一个一个的struct_task{...}结构体对象

![image-20231030205450223](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030205450223.png)



（2）每个进程都有自己独立的进程虚拟地址空间，也就是说，进程之间的数据是独立的，从而导致进程的独立性

![image-20231030212416200](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030212416200.png)

（3）要想在进程与进程间建立联系，需要通过内核，在内核中开辟一块缓冲区，两个进程的信息在缓冲区中进行交换或者传递。

![image-20231030205756719](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030205756719.png)



#### **进程通信的方式**

##### 管道 -- pipe

管道是一种半双工的通信方式，数据只能单向流动

- **管道的种类：**

  - **匿名管道**：只能在具有亲缘关系的进程间使用（进程的亲缘关系通常是指父子进程关系）

  - **命名管道**：允许无亲缘关系进程间的通信。

###### 匿名管道

- **特点**

  - 只能用于具有共同祖先的进程（具有亲缘关系的进程）之间进行通信；通常，一个管道由一个进程创建，然后该进程调用fork()，此后父子进程之间就可以应用该管道。

  - 一般而言，进程退出，管道释放，所以管道的生命周期跟随进程。

  - 一般而言，内核会对管道操作进行同步与互斥

  - 管道是半双工的，数据只能向一个方向流动；需要双方通信时，需要建立起两个管道。

![image-20231030220106938](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030220106938.png)

- **函数原型**

  ```c++
  #include <unistd.h>
  int pipe(int fd[2]);
  ```

  - 函数
    - pipe函数的成功调用会创建出一个匿名管道，对应在内核中会有一个内核缓冲区
    - 此函数只是创建了管道，要想从管道中读取数据或者向管道中写入数据，需要使用read()和write()函数来完成。
    - 当管道通信结束后，需要使用close()函数关闭管道的读写端。
  - 输入
    - fd[0] 表示管道的读端
    - fd[1]表示管道的写端
    - 都是文件描述符
    - fd参数是输出型参数，不需要程序员赋值，由pipe函数在调用时赋值
  - 返回值
    - 创建成功返回0，创建失败返回-1
    - 并且设置了适当的错误返回信息。

- **函数使用**

```c++
#include <stdio.h>
#include <unistd.h>
#include <string.h>

#define MAXSIZE 100

int main()
{
    int fd[2], pid, line;
    char message[MAXSIZE];
    
    /*创建管道*/
    if(pipe(fd) == -1)
    {
        perror("create pipe failed!");
        return 1;
    }
    /*创建新进程*/
    else if((pid = vfork()) < 0)
    {
        // vfork() : 创建子进程，并先执行子进程，在执行父进程
        perror("not create a new process!");
        return 1;
    }
    /*子进程*/
    else if(pid == 0)
    {
        close(fd[0]);
        printf("child process SEND message!\n");
        write(fd[1], "Hello Linux!",12); /*向文件中写入数据*/ 
        _exit(0);
    }
    /*父进程*/
    else if(pid > 0)
    {
        close(fd[1]);
        printf("parent process RECEIVE message is:\n");
        line = read(fd[0], message, MAXSIZE); /*读取消息，返回消息长度*/
        write(STDOUT_FILENO,message,line); /*将消息写入终端*/
        printf("\n");
        wait(NULL);
        _exit(0);
    }
    return 0;
}


```

输出

![image-20231030215615047](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030215615047.png)

###### 命名管道

- **函数原型**

  ```shell
  $ mkfifo filename
  ```

  or

  ```c++
  #include <sys/tyoes.h>
  #include <sys/stat.h>
  int mkfifo(const char* pathname, mode_t mode);
  ```

  - pathname是一个文件的路径名，是创建的一个命名管道的文件名；
  - mode是指文件的权限

- **函数使用**

```c++
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
// 文件路径
#define FIFO "/root/process/hello"

int main()
{
    int fd;
    int pid;
    char r_msg[BUFSIZ];
    if((pid = mkfifo(FIFO,0777)) == -1) /*创建命名管道*/
    {
		perror("create fifo channel failed!");
		return 1;
    }
    else
    	printf("create success!\n");
    
    fd = open(FIFO, O_RDWR);  /*打开命名管道*/
    if(fd == -1)
    {
        perror("cannot open the FIFO");
        return 1;
    }
    if(write(fd,"hello world", 12) == -1)  /*写入消息*/
    {
        perror("write data error!");
        return 1;
    }
    else
    	printf("write data success!\n");
    if(read(fd, r_msg, BUFSIZ) == -1)  /*读取消息*/
    {
        perror("read error!");
        return 1;
    }
    else
    	printf("the receive data is:  %s\n",r_msg);
    
    close(fd);   /*关闭文件*/
    
    return 0;
}


```

输出

![image-20231030220736834](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231030220736834.png)



- mkfifo()进行数据传递的过程

  - 用mkfifo()函数创建一个命名管道，命名管道文件路径为/root/process/hello

  - 调用open()函数打开该命名管道文件，以读写的方式打开。

  - 调用write()函数向文件写入信息"hello world", 同时调用read()函数读取该文件，输出到终端。

  - 调用close()函数关闭打开的命名管道文件。



****



##### 消息队列 -- msg queue

- **概述**

管道的缺点就是通信效率低，且只能传输无格式的字节流。

对于消息队列而言，如下图所示，进程A如果想要给进程B发送消息，只需要把数据放入到消息队列中，进程B在需要的时候自行去消息队列中读取数据即可，反过来同理。

<img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231031193241055.png" alt="image-20231031193241055" style="zoom:50%;" />

**消息队列的本质就是存放在内存中的消息的链表，而消息本质上是用户自定义的数据结构**。

一个消息队列由一个标识符（即队列ID）来标识。

- **函数原型**

```c++
#include <sys/msg.h> // 消息队列相关函数及数据结构头文件


int msgget(key_t key, int msg_flg); // 创建消息队列，key值唯一标识该消息队列
/**
 * param:
 	** key: 某个消息队列的名字（关键字），key值的获取既可以通过宏定义key值，也可以通过ftok函数生成key值
 	** msgflg: 由九个权限标志构成
 		*** IPC_CREAT:如果消息队列对象不存在，则创建，否则进行打开操作
 		*** IPC_EXCL:如果消息队列对象不存在则创建，否则产生一个错误并返回
 * return:
 	** 成功则返回一个非负整数，表示该消息队列的标识码
 	** 失败则返回“-1”
*/
int msgrcv(int msg_id, void *msg_ptr, size_t msg_sz, long int msg_type, int msg_flg);// 接收消息
/**
 * param:
 	** msg_id: 由masgget()函数返回的消息队列标识码
 	** msg_ptr: 指针指向准备接收的消息（指的是结构体中内容text的大小，不是结构体本身的大小）
 	** msg_sz: msg_ptr指向的消息的长度
 	** msg_type: 可以实现接受优先级的简单形式，
 		*** msg_type = 0 : 返回队列的第一条消息
 		*** msg_type > 0 : 返回队列第一条类型等于msg_type的消息
 		*** msg_type < 0 : 返回队列第一条类型小于等于msgtype绝对值的消息
 	** msg_flg: 控制着队列没有相应类型的消息可以接收时将要发生的事情
 		*** msg_flg = IPC_NOWAIT表示队列没有可读消息不等待，返回ENOMSG错误
 		*** msg_flg = MSG_NOERROR表示消息大小超过msg_sz时被截断
 		*** msg_flg = MSC_EXCEPT表示，当mssg_type>0时，返回队列第一条不等于msg_type的消息
 			
 * return:
 	** 成功返回实际放到接收缓冲区里的字符个数
 	** 失败返回-1
*/


int msgsnd(int msg_id, const void *msg_ptr, size_t msg_sz, int msg_flg);// 发送消息
/**
 * param:
 	** msg_id: 由masgget()函数返回的消息队列标识码
 	** msg_ptr: 指针指向准备发送的消息
 	** msg_sz: msg_ptr指向的消息的长度，（指的是结构体中内容text的大小，不是结构体本身的大小）
 	** msg_flg: 控制着当前消息队列满或到达系统上限时将要发生的事情
 		*** msg_flg = IPC_NOWAIT表示队列满的时候不等待，返回EAGAIN错误
 * return:
 	** 成功返回0
 	** 失败返回-1
*/


int msgctl(int msg_id, int cmd, struct msgid_ds *buf);// 控制消息队列函数
/**
 * param:
 	** msg_id: 由masgget()函数返回的消息队列标识码
 	** cmd: 将要采取的动作
 		*** IPC_STAT: 把msgid_ds结构中的数据设置为消息队列的当前关联值
 		*** IPC_SET: 在进程有足够权限的前提下，把消息队列的当前关联值设置为msgid_ds数据结构中给定的值
 		*** IPC_RMID: 删除消息队列（若选择删除，则第三个参数为NULL）
 	*
 * return:
 	** 成功返回0
 	** 失败返回-1
*/
```

- **函数使用**

server.c

```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/stat.h>
#include<time.h>

// 用于创建一个唯一的key
#define MSG_FILE "/etc/passwd"

// 消息结构
struct msg_form {
    long mtype;
    char mtext[256];
};

int main()
{
    int msqid;
    key_t key;
    struct msg_form msg;
  
   // 获取key值
   if((key = ftok(MSG_FILE,'z')) < 0)
   {
       perror("ftok error");
       exit(1);
    }

  // 打印key值
    printf("Message Queue - Server key is: %d.\n", key);

   // 创建消息队列
    if ((msqid = msgget(key, IPC_CREAT|0777)) == -1)
    {
       perror("msgget error");
       exit(1);
    }
 
    // 打印消息队列ID及进程ID
    printf("My msqid is: %d.\n", msqid);
    printf("My pid is: %d.\n", getpid());

    // 循环读取消息
    for(;;) 
    {
        msgrcv(msqid, &msg, sizeof(msg.mtext), 888, 0);// 返回类型为888的第一个消息
        printf("Server: receive msg.mtext is: %s.\n", msg.mtext);
        printf("Server: receive msg.mtype is: %d.\n", msg.mtype);

        msg.mtype = 999; // 客户端接收的消息类型
        sprintf(msg.mtext, "hello, I'm server %d", getpid());
        msgsnd(msqid, &msg, sizeof(msg.mtext), 0);
   }
    
   if (msgctl(msgid, IPC_RMID, 0) == -1) { # 删除消息队列，如果删除失败执行if语句并退出
        fprintf(stderr, “msgctl(IPC_RMID) failed\n”);
        exit(EXIT_FAILURE);
   }
   exit(EXIT_SUCCESS);
    
   return 0;
 }
```

client.c

```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/stat.h>
#include<time.h>
// 用于创建一个唯一的key
#define MSG_FILE "/etc/passwd"

// 消息结构
struct msg_form {
    long mtype;
    char mtext[256];
};

int main()
{
    int msqid;
    key_t key;
    struct msg_form msg;

    // 获取key值
    if ((key = ftok(MSG_FILE, 'z')) < 0) 
    {
        perror("ftok error");
        exit(1);
    }

    // 打印key值
    printf("Message Queue - Client key is: %d.\n", key);

    // 打开消息队列
    if ((msqid = msgget(key, IPC_CREAT|0777)) == -1) 
    {
        perror("msgget error");
        exit(1);
    }

    // 打印消息队列ID及进程ID
    printf("My msqid is: %d.\n", msqid);
    printf("My pid is: %d.\n", getpid());

    // 添加消息，类型为888
    msg.mtype = 888;
    sprintf(msg.mtext, "hello, I'm client %d", getpid());
    msgsnd(msqid, &msg, sizeof(msg.mtext), 0);

    // 读取类型为999的消息
    msgrcv(msqid, &msg, sizeof(msg.mtext), 999, 0);
    printf("Client: receive msg.mtext is: %s.\n", msg.mtext);
    printf("Client: receive msg.mtype is: %d.\n", msg.mtype);
    return 0;
}
```



- **消息队列和管道的对比**

1.匿名管道是跟随进程的，消息队列是跟随内核的，也就是说进程结束之后，匿名管道就死了，但是消息队列还会存在（除非显示调用函数销毁）

2.管道是文件，存放在磁盘上，访问速度慢，消息队列是数据结构，存放在内存，访问速度快

3.管道是数据流式存取，消息队列是数据块式存取



---



##### 共享内存 -- shared memory

共享内存（Shared Memory）,指的是两个或多个进程共享一个给定的存储区

- **特点**

  - 共享内存是最快的一种 IPC，因为进程是直接对内存进行存取。

  - 因为多个进程可以同时操作，所以需要进行同步。

  - 信号量+共享内存通常结合在一起使用，信号量用来同步对共享内存的访问。

- **函数原型**

```c
#include <stdio.h>
int shmget(key_t key, size_t size, int flag);
/**
 * 创建或获取一个共享内存
 * param: 
 	* key: 进程间通信关键字，ftok()的返回值
 	* size: 该共享存储段的长度（字节）
 	* flag: 表示函数的行为及共享内存的权限
 		** IPC_CREAT: 如果不存在则创建
 		** IPC_EXCL: 如果已经存在则返回错误
 * return:
 	* 成功返回共享内存ID
 	* 失败返回-1
*/

void shmat(int shm_id, const void *addr, int flag);
/**
 * 连接共享内存到当前进程的地址空间
 * param: 
 	* shm_id: 共享内存标识符，shmget()的返回值
 	* addr: 共享内存映射地址，如果设置为NULL则由系统自动指定
 	* flag: 共享内存段内的访问权限和映射条件（通常为0）
 		** 0: 具有可读可写权限
 		** SHM_RDONLY: 只读
 		** SHM_RND: addr非空时才有效
 * return:
 	* 成功返回共享内存段映射地址（也就是返回指向共享内存的指针）
 	* 失败返回-1
*/

int shmdt(void addr); 
/**
 * 断开与共享内存的连接
 * param: 
 	* addr: 共享内存映射地址
 * return:
 	* 成功返回0
 	* 失败返回-1
*/

int shmctl(int shm_id, int cmd, struct shmid_ds *buf);
/**
 * 控制共享内存的相关信息
 * param: 
 	* shm_id: 共享内存标识符
 	* cmd: 函数功能的控制
 		** IPC_RMID: 删除
 		** IPC_SET: 设置shmid_ds参数，相当于把共享内存原来的属性值替换为buf里的属性值
 		** IPC_STAT: 保存shmid_ds参数，把共享内存原来的属性值被分到buf中
 		** SHM_LOCK: 锁定共享内存段（root）（SHM_LOCK 用于锁定内存，禁止内存交换。并不代表共享内存被锁定后禁止其它进程访问。其真正的意义是：被锁定的内存不允许被交换到虚拟内存中。这样做的优势在于让共享内存一直处于内存中，从而提高程序性能。）
 		** SHM_UNLOCK: 解锁共享内存段
 	* buf: shmid_ds数据类型的地址，可用于存放或修改共享内存的属性
 * return:
 	* 成功返回0
 	* 失败返回-1
*/
```

- **函数使用**

创建两个进程，在 A 进程中创建一个共享内存，并向其写入数据，通过 B 进程从共享内存中读取数据。

写端：

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>

#define BUFSZ 512

int main(int argc, char *argv[])
{
    int shmid;
    int ret;
    key_t key;
    char *shmadd;
 
    //创建key值
    key = ftok("../", 2015);
    if(key == -1)
    {
        perror("ftok");
    }
 
    //创建共享内存
    shmid = shmget(key, BUFSZ, IPC_CREAT|0666);    
    if(shmid < 0)
    {
        perror("shmget");
        exit(-1);
    }
 
    //映射
    shmadd = shmat(shmid, NULL, 0);
    if(shmadd < 0)
    {
        perror("shmat");
        _exit(-1);
    }
 
    //拷贝数据至共享内存区
    printf("copy data to shared-memory\n");
    bzero(shmadd, BUFSZ); // 共享内存清空
    strcpy(shmadd, "how are you, lh\n");
 
    return 0;
}

```

读端：

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>

#define BUFSZ 512

int main(int argc, char *argv[])
{
    int shmid;
    int ret;
    key_t key;
    char *shmadd;
 
    //创建key值
    key = ftok("../", 2015);
    if(key == -1)
    {
        perror("ftok");
    }
 
    system("ipcs -m"); //查看共享内存
 
    //打开共享内存
    shmid = shmget(key, BUFSZ, IPC_CREAT|0666);
    if(shmid < 0)
    {
        perror("shmget");
        exit(-1);
    }
 
    //映射
    shmadd = shmat(shmid, NULL, 0);
    if(shmadd < 0)
    {
        perror("shmat");
        exit(-1);
    }
 
    //读共享内存区数据
    printf("data = [%s]\n", shmadd);
 
    //分离共享内存和当前进程
    ret = shmdt(shmadd);
    if(ret < 0)
    {
        perror("shmdt");
        exit(1);
    }
    else
    {
        printf("deleted shared-memory\n");
    }
 
    //删除共享内存
    shmctl(shmid, IPC_RMID, NULL);
 
    system("ipcs -m"); //查看共享内存
 
    return 0;
}
```



---



##### 信号量 --Semaphore

- **概述**

​	信号量用于实现进程间的互斥与同步，而不是用于存储进程间通信数据。

- **特点**

  - 信号量用于进程间同步，若要在进程间传递数据需要结合共享内存。

  - 信号量基于操作系统的 PV 操作，程序对信号量的操作都是[原子操作](https://www.zhihu.com/search?q=原子操作&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2931441698})。

  - 每次对信号量的 PV 操作不仅限于对信号量值加 1 或减 1，而且可以加减任意正整数。

  - 支持信号量组。

- **工作原理**

  - P(sv)：如果sv的值大于零，就给它减1；如果它的值为零，就挂起该进程的执行

  - V(sv)：如果有其他进程因等待sv而被挂起，就让它恢复运行，如果没有进程因等待sv而挂起，就给它加1.

  > PV操作均为原子操作

- - **函数原型**

    最简单的信号量是只能取 0 和 1 的变量，这也是信号量最常见的一种形式，叫做二值信号量（Binary Semaphore）。而可以取多个正整数的信号量被称为通用信号量。Linux 下的信号量函数都是在通用的信号量数组上进行操作，而不是在一个单一的二值信号量上进行操作。

    ```C
    #include <stdio.h>
    
    int semget(key_t key, int num_sems, int sem_flags);
    /**
     * 创建或获取一个信号量组
     * param:
     	* key: 进程间通信关键字，ftok()的返回值
     	* num_sems: 指定信号量集中需要的信号量数目，基本是1
     	* flag: 表示信号的创建标志
     		** IPC_CREAT: 如果不存在则创建
     		** IPC_EXCL: 如果已经存在则返回错误
     * return:
     	* 成功返回信号量集ID
     	* 失败返回-1
    */
    
    int semop(int semid, struct sembuf semoparray[], size_t numops);  
    /**
     *  对信号量组进行操作，改变信号量的值
     * param:
     	* semid: semget()返回的信号量集ID
     	* struct sembuf定义如下
     		'''
     		struct sembuf{ 
                short sem_num;   //信号量组中对应的序号，0～sem_nums-1
                short sem_op;   //信号量在一次操作中需要改变的数据，通常是两个数，
                                //一个是-1，即P（等待）操作， 
                                //一个是+1，即V（发送信号）操作。 
                short sem_flg; //通常为SEM_UNDO,使操作系统跟踪信号量， 
                              //并在进程没有释放该信号量而终止时，操作系统释放信号量 
        	}; 
     		'''
     	* nops: 信号量的个数
     * return:
     	* 成功返回0
     	* 失败返回-1
    */
    
    int semctl(int semid, int sem_num, int cmd, ...);
    /**
     * 控制信号量的相关信息
     * param:
     	* semid: semget()返回的信号量集ID
     	* sem_num: 表示当前信号量集中的哪一个信号
     	* cmd: 操作
     		** SETVAL: 用来把信号量初始化为一个已知的值，这个值可以通过设置第四个参数union semun中的val成员设置，其作用是在信号量第一次使用前对它进行设置
     		** IPC_RMID: 用于删除一个已经无需继续使用的信号量标识符，删除操作不需要缺省参数，只需要三个参数
     	* ...: 缺省参数，一般定义为union semun arg;
     		'''
     		union semun
            { 
                int val;  //使用的值
                struct semid_ds *buf;  //IPC_STAT、IPC_SET 使用的缓存区
                unsigned short *arry;  //GETALL,SETALL 使用的数组
                struct seminfo *__buf; // IPC_INFO(Linux特有) 使用的缓存区
            };
     		'''
     * return:
     	* 成功返回0
     	* 失败返回-1
    */
    ```

- **代码实现**

```C
#include<stdio.h>
#include<stdlib.h>
#include<sys/stat.h>
#include<time.h>

// 联合体，用于semctl初始化
union semun
{
    int val; //for SETVAL
    struct semid_ds buf;
    unsigned short *array;
};

// 初始化信号量
int init_sem(int sem_id, int value)
{
    union semun tmp;
    tmp.val = value;
    if(semctl(sem_id, 0, SETVAL, tmp) == -1)
    {
        perror("Init Semaphore Error");
        return -1;
    }
    return 0;
}

// P操作:
//    若信号量值为1，获取资源并将信号量值-1 
//    若信号量值为0，进程挂起等待
int sem_p(int sem_id)
{
    struct sembuf sbuf;
    sbuf.sem_num = 0; /序号/
    sbuf.sem_op = -1; /P操作/
    sbuf.sem_flg = SEM_UNDO;

    if(semop(sem_id, &sbuf, 1) == -1)
    {
        perror("P operation Error");
        return -1;
    }
    return 0;
}

// V操作：
//    释放资源并将信号量值+1
//    如果有进程正在挂起等待，则唤醒它们
int sem_v(int sem_id)
{
    struct sembuf sbuf;
    sbuf.sem_num = 0; /序号/
    sbuf.sem_op = 1;  /V操作/
    sbuf.sem_flg = SEM_UNDO;

    if(semop(sem_id, &sbuf, 1) == -1)
    {
        perror("V operation Error");
        return -1;
    }
    return 0;
}

// 删除信号量集
int del_sem(int sem_id)
{
    union semun tmp;
    if(semctl(sem_id, 0, IPC_RMID, tmp) == -1)
    {
        perror("Delete Semaphore Error");
        return -1;
    }
    return 0;
}

int main()
{
    int sem_id;  // 信号量集ID
    key_t key;  
    pid_t pid;

    // 获取key值
    if((key = ftok(".", 'z')) < 0)
    {
        perror("ftok error");
        exit(1);
    }

    // 创建信号量集，其中只有一个信号量
    if((sem_id = semget(key, 1, IPC_CREAT|0666)) == -1)
    {
        perror("semget error");
        exit(1);
    }

    // 初始化：初值设为0资源被占用
    init_sem(sem_id, 0);

    if((pid = fork()) == -1)
        perror("Fork Error");
    else if(pid == 0) //子进程
    {
        sleep(2);
        printf("Process child: pid=%d\n", getpid());
        sem_v(sem_id);  //释放资源
    }
    else  //父进程//
    {
        sem_p(sem_id);   //等待资源
        printf("Process father: pid=%d\n", getpid());
        sem_v(sem_id);   //释放资源
        del_sem(sem_id); //删除信号量集
    }
    return 0;
}
```



##### 信号 -- signal

- **概述**

​	**信号**是提供处理**异步事件机制**的软件中断。

​	**通过发送指定信号来通知进程某个异步事件的发送，以迫使进程执行信号处理程序。信号处理完毕后，被中断进程将恢复执行**。

​	也就是，A给B发送信号，B收到信号之前执行自己的代码，收到信号后，不管执行到程序的什么位置，都要暂停运行，去处理信号，处理完毕再继续执行。

​	这些事情可以来自系统外部--例如系统产生中断符（通常Ctrl-C），或者来自程序或内核内部的活动，例如进程执行除以零的代码。

​	作为一种进程间通信的基本形式，进程也可以给另一个进程发送信号。

- **信号产生**

  - 按键产生，如：Ctrl+c、Ctrl+z、Ctrl+\
  - 系统调用产生，如：kill、raise、abort
  - 软件条件产生，如：定时器alarm
  - 硬件异常产生，如：非法访问内存(段错误)、除0(浮点数例外)、内存对齐出错(总线错误)
  - 命令产生，如：kill命令

- **进程间使用信号通信**

  - signal()函数注册一个信号捕捉函数

  ```C
  typedef void (*sighandler_t)(int);
  sighandler_t signal(int signum, sighandler_t handler);
  ```

  - **sigaction** 函数修改信号处理动作（通常在Linux用其来注册一个信号的捕捉函数）

  ```C
  int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);  //成功：0；失败：-1，设置errno
  ```

  - **kill** 函数: 给指定进程发送指定信号(不一定杀死)

  ```C
  int kill(pid_t pid, int sig);
  /**
   * pid: 进程号
   * sig: 信号量，当为0时（即空信号），实际不发送任何信号，但照常进行错误检查。因此，可用于检查目标进程是否存在，以及当前进程是否具有向目标发送信号的权限
  */
  ```

  - **raise** 函数：给当前进程发送指定信号(自己给自己发)

  ```C
  //raise(signo) == kill(getpid(), signo);
  int raise(int sig);// 成功：0，失败非0值
  ```

  - **abort** 函数：给自己发送异常终止信号SIGALRM信号。进程收到该信号，默认动作终止。

  ```C
  void abort(void); 该函数无返回
  ```

  - __alarm__函数：设置定时器(闹钟)。在指定seconds后，内核会给当前进程发送SIGALRM信号。进程收到该信号，默认动作终止。

  ```C
  unsigned int alarm(unsigned int seconds); 返回0或剩余的秒数，无失败。
  ```

  - **setalarm** 函数：设置定时器(闹钟)。 可代替alarm函数。精度微秒us，可以实现周期定时。

  ```C
  int setitimer(int which, const struct itimerval *new_value, struct itimerval *old_value);//成功：0；失败：-1，设置errno
  
  /**
   * which：指定定时方式
  	** 自然定时：ITIMER_REAL → 14）SIGLARM 计算自然时间
  	** 虚拟空间计时(用户空间)：ITIMER_VIRTUAL → 26）SIGVTALRM 只计算进程占用cpu的时间
  	** 运行时计时(用户+内核)：ITIMER_PROF → 27）SIGPROF 计算占用cpu及执行系统调用的时间
  
  */
  ```

  

##### Socket套接字编程 -- socket

- **概念**

  之前的各种通信机制如：pipe，FIFO，message queue，signal ，semaphore ，shared memory 都**局限于同一台计算机上的进程间通信**

  套接字允许进程与不同计算机或者同一计算机上的其它进程通信

  网络上的两个程序通过一个双向的通信连接实现数据的交换，这个连接的一端称为一个socket

  建立网络通信连接至少要一对端口号(socket)。socket本质是编程接口(API)，对TCP/IP的封装，TCP/IP也要提供可供程序员做网络开发所用的接口，这就是Socket编程接口

  

- **TCP传输方式**

TCP是一个**面向连接的**传输层协议，在数据发送之前（即进程通信之前)，必须先建立连接。通信完毕后，必须关闭连接。基于TCP传输协议的服务器与客户机间的通信工作流程如下图：

![image-20231101153920262](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231101153920262.png)

- **函数原型**

```c
int socket(int domain, int type, int protocol);
/**
	* socket函数对应于普通文件的打开操作，返回的是一个socket描述符(socket descriptor)，唯一标识一个socket
	* param:
		* domain: 协议族，常见的有AF_INET、AF_INET6、AF_LOCAL、AF_ROUTE，协议族决定了socket地址的类型，在通信中必须采用对应的地址格式要求
		* type: 执行socket类型，常用的socket类型有，SOCK_STREAM、SOCK_DGRAM、SOCK_RAW、SOCK_PACKET、SOCK_SEQPACKET等
		* protocol: 协议，常用的协议有，IPPROTO_TCP、IPPTOTO_UDP、IPPROTO_SCTP、IPPROTO_TIPC等，它们分别对应TCP传输协议、UDP传输协议、STCP传输协议、TIPC传输协议
	* return:
		* socket描述符
*/
```

```C
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
/**
	* bind()函数把一个地址族中的特定地址赋给socket。
	* param:
		* sockfd: socket描述符
		* addr: const struct sockaddr *指针，指向要绑定给sockfd的协议地址。
		* addrlen: 地址的长度
	* return:
		* socket描述符
*/
```

```C
int listen(int sockfd, int backlog);

/**
	* 调用listen()来监听socket
	* param:
		* sockfd: socket描述符
		* backlog: 最大连接个数
	* return:
		* 
*/


int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
/**
	* 如果客户端这时调用connect()发出连接请求，服务器端就会接收到这个请求。
	* param:
		* sockfd: socket描述符
		* addr: 服务器的socket地址
		* addrlen: 地址的长度
	* return:
		* 
*/

```

```C
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
/**
	* TCP服务器监听到连接请求之后，就会调用accept()函数取接收请求，这样连接就建立好了。之后就可以开始网络I/O操作了，即类同于普通文件的读写I/O操作。
	* param:
		* sockfd: socket描述符
		* addr: 服务器的socket地址
		* addrlen: 地址的长度
	* return:
		* 其返回值是由内核自动生成的一个全新的描述字，代表与返回客户的TCP连接。
*/
```

```C
#include <unistd.h>       
ssize_t read(int fd, void *buf, size_t count);       
ssize_t write(int fd, const void *buf, size_t count);       

#include <sys/types.h>       
#include <sys/socket.h>       
ssize_t send(int sockfd, const void *buf, size_t len, int flags);       
ssize_t recv(int sockfd, void *buf, size_t len, int flags);       

ssize_t sendto(int sockfd, const void *buf, size_t len, int flags,                      const struct sockaddr *dest_addr, socklen_t addrlen);       
ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags,                        struct sockaddr *src_addr, socklen_t *addrlen);       

ssize_t sendmsg(int sockfd, const struct msghdr *msg, int flags);       
ssize_t recvmsg(int sockfd, struct msghdr *msg, int flags);
```



```C
#include <unistd.h>
int close(int fd);
/**
	* 完成读写操作后就要关闭相应的socket描述符
	* param:
		* fd: socket描述符或者文件描述符
	* return:
		* 
*/
```



- **函数使用**

服务端TCPServer.cpp

```c++
#include <stdio.h>
#include <winsock2.h>
#pragma comment (lib, "ws2_32.lib")  //加载 ws2_32.dll

#define BUF_SIZE 100

int main()
{
    WSADATA wsaData;
    WSAStartup(MAKEWORD(2, 2), &wsaData);

    //创建套接字
    SOCKET servSock = socket(AF_INET, SOCK_STREAM, 0);//【socket】

    //绑定套接字
    sockaddr_in sockAddr;
    memset(&sockAddr, 0, sizeof(sockAddr));  //每个字节都用0填充
    sockAddr.sin_family = PF_INET;  //使用IPv4地址
    sockAddr.sin_addr.s_addr = inet_addr("127.0.0.1");  //具体的IP地址
    sockAddr.sin_port = htons(1234);  //端口
    bind(servSock, (SOCKADDR*)&sockAddr, sizeof(SOCKADDR));//【bind】

    //进入监听状态
    listen(servSock, 20);//【listen】

    //接收客户端请求
    SOCKADDR clntAddr;
    int nSize = sizeof(SOCKADDR);
    char buffer[BUF_SIZE] = { 0 };  //缓冲区
    while (1) 
    {
        SOCKET clntSock = accept(servSock, (SOCKADDR*)&clntAddr, &nSize);//【accept】
        int strLen = recv(clntSock, buffer, BUF_SIZE, 0);  //接收客户端发来的数据 【recv】
        send(clntSock, buffer, strLen, 0);  //将数据原样返回【send】

        closesocket(clntSock);  //关闭套接字
        memset(buffer, 0, BUF_SIZE);  //重置缓冲区
    }

    //关闭套接字
    closesocket(servSock);

    //终止 DLL 的使用
    WSACleanup();

    return 0;
}
```



客户端TCPClient.cpp

```c++
#include <stdio.h>
#include <WinSock2.h>
#include <windows.h>
#pragma comment(lib, "ws2_32.lib")  //加载 ws2_32.dll

#define BUF_SIZE 100

int main() {
    //初始化DLL
    WSADATA wsaData;
    WSAStartup(MAKEWORD(2, 2), &wsaData);

    //向服务器发起请求
    sockaddr_in sockAddr;
    memset(&sockAddr, 0, sizeof(sockAddr));  //每个字节都用0填充
    sockAddr.sin_family = PF_INET;
    sockAddr.sin_addr.s_addr = inet_addr("127.0.0.1");
    sockAddr.sin_port = htons(1234);

    char bufSend[BUF_SIZE] = { 0 };
    char bufRecv[BUF_SIZE] = { 0 };

    while (1) 
    {
        //创建套接字
        SOCKET sock = socket(PF_INET, SOCK_STREAM, IPPROTO_TCP);//【socket】
        connect(sock, (SOCKADDR*)&sockAddr, sizeof(SOCKADDR));//【connect】
        //获取用户输入的字符串并发送给服务器
        printf("Input a string: ");
        gets_s(bufSend);
        send(sock, bufSend, strlen(bufSend), 0);//【send】
        //接收服务器传回的数据
        recv(sock, bufRecv, BUF_SIZE, 0);//【recv】
        //输出接收到的数据
        printf("Message form server: %s\n", bufRecv);

        memset(bufSend, 0, BUF_SIZE);  //重置缓冲区
        memset(bufRecv, 0, BUF_SIZE);  //重置缓冲区
        closesocket(sock);  //关闭套接字【close】
    }

    WSACleanup();  //终止使用 DLL
    return 0;
}
```



### 8 僵尸进程

##### 什么是僵尸进程

在 Linux 环境中，一般通过 `fork` 函数来创建子进程的。创建完毕之后，父子进程独立运行，父进程无法预知子进程什么时候结束。

通常情况下，子进程退出后，父进程会使用 `wait` 或 `waitpid` 函数进行回收子进程的资源，并获得子进程的终止状态。

但是，如果**父进程先于子进程结束**，则**子进程成为孤儿进程**。孤儿进程将被 init 进程（进程号为1）领养，并由 init 进程对孤儿进程完成状态收集工作。

而如果**子进程先于父进程退出**，**同时父进程太忙了，无瑕回收子进程的资源**，子进程残留资源（PCB）存放于内核中，变成僵尸（Zombie）进程



##### 如何查看linux系统上的僵尸进程，如何统计有多少僵尸进程？

- ps命令查看僵尸进程，关键词<defunct>

```shell
#ps -ef | grep defunct
```

![image-20231101195326472](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231101195326472.png)

- top命令也记录了僵尸进程（0 zombie）

```shell
top
```

![image-20231101195534403](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231101195534403.png)



##### 孤儿进程和僵尸进程的区别

　**孤儿进程**

一个父进程退出，而它的一个或多个子进程还在运行，那么那些子进程将成为孤儿进程。孤儿进程将被init进程(进程号为1)所收养，并由init进程对它们完成状态收集工作。

　**僵尸进程**

一个进程使用fork创建子进程，如果子进程退出，而父进程并没有调用wait或waitpid获取子进程的状态信息，那么子进程的进程描述符仍然保存在系统中。这种进程称之为僵尸进程。



##### 产生僵尸进程的栗子

子进程执行的程序

```C
#include <stdio.h> 
int main()  
{  
     int i = 0;  
     for (i = 0 ; i < 10; i++)  
            {  
                    printf ("child time %d\n", i+1);  
                    sleep (1);  
            }  
     return 0;  
}  
```

父进程执行的程序

```C
#include <stdio.h> 
#include <unistd.h> 
#include <sys/types.h> 
#include <sys/wait.h> 
int main(){  
     int pid = fork ();  
     if (pid == 0){
         system ("./test_prog");
         _exit (0);
     }else{  
         int i = 0;  
         /* 
         int status = 0; 
         while (!waitpid(pid, &status, WNOHANG)){
            printf ("father waiting%d\n", ++i);
            sleep (1);
         }*/ 
         while (1){
             printf ("father waiting over%d\n", ++i);
             sleep (1);
         }  
         return 0;
     } 
}  
```

执行`./father`,当子进程退出后，由于父进程没有对它的退出进行关注，会出现僵尸进程



##### 如何杀死一个僵尸进程

僵尸进程其实已经就是退出的进程，因此无法再利用kill命令杀死僵尸进程。

僵尸进程的罪魁祸首是父进程没有回收它的资源，那我们可以想办法让其它进程去回收僵尸进程的资源，这个进程就是 init 进程。

因此，我们可以直接杀死父进程，init 进程就会很善良地把那些僵尸进程领养过来，并合理的回收它们的资源，那些僵尸进程就得到了妥善的处理了。

- 首先通过如下命令查看僵尸进程的PID

```shell
ps -ef | grep defunt
```

- 然后通过如下命令查看系统内进程的树状图

```shell
pstree -psn
```

![image-20231101202757402](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231101202757402.png)

- 然后通过kill命令干掉父进程

```shell
kill -9 <parent_process_ID>
```

- 最后可以再次通过ps或者top命令验证僵尸进程是否还存在

##### 如何避免僵尸进程

- 父进程通过`wait`和`waitpid`等函数等待子进程结束，这会导致父进程挂起。

​		如上述栗子中的注释部分

- 如果父进程很忙，那么可以用`signal`函数为SIGCHLD安装handler。在子进程结束后，父进程会收到该信号，可以在handler中调用wait回收。

```C
signal(SIGCHLD,sig_child);


static void sig_child(int signo)
{
      pid_t pid;
      int stat;
      //处理僵尸进程
      while ((pid = waitpid(-1, &stat, WNOHANG)) >0)
             printf("child %d terminated.\n", pid);
}
```



- 如果父进程不关心子进程什么时候结束，那么可以用**signal(SIGCLD, SIG_IGN)或signal（SIGCHLD, SIG_IGN）**通知内核，自己对子进程的结束不感兴趣，那么子进程结束后，内核会回收，并不再给父进程发送信号

-  fork两次，父进程fork一个子进程，然后继续工作，子进程fork一个孙进程后退出，那么孙进程被init接管，孙进程结束后，init会回收。不过子进程的回收还要自己做。



---



### 9 进程调度策略

##### 先来先服务（FCFS）

按照进程到达的顺序分配CPU时间，即先到达的进程先执行，直到该进程执行完成。

优点是简单、公平，无偏向性。缺点是平均等待时间可能较长，尤其对于长时间运行的进程。

```c++
#include <iostream>
#include <queue>

struct Process {
    int id;
    int burst_time;
};

void FCFS(std::queue<Process> processes) {
    int current_time = 0;

    while (!processes.empty()) {
        Process current_process = processes.front();
        processes.pop();

        std::cout << "Running process " << current_process.id << " for " << current_process.burst_time << " units" << std::endl;

        current_time += current_process.burst_time;
    }
}

int main() {
    std::queue<Process> processes;
    processes.push({1, 10});
    processes.push({2, 5});
    processes.push({3, 8});
    FCFS(processes);

    return 0;
}

```

#####  最短作业优先（SJF）

选择具有最短执行时间的进程来运行。

优点是可以减少平均等待时间。缺点是需要预测进程执行时间，对长时间运行的进程不公平。

```c++
#include <iostream>
#include <queue>
#include <algorithm>

struct Process {
    int id;
    int burst_time;
};

bool compare(Process a, Process b) {
    return a.burst_time < b.burst_time;
}

void SJF(std::queue<Process> processes) {
    std::vector<Process> process_list;
    while (!processes.empty()) {
        process_list.push_back(processes.front());
        processes.pop();
    }

    std::sort(process_list.begin(), process_list.end(), compare);

    for (const Process& process : process_list) {
        std::cout << "Running process " << process.id << " for " << process.burst_time << " units" << std::endl;
    }
}

int main() {
    std::queue<Process> processes;
    processes.push({1, 10});
    processes.push({2, 5});
    processes.push({3, 8});
    SJF(processes);

    return 0;
}

```



#####  优先级调度

使用每个进程的优先级来选择下一个要运行的进程。优先级可以是静态的或动态的，并根据不同的算法进行调整。优先级较高的进程会优先获得CPU时间。

```C++
#include <iostream>
#include <queue>

struct Process {
    int id;
    int burst_time;
    int priority;
};

struct ComparePriority {
    bool operator()(Process const& p1, Process const& p2) {
        return p1.priority > p2.priority;
    }
};

void Priority(std::priority_queue<Process, std::vector<Process>, ComparePriority> processes) {
    while (!processes.empty()) {
        Process current_process = processes.top();
        processes.pop();

        std::cout << "Running process " << current_process.id << " with priority " << current_process.priority
                  << " for " << current_process.burst_time << " units" << std::endl;
    }
}

int main() {
    std::priority_queue<Process, std::vector<Process>, ComparePriority> processes;
    processes.push({1, 10, 2});
    processes.push({2, 5, 1});
    processes.push({3, 8, 3});
    Priority(processes);

    return 0;
}
```



#####  时间片轮转（RR）

调度策略将CPU时间分成固定长度的时间片，每个进程按照顺序获取一个时间片。当时间片用完后，当前进程被挂起并排队等待。

优点是公平，允许每个进程有一定的执行时间。缺点是响应时间较长，且不适用于长时间运行的进程。

```c++
#include <iostream>
#include <queue>

struct Process {
    int id;
    int burst_time;
};

void RoundRobin(std::queue<Process> processes, int time_slice) {
    while (!processes.empty()) {
        Process current_process = processes.front();
        processes.pop();

        if (current_process.burst_time > time_slice) {
            std::cout << "Running process " << current_process.id << " for " << time_slice << " units" << std::endl;
            current_process.burst_time -= time_slice;
            processes.push(current_process);
        } else {
            std::cout << "Running process " << current_process.id << " for " << current_process.burst_time << " units" << std::endl;
        }
    }
}

int main() {
    std::queue<Process> processes;
    processes.push({1, 10});
    processes.push({2, 15});
    processes.push({3, 5});
    RoundRobin(processes, 8);

    return 0;
}
```



#####  最短剩余事件优先（SRTF）

是一种基于抢占的进程调度策略。在这种策略下，CPU总是分配给剩余执行时间最短的进程。如果新进程到达并且其剩余运行时间比当前正在运行的进程的剩余时间短，那么当前进程将被中断，而新进程将被赋予CPU。

优点是能确保更快的任务能尽快得到处理，而不会因为长时间任务而过长的等待；缺点是可能会产生“饿死”的问题，如果多个短任务连续到达，一个长任务可能会一直得不到处理。



---



### 10 关于死锁

##### 什么是死锁

死锁是指在多进程或多线程系统中，各个进程或线程因竞争资源而陷入互相等待的状态，无法继续执行。死锁通常涉及多个进程或线程，每个进程/线程都在等待另一个进程/线程释放其所需的资源，导致所有进程/线程都无法继续执行。

**死锁通常包括以下必要条件：**

- **互斥条件**：资源只能由一个进程或线程占用，其他进程或线程需要等待释放。（独一份）

<img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231106204150187.png" alt="image-20231106204150187" style="zoom:50%;" />

- **请求和保持条件**：进程持有至少一个资源，并且在请求其他资源时继续保持已拥有的资源。（吃着碗里的，想着锅里的）

<img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231106204314546.png" alt="image-20231106204314546" style="zoom:50%;" />

- **不可剥夺条件**：资源不能被强行剥夺，只能由持有资源的进程主动释放。（护食）

<img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231106204420625.png" alt="image-20231106204420625" style="zoom:50%;" />

- **循环等待条件**：存在一组进程，每个进程都等待下一个进程所拥有的资源。

<img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231106204439250.png" alt="image-20231106204439250" style="zoom:50%;" />

**模拟死锁问题的产生**

- 首先，先创建 2 个线程，分别为线程 A 和 线程 B，然后有两个互斥锁，分别是 mutex_A 和 mutex_B，代码如下：

```c++
pthread_mutex_t mutex_A = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t mutex_B = PTHREAD_MUTEX_INITIALIZER;

int main()
{
    pthread_t tidA, tidB;

    //创建两个线程
    pthread_create(&tidA, NULL, threadA_proc, NULL);
    pthread_create(&tidB, NULL, threadB_proc, NULL);

    pthread_join(tidA, NULL);
    pthread_join(tidB, NULL);

    printf("exit\n");

    return 0;
}
```

A函数：

```c++
//线程函数 A
/*
先获取互斥锁 A，然后睡眠 1 秒；
再获取互斥锁 B，然后释放互斥锁 B；
最后释放互斥锁 A；
*/
void *threadA_proc(void *data)
{
    printf("thread A waiting get ResourceA \n");
    pthread_mutex_lock(&mutex_A);
    printf("thread A got ResourceA \n");

    sleep(1);

    printf("thread A waiting get ResourceB \n");
    pthread_mutex_lock(&mutex_B);
    printf("thread A got ResourceB \n");

    pthread_mutex_unlock(&mutex_B);
    pthread_mutex_unlock(&mutex_A);
    return (void *)0;
}
```



```c++
//线程函数 B
/**
先获取互斥锁 B，然后睡眠 1 秒；
再获取互斥锁 A，然后释放互斥锁 A；
最后释放互斥锁 B；
*/
void *threadB_proc(void *data)
{
    printf("thread B waiting get ResourceB \n");
    pthread_mutex_lock(&mutex_B);
    printf("thread B got ResourceB \n");

    sleep(1);

    printf("thread B waiting  get ResourceA \n");
    pthread_mutex_lock(&mutex_A);
    printf("thread B got ResourceA \n");

    pthread_mutex_unlock(&mutex_A);
    pthread_mutex_unlock(&mutex_B);
    return (void *)0;
}
```

运行结果：

```shell
thread B waiting get ResourceB 
thread B got ResourceB 
thread A waiting get ResourceA 
thread A got ResourceA 
thread B waiting get ResourceA 
thread A waiting get ResourceB 
// 阻塞中...
```

可以看到线程 B 在等待互斥锁 A 的释放，线程 A 在等待互斥锁 B 的释放，双方都在等待对方资源的释放，很明显，产生了死锁问题。



##### 如何解决死锁

主要有一下三种方法：

- 死锁防止
- 死锁避免
- 死锁检测和恢复



---



###### 死锁防止

产生死锁的四个必要条件是：互斥条件、持有并等待条件、不可剥夺条件、循环等待条件。那么避免死锁问题就只需要破环其中一个条件就可以。

其中最容易破坏的就是循环等待这个条件，**使用资源有序分配法，来破环循环等待条件**。

也就是说，线程 A 和 线程 B 获取资源的顺序要一样，当线程 A 是先尝试获取资源 A，然后尝试获取资源 B 的时候，线程 B 同样也是先尝试获取资源 A，然后尝试获取资源 B。也就是说，线程 A 和 线程 B 总是以**相同的顺序**申请自己想要的资源。

**破坏互斥条件**

使资源同时访问而非互斥使用，就没有进程会阻塞在资源上，从而不发生死锁。

> 只读数据文件、磁盘等软硬件资源均可采用这种办法管理；
> 但是许多资源是独占性资源，如可写文件、键盘等只能互斥的占有；
> 所以这种做法在许多场合是不适用的。

**破坏占有和等待条件**

采用静态分配的方式，静态分配的方式是指进程必须在执行之前就申请需要的全部资源，且直至所要的资源全部得到满足后才开始执行。

> 实现简单，但是严重的减低了资源利用率。
> 因为在每个进程占有的资源中，有些资源在运行后期使用，有些资源在例外情况下才被使用，可能会造成进程占有一些几乎用不到的资源，而使其他想使用这些资源的进程等待。

**破坏不剥夺条件**

剥夺调度能够防止死锁，但是只适用于内存和处理器资源。

> 方法一：占有资源的进程若要申请新资源，必须主动释放已占有资源，若需要此资源，应该向系统重新申请。
>
> 方法二：资源分配管理程序为进程分配新资源时，若有则分配；否则将剥夺此进程已占有的全部资源，并让进程进入等待资源状态，资源充足后再唤醒它重新申请所有所需资源。

**破坏循环等待条件**

给系统的所有资源编号，规定进程请求所需资源的顺序必须按照资源的编号依次进行。

> 采用层次分配策略，将系统中所有的资源排列到不同层次中
>
> - 一个进程得到某层的一个资源后，只能申请较高一层的资源
> - 当进程释放某层的一个资源时，必须先释放所占有的较高层的资源
> - 当进程获得某层的一个资源时，如果想申请同层的另一个资源，必须先释放此层中已占有的资源



###### 死锁避免

- **安全状态**

<img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231106210406551.png" alt="image-20231106210406551" style="zoom:50%;" />

图 a 的第二列 Has 表示已拥有的资源数，第三列 Max 表示总共需要的资源数，Free 表示还有可以使用的资源数。

从图 a 开始出发，先让 B 拥有所需的所有资源（图 b）；

运行结束后释放 B，此时 Free 变为 5（图 c）；

接着以同样的方式运行 C 和 A，使得所有进程都能成功运行；

因此可以称图 a 所示的状态时安全的。

- **单个资源的银行家算法**

与安全状态类似。

一个小城镇的银行家，他向一群客户分别承诺了一定的贷款额度，算法要做的是判断对请求的满足是否会进入不安全状态，如果是，就拒绝请求；否则予以分配。

<img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231106210555073.png" alt="image-20231106210555073" style="zoom:50%;" />

- **多个资源的银行家算法**

<img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231106210831593.png" alt="image-20231106210831593" style="zoom:50%;" />

上图中有五个进程，四个资源。

左边的图表示已经分配的资源，右边的图表示还需要分配的资源。

最右边的 E、P 以及 A 分别表示：总资源、已分配资源以及可用资源。

注意这三个为向量，而不是具体数值，例如 A=(1020)，表示 4 个资源分别还剩下 1/0/2/0。

检查一个状态是否安全的算法如下：

- 查找右边的矩阵是否存在一行小于等于向量 A。如果不存在这样的行，那么系统将会发生死锁，状态是不安全的。
- 假若找到这样一行，将该进程标记为终止，并将其已分配资源加到 A 中。
- 重复以上两步，直到所有进程都标记为终止，则状态时安全的。

如果一个状态不是安全的，需要拒绝进入这个状态。



---

###### 死锁检测和恢复

> 对资源的分配加以适当限制可防止或避免死锁发生，但不利于进程对系统资源的充分共享。

- 如果进程 - 资源分配图中无环路，此时系统没有发生死锁。

- 如果进程 - 资源分配图中有环路，则可分为以下两种情况：

  - 每种资源类中仅有一个资源，则系统发生了死锁。此时，环路是系统发生死锁的**充分必要条件**，环路中的进程就是死锁进程。

  - 每种资源类中有多个资源，则环路的存在只是产生死锁的**必要不充分条件**，系统未必会发生死锁。

- **死锁检测**

  - **每种资源类中仅有一个资源的死锁检测**

  <img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231106211938401.png" alt="image-20231106211938401" style="zoom:50%;" />

  > ​	上图为资源分配图，其中方框表示资源，圆圈表示进程。
  >
  > ​	资源指向进程表示该资源已经分配给该进程，进程指向资源表示进程请求获取该资源。

  ​		图 a 可以抽取出环，如图 b，它满足了环路等待条件，因此会发生死锁。

  **死锁检测算法：**		

  每种类型一个资源的死锁检测算法是通过检测有向图**是否存在环**来实现，从一个节点出发进行深度优先搜索，对访问过的节点进行标记，如果访问了已经标记的节点，就表示有向图存在环，也就是检测到死锁的发生。

  - **每种资源类中有多个资源的死锁检测**

  <img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231106212202912.png" alt="image-20231106212202912" style="zoom:50%;" />

​		每个资源类用一个方框表示，方框中的圆点表示此资源类中的各个资源；

​		每个进程用一个圆圈来表示，用有向边表示进程申请资源和资源分配情况。

​		约定方框→圆圈表示资源分配，圆圈→方框表示申请资源。

​		这种情况下，图3-6 发生了死锁，而图3-7没有发生死锁。

> 在这种情况下，系统处于死锁状态的充分条件是：当且仅当此状态的进程-资源分配图是不可完全简化的，这一充分条件称为死锁定理

​		 **死锁检测算法**：

<img src="C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231106212625958.png" alt="image-20231106212625958" style="zoom:50%;" />

​		上图中，有三个进程四个资源，每个数据代表的含义如下：

​		E 向量：资源总量

​		A 向量：资源剩余量

​		C 矩阵：每个进程所拥有的资源数量，每一行都代表一个进程拥有资源的数量

​		R 矩阵：每个进程请求的资源数量

进程 P1 和 P2 所请求的资源都得不到满足，只有进程 P3 可以，让 P3 执行，之后释放 P3 拥有的资源，此时 A = (2 2 2 0)。P2 可以执行，执行后释放 P2 拥有的资源，A = (4 2 2 1) 。P1 也可以执行。所有进程都可以顺利执行，没有死锁。

**算法总结如下：**

每个进程最开始时都不被标记，执行过程有可能被标记。当算法结束时，任何没有被标记的进程都是死锁进程。

1. 寻找一个没有标记的进程 Pi，它所请求的资源小于等于 A。
2. 如果找到了这样一个进程，那么将 C 矩阵的第 i 行向量加到 A 中，标记该进程，并转回 1。
3. 如果没有这样一个进程，算法终止。

- **死锁恢复**

  - **资源剥夺法**
    剥夺陷于死锁的进程所占用的资源，但并不撤销此进程，直至死锁解除。

  - **进程回退法**
    根据系统保存的检查点让所有的进程回退，直到足以解除死锁，这种措施要求系统建立保存检查点、回退及重启机制。

  - **进程撤销法**

    - 撤销陷入死锁的所有进程，解除死锁，继续运行。

    - 逐个撤销陷入死锁的进程，回收其资源并重新分配，直至死锁解除。

      > 可选择符合下面条件之一的先撤销： 
      >
      > 1. CPU消耗时间最少者 
      >
      > 2. 产生的输出量最小者
      > 3. 预计剩余执行时间最长者
      > 4. 分得的资源数量最少然后优先级最低者

  - **系统重启法**
    结束所有进程的执行并重新启动操作系统。这种方法很简单，但先前的工作全部作废，损失很大。



---

### 11 锁机制

##### 互斥锁

- **概述**

mutex，用于保证在任何时刻，都只能有一个线程访问该对象。当获取锁操作失败时，线程会进入睡眠，等待锁释放时被唤醒。

- **适用情况**

当线程等待锁的时间较长，或者竞争锁的线程数较多时，使用互斥锁较为合适。

- **特点**

  - 多个线程访问共享数据的时候是串行的

  - 多线程共享一个互斥量，然后线程之间去竞争，得到锁的线程可以进入临界区执行代码。

  - mutex是睡眠等待（sleep waiting）类型的锁，当线程抢互斥锁失败的时候，线程会陷入休眠。

- **缺点**

缺点就是效率低，休眠唤醒会消耗一点时间

- **函数接口**

```c++
// 声明一个互斥量    
pthread_mutex_t mtx;
// 初始化 
pthread_mutex_init(&mtx, NULL);
// 加锁  
pthread_mutex_lock(&mtx);
// 解锁 
pthread_mutex_unlock(&mtx);
// 销毁
pthread_mutex_destroy(&mtx);
```

- **非阻塞式锁**

pthread_mutex_trylock()用于以非阻塞的模式来请求互斥量。当加锁失败的时候，并不会阻塞进程，而是继续进行其他操作

```c++
ret = pthread_mutex_trylock(&mtx);
if (0 == ret) { // 加锁成功
    ... 
    pthread_mutex_unlock(&mtx);
} else if(EBUSY == ret){ // 锁正在被使用;
    ... 
}
```



##### 条件变量

> 条件变量不是锁，是一种线程间通信方式，并且几乎总和互斥量一起适用。
>
> 互斥锁用于保护临界区代码，确保同一时间只有一个线程可以进入临界区。而条件变量用于线程之间的通信，使得线程可以等待某个条件为真时再继续执行。



- **代码示例**

在这个示例中，生产者线程通过互斥锁保护共享资源，在每次生产一个资源后，通过条件变量发送信号通知消费者线程。同时，消费者线程在消费资源之前会等待条件变量的信号，一旦收到信号后进行消费。

需要注意的是，互斥锁和条件变量必须在使用之前进行初始化，并在使用完成后进行销毁。

```c++
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define NUM_THREADS 5

pthread_mutex_t mutex;
pthread_cond_t condition;
int shared_resource = 0;

void* producer(void* arg) {
    for (int i = 0; i < 10; i++) {
        pthread_mutex_lock(&mutex);
        shared_resource += 1;

        printf("Producer: shared_resource = %d\n", shared_resource);

        pthread_cond_signal(&condition);  // 唤醒等待的消费者线程
        pthread_mutex_unlock(&mutex);

        // 生产者产生一个资源后，休眠一段时间
        sleep(1);
    }

    pthread_exit(NULL);
}

void* consumer(void* arg) {
    int value = 0;

    for (int i = 0; i < 10; i++) {
        pthread_mutex_lock(&mutex);

        while (shared_resource == 0) {
            pthread_cond_wait(&condition, &mutex);  // 等待生产者发信号
        }

        value = shared_resource;
        shared_resource = 0;

        printf("Consumer: Consumed value = %d\n", value);

        pthread_mutex_unlock(&mutex);

        // 消费者消耗一个资源后，休眠一段时间
        sleep(1);
    }

    pthread_exit(NULL);
}

int main() {
    pthread_t producer_thread, consumer_thread;

    pthread_mutex_init(&mutex, NULL);
    pthread_cond_init(&condition, NULL);

    pthread_create(&producer_thread, NULL, producer, NULL);
    pthread_create(&consumer_thread, NULL, consumer, NULL);

    pthread_join(producer_thread, NULL);
    pthread_join(consumer_thread, NULL);

    pthread_mutex_destroy(&mutex);
    pthread_cond_destroy(&condition);

    return 0;
}

```















##### 自旋锁

- **概述**

通过循环等待的方式来实现。当线程尝试获取自旋锁时，如果锁已经被其他线程获取，它将循环等待直到锁被释放。

- **适用情况**

当线程等待锁的时间很短且竞争锁的线程数较少时，使用自旋锁可以避免进程上下文切换带来的开销。

- **缺点**

  - **过多占据CPU时间**：如果锁的当前持有者长时间不释放该锁，那么等待者将长时间的占据cpu时间片，导致CPU资源的浪费，因此可以设定一个时间，当锁持有者超过这个时间不释放锁时，等待者会放弃CPU时间片阻塞；

  - **死锁问题**：试想一下，有一个线程连续两次试图获得自旋锁（比如在递归程序中），第一次这个线程获得了该锁，当第二次试图加锁的时候，检测到锁已被占用（其实是被自己占用），那么这时，线程会一直等待自己释放该锁，而不能继续执行，这样就引起了死锁。因此递归程序使用自旋锁应该遵循以下原则：递归程序决不能在持有自旋锁时调用它自己，也决不能在递归调用时试图获得相同的自旋锁。

- **函数接口**

```c++
// 声明一个自旋锁变量
pthread_spinlock_t spinlock;

// 初始化   
pthread_spin_init(&spinlock, 0);

// 加锁  
pthread_spin_lock(&spinlock);

// 解锁 
pthread_spin_unlock(&spinlock);

// 销毁  
pthread_spin_destroy(&spinlock);
```

> pthread_spin_init(&spinlock, 0);函数的第二个参数名为pshared（int类型）。
>
> 表示的是进程间能否共享自旋锁。
>
> 这被称之为Thread Process-Shared Synchronization。
>
> 互斥量的通过该属性也可以把互斥量设置成进程间共享的。
>
> pshared有两个[枚举值](https://www.zhihu.com/search?q=枚举值&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1267625567})：
>
> - **PTHREAD_PROCESS_PRIVATE：仅同进程下读线程可以使用该自旋锁**
> - **PTHREAD_PROCESS_SHARED：不同进程下的线程可以使用该自旋锁**



##### 读写锁

- **概述**

读写锁会对读操作和写操作进行区分：

当读写锁被加了【写锁】时，其他线程对该锁加【读锁】或者【写锁】都会**阻塞**。

当读写锁被加了【读锁】时，其他线程对该锁加【写锁】会**阻塞**，加【读锁】会成功。

- **适用情况**

多读少写

- **函数接口**

```c++
// 声明一个读写锁
pthread_rwlock_t rwlock;

// 初始化一个读写锁
pthread_rwlock_init(&rwlock, NULL);

...
// 在读之前加读锁
pthread_rwlock_rdlock(&rwlock);

//... 共享资源的读操作

// 读完释放锁
pthread_rwlock_unlock(&rwlock);

// 在写之前加写锁
pthread_rwlock_wrlock(&rwlock); 

//... 共享资源的写操作

// 写完释放锁
pthread_rwlock_unlock(&rwlock);

// 销毁读写锁
pthread_rwlock_destroy(&rwlock);

```

- **非阻塞式锁**

读写锁和互斥量一样也有trylock函数，也是以非阻塞地形式来请求锁，不会导致阻塞。

```c++
 pthread_rwlock_tryrdlock(&rwlock)
 pthread_rwlock_trywrlock(&rwlock)
```



##### 悲观锁

> MySQL默认悲观锁
>
> 无论是悲观锁还是乐观锁都是一种设计思想

- **概述**

悲观锁在操作数据时比较悲观，认为别人会同时修改数据。

因此操作数据时直接把数据锁住，直到操作完成后才会释放锁；上锁期间其他人不能修改数据。

- **适用情况**

  - 当读操作较多，写操作较少的情况。

  - 当对共享资源操作的时间较长，且并发冲突概率较高的情况。

- **实现**

悲观锁的实现通常借助于互斥锁（Mutex）、读写锁（Read-Write Lock）或自旋锁（Spin Lock）等。





##### 乐观锁

- **概述**

乐观锁在操作数据时非常乐观，认为别人不会同时修改数据。

因此乐观锁不会上锁，只是在执行更新的时候判断一下在此期间别人是否修改了数据：如果别人修改了数据则放弃操作，否则执行操作。

- **适用情况**

  - 当读操作较少，写操作较多的情况。

  - 当对共享资源操作的时间较短且并发冲突概率较低的情况。

- **特点**

  - 许多CAS的操作是自旋的：如果操作不成功，会一直重试，直到操作成功为止。

  - 乐观锁适用于多读的应用类型，这样可以提高吞吐量



- **实现方式**

  - **CAS（Compare And Swap）**

  > CAS包含以下三个操作：
  >
  > > 需要读写的内存位置(V)
  > >
  > > 进行比较的预期值(A)
  > >
  > > 拟写入的新值(B)

  如果内存位置V的值等于预期的A值，则将该位置更新为新值B，否则不进行任何操作。

  > CAS的缺点：
  >
  > > 1：**ABA问题：**如果一个变量V初次读取的时候是A值，并且在准备赋值的时候检查到它仍然是A值，那我们就能说明它的值没有被其他线程修改过了吗？很明显是不能的，因为在这段时间它的值可能被改为其他值，然后又改回A，那CAS操作就会误认为它从来没有被修改过。这个问题被称为CAS操作的 **"ABA"问题。**
  > >
  > > 2：**循环时间长，CPU开销大**：自旋CAS（也就是不成功就一直循环执行直到成功）如果长时间不成功，会给CPU带来非常大的执行开销。
  > >
  > > 3：**只能保证一个变量的原子性**：CAS 只对单个共享变量有效，当操作涉及跨多个共享变量时 CAS 无效。

  

  

  - **版本号机制**

  一般是在数据表中加上一个数据版本号version字段，表示数据被修改的次数，当数据被修改时，version值会加一。

  当线程A要更新数据值时，在读取数据的同时也会读取version值，在提交更新时，若刚才读取到的version值为当前数据库中的version值相等时才更新，否则重试更新操作，直到更新成功。



##### 可重入锁

可重入锁是一种支持同一线程多次获取的锁，也称为递归锁。当一个线程多次请求同一把可重入锁时，不会发生死锁，而是每次成功获取锁时，都会对锁计数器进行加一操作，每次释放锁时，计数器减一。只有当计数器为零时，锁才会被完全释放，其他线程才能获取该锁。

可重入锁的主要目的是允许同一线程在持有锁的情况下再次获取该锁，而不会发生死锁或其他异常情况。这在某些情况下是非常有用的，特别是在递归调用或函数嵌套的情况下，同一线程可能需要多次获取同一把锁。

在使用可重入锁时，需要注意以下几点：

1. **计数器：** 可重入锁内部维护一个计数器，记录当前线程成功获取锁的次数。每次成功获取锁，计数器加一；每次释放锁，计数器减一。只有当计数器为零时，其他线程才能获取该锁。
2. **释放次数：** 当一个线程成功获取锁多次后，需要相应次数地释放锁，才能真正释放锁资源。否则，其他线程无法获取该锁。
3. **避免死锁：** 可重入锁的设计是为了避免同一线程在获取锁后阻塞自己，从而导致死锁。在使用可重入锁时，仍需注意避免死锁的情况。

**举例**

可重入锁在多线程编程中是一种常见的同步机制，例如在Java中，ReentrantLock就是一个可重入锁的实现。

在C++中，可重入锁通常是通过`std::recursive_mutex`类实现的。`std::recursive_mutex`是C++标准库提供的一种可重入互斥锁，它允许同一线程多次获取锁，而不会导致死锁。

使用`std::recursive_mutex`的基本方法如下：

```c++
#include <iostream>
#include <mutex>

int main() {
    std::recursive_mutex myMutex;

    // 第一次获取锁
    myMutex.lock();
    std::cout << "First lock acquired." << std::endl;

    // 第二次获取锁，仍然成功
    myMutex.lock();
    std::cout << "Second lock acquired." << std::endl;

    // 释放锁
    myMutex.unlock();
    std::cout << "First lock released." << std::endl;

    // 再次释放锁
    myMutex.unlock();
    std::cout << "Second lock released." << std::endl;

    return 0;
}
```

在上面的示例中，`std::recursive_mutex`允许同一线程多次调用`lock`，并通过相应次数的`unlock`释放锁。这就是可重入锁的概念。

需要注意的是，虽然可重入锁允许同一线程多次获取锁，但在实际使用时，仍需小心设计，以避免潜在的死锁和竞争条件。

---



### 12 页面置换算法

> 在程序运行过程中，如果要访问的页面不在内存中，就发生缺页中断从而将该页调入内存中。此时如果内存已无空闲空间，系统必须从内存中调出一个页面到磁盘对换区中来腾出空间。

##### 先进先出(FIFO)算法：

- 思路：置换最先调入内存的页面，即置换在内存中驻留时间最久的页面。
- 实现：按照进入内存的先后次序排列成队列，从队尾进入，从队首删除。
- 特点：实现简单；性能较差，调出的页面可能是经常访问的

##### 最近最少使用（LRU）算法:

- 思路： 置换最近一段时间以来最长时间未访问过的页面。根据程序局部性原理，刚被访问的页面，可能马上又要被访问；而较长时间内没有被访问的页面，可能最近不会被访问。
- 实现：可以通过维护一个链表实现，缺页时，将链表尾部的内存页面删除，访问到一个已经存在的页面时，从链表中删除并置于表头，如果该页面不存在，则直接插入表头
- 特点：可能达到最优的效果，维护这样的访问链表开销比较大



##### 最不常用算法（Least Frequently Used, LFU）

- 思路：缺页时，置换访问次数最少的页面
- 实现：每个页面设置一个访问计数，访问页面时，访问计数加1，缺页时，置换计数最小的页面
- 特点：算法开销大，开始时频繁使用，但以后不使用的页面很难置换



---



### 13 什么是抖动现象

##### 介绍

在操作系统中，抖动现象是指如果分配给进程的存储块数量小于进程所需要的最小值，进程的运行将很频繁地产生缺页中断，这种频率非常高的页面置换现象称为抖动。

在请求分页存储管理中，可能出现这种情况，即对刚被替换出去的页，立即又要被访问。需要将它调入，因无空闲内存又要替换另一页，而后者又是即将被访问的页，于是造成了系统需花费大量的时间忙于进行这种频繁的页面交换，致使系统的实际效率很低，严重导致系统瘫痪，这种现象称为抖动现象。 

抖动现象发生在FIFO页面置换算法中，FIFO并不是一个好的置换算法。

##### 产生原因

进程内存不足，分配页面太少，所以总是缺页。

##### 解决方法

换个好点的页面置换算法，减少进程数，增大内存。



---



### 14 用户空间与内核空间通信方式有哪些？

内核态与用户态的定义： 当进程运行在内核空间时就处于内核态，而进程运行在用户空间时则处于用户态。

对于进程来说，它既有内核空间（与其他进程共享），也有用户空间（进程私有）。

内核空间和用户空间交换数据的方式有很多。

- 用户空间发起的有系统调用、proc、虚拟文件系统等。
- 内核空间主动发起的有get_user/put_user、信号、netlink等。



##### 系统调用

Linux系统下，设备即文件，也因此大部分设备驱动程序都实现了标准的系统接口，如：

​    ● open()，read()，write()， ioctl()， mmap()

​    ● get_user(x，ptr)：在内核中被调用，获取用户空间指定地址的数值并保存到内核变量x中。

​    ● put_user(x，ptr)：在内核中被调用，将内核空间变量x的数值保存到到用户空间指定地址处

​    ● Copy_from_user() / copy_to_user()：主要应用于设备驱动读写函数中，通过系统调用触发

##### procfs(/proc)

  `procfs` 是 **进程文件系统** 的缩写，它本质上是一个伪文件系统，为什么说是**伪**文件系统呢？因为它不占用外部存储空间，只是占用少量的内存，通常是挂载在 `/proc` 目录下。

`/proc`目录下的文件，实际上是一个内核变量。内核就是通过这个目录，以文件的形式展现自己的内部信息，相当于 `/proc` 目录为用户态和内核态之间的交互搭建了一个桥梁，用户态读写 `/proc` 下的文件，就是读写内核相关的配置参数。

比如常见的 `/proc/cpuinfo`、`/proc/meminfo` 和 `/proc/net` 就分别提供了 CPU、内存、网络的相关参数。

   

#####  sysctl

```shell
man sysctl
```

![image-20231108171038791](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231108171038791.png)

它主要是被用来修改内核的运行时参数，换句话说，它可以在内核运行过程中，动态修改内核参数。

它本质上还是用到了文件的读写操作，来完成用户态和内核态的通信。它使用的是 `/proc` 的一个子目录 `/proc/sys`。

和 procfs 的区别在于：

procfs 主要是输出只读数据，而 sysctl 输出的大部分信息是可写的。



例如，我们比较常见的是通过 `cat /proc/sys/net/ipv4/ip_forward` 来获取内核网络层是否允许转发 IP 数据包，

通过 `echo 1 > /proc/sys/net/ipv4/ip_forward` 或者 `sysctl -w net.ipv4.ip_forward=1` 来设置内核网络层允许转发 IP 数据包。

同样的操作，Linux 也提供了文件 `/etc/sysctl.conf` 来让你进行批量修改。



##### sysfs

sysfs 是 Linux 2.6 才引入的一种虚拟文件系统，它的做法也是通过文件 `/sys` 来完成用户态和内核的通信。和 procfs 不同的是，sysfs 是将一些原本在 procfs 中的，关于设备和驱动的部分，独立出来，以 “设备树” 的形式呈现给用户。

sysfs 不仅可以从内核空间读取设备和驱动程序的信息，也可以对设备和驱动进行配置。

```javascript
# ls /sys
block  bus  class  dev  devices  firmware  fs  hypervisor  kernel  module  power
```

可以看到这些文件基本上都跟计算机的设备和驱动等息息相关的。

##### netlink

netlink 是 Linux 用户态与内核态通信最常用的一种方式。Linux kernel 2.6.14 版本才开始支持。

它本质上是一种 socket，常规 socket 使用的标准 API，在它身上同样适用。

比如创建一个 netlink socket，可以调用如下的 socket 函数：

```c
#include <asm/types.h>
#include <sys/socket.h>
#include <linux/netlink.h>

netlink_socket = socket(AF_NETLINK, socket_type, netlink_family);
```

> `net-tools` 工具通过 procfs(/proc) 和 ioctl 系统调用去访问和改变内核网络参数配置，而 `iproute2` 则通过 netlink 套接字接口与内核通信，前者已经被淘汰了，后者逐步成为标准。



##### 内存映像

 mmap共享内存。

Linux通过mmap的把内核中特定部分的内存空间映射到用户级程序的内存空间去，从而提供了用户程序对内存直接访问的能力。该方式尤其适合在那些内核和用户空间需要快速大量交互数据的情况下。



---



### 15 什么是写时复制

##### 概述

**写时复制**（**Copy-on-write**，简称**COW**）是一种计算机程序设计领域的优化策略。

其核心思想是，如果有多个调用者（callers）同时请求相同资源（如内存或磁盘上的数据存储），他们会共同获取相同的指针指向相同的资源，直到某个调用者试图修改资源的内容时，系统才会真正复制一份专用副本（private copy）给该调用者，而其他调用者所见到的最初的资源仍然保持不变。这过程对其他的调用者都是[透明](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E9%80%8F%E6%98%8E)的。

此做法主要的优点是如果调用者没有修改该资源，就不会有副本（private copy）被创建，因此多个调用者只是读取操作时可以共享同一份资源。

##### 实现

1. **共享资源创建**：当多个进程或线程需要访问同一资源时，该资源被创建并标记为可共享。这样所有进程或线程都可以同时访问该资源，而无需进行实际的复制。
2. **写操作检测**：当某个进程或线程要进行写操作时，系统会检测到这一操作，并意识到需要进行复制，以确保该进程或线程有自己的私有拷贝。
3. **资源复制**：当写操作被检测到时，系统会为需要写的进程或线程创建一个新的私有拷贝。这个拷贝是原始资源的独立副本，可以进行修改而不会影响其他共享进程或线程。

##### 代码

set

```java
public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }
```

get

```java
 public E get(int index) {
        return get(getArray(), index);
    }
    
    @SuppressWarnings("unchecked")
    private E get(Object[] a, int index) {
        return (E) a[index];
    }    
```



##### 优点

- **节约内存**：在写操作之前，所有进程或线程共享同一块内存或资源，节省了复制所需的额外内存空间。
- **提高性能**：由于没有实际的复制操作，因此减少了对内存和其他资源的访问和操作，提高了整体性能。
- **避免不必要的复制**：在某些情况下，多个进程或线程对资源的修改是相同的，这时就不需要进行实际的复制，而是继续共享。

##### 缺点

- **数据一致性问题**：这种实现只是保证数据的最终一致性，在添加到拷贝数据而还没进行替换的时候，读到的仍然是旧数据。

- **内存占用问题**：如果对象比较大，频繁地进行替换会消耗内存，从而引发 Java 的 GC 问题，这个时候，我们应该考虑其他的容器，例如 ConcurrentHashMap。

##### 用途

- **虚拟内存管理中的写时复制**

一般把这种被共享访问的页面标记为只读。当一个task试图向内存中写入数据时，[内存管理单元](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E5%86%85%E5%AD%98%E7%AE%A1%E7%90%86%E5%8D%95%E5%85%83)（MMU）抛出一个异常，内核处理该异常时为该task分配一份物理内存并复制数据到此内存，重新向MMU发出执行该task的写操作。

- **数据存储中的写时复制**

[Linux](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/Linux)等的文件管理系统使用了写时复制策略。

[数据库](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E6%95%B0%E6%8D%AE%E5%BA%93)服务器也一般采用了写时复制策略，为用户提供一份snapshot。

- **软件应用中的写时复制**

[C++标准程序库](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/C%2B%2B%E6%A0%87%E5%87%86%E7%A8%8B%E5%BA%8F%E5%BA%93)中的[std::string](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/String_(C%2B%2B%E6%A0%87%E5%87%86%E5%BA%93))类，在C++98/C++03标准中是允许写时复制策略。但在[C++11](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/C%2B%2B11)标准中为了提高并行性取消了这一策略。[[1\]](https://link.zhihu.com/?target=https%3A//zh.wikipedia.org/wiki/%E5%AF%AB%E5%85%A5%E6%99%82%E8%A4%87%E8%A3%BD%23cite_note-1) GCC从版本5开始，std::string不再采用COW策略。



---

### 16 硬链接和软连接

![image-20231113205615620](C:\Users\WangZhe\AppData\Roaming\Typora\typora-user-images\image-20231113205615620.png)

硬链接（Hard Link）和软链接（Symbolic Link）都是用于创建文件系统中的链接，但它们有一些区别。

**硬链接**

- 硬链接是操作系统中的一个文件系统链接。它是通过将一个文件关联到一个已存在的路径来创建的，本质上是多个文件名指向同一个inode。
- 硬链接与原始文件是平等的，删除任何一个硬链接或原始文件不会影响其他链接或原始文件。
- 可以通过硬链接访问和修改原始文件内容。
- 硬链接不能跨越文件系统或分区创建。
- 硬链接没有指向目录的硬链接。

**软链接**

- 软链接是一个指向目标文件或目录的指针，类似于快捷方式。它是以文件路径的形式来表示的，指向另一个文件或目录的路径。
- 软链接类似于一个符号，当访问软链接时，操作系统会自动将其解析到目标路径。
- 删除软链接不会删除目标文件或目录，但如果删除了目标文件或目录，软链接将无效。
- 可以跨越文件系统或分区创建软链接。

**总结**

- **文件真正删除的条件是与之相关的所有硬连接文件均被删除。**

- 硬链接创建的文件有相同的inode和权限，可以看作是同一个文件的多个别名；软链接创建的文件是一个指向原始文件或目录的指针。
- **硬链接不能指向目录，软链接可以。**
- 硬链接一旦创建就不能改变其目标，而软链接可以。



---



### 17 硬中断和软中断

在计算机系统中，硬中断（Hardware Interrupt）和软中断（Software Interrupt）是两种不同类型的中断，用于处理系统中的异步事件和特殊情况。

1. **硬中断（Hardware Interrupt）：**
   - **来源：** 由外部硬件设备发送给处理器的中断信号，如定时器、键盘、磁盘等设备。
   - **触发条件：** 由硬件设备的状态或事件引起，例如时钟中断、IO中断等。
   - **处理方式：** 当硬件设备发生中断时，处理器会立即中断当前执行的指令，保存当前的上下文状态，跳转到中断处理程序来处理中断事件。硬中断是由硬件控制器直接触发和处理的。
2. **软中断（Software Interrupt）：**
   - **来源：** 由软件程序主动触发的中断，也称为异常或陷阱。
   - **触发条件：** 由软件程序中的特殊指令或系统调用引起，例如系统调用、除零错误等。
   - **处理方式：** 软中断是由当前运行的程序执行特殊指令或发生特殊情况时触发的，处理器会跳转到相应的中断服务例程来处理。软中断是通过指令执行而非外部硬件触发的。

总结：

- **硬中断** 是由外部硬件设备触发的，引起处理器执行相应的中断处理程序，是系统异步事件的一种处理机制。
- **软中断** 是由软件程序中的指令或系统调用触发的，允许程序主动请求内核的服务或处理异常情况。



---

### 18 什么是上下文

上下文（Context）在计算机科学中通常指的是程序执行的环境和状态信息。它包含了程序在执行过程中所需的各种数据和资源，以及程序在继续执行时所需的执行状态。

在操作系统、编程语言和计算机体系结构中，上下文通常包括以下方面的信息：

1. **进程上下文（Process Context）：** 进程上下文包括了一个正在执行的进程所需的所有信息，如程序计数器（Program Counter）、寄存器的值、打开的文件描述符、环境变量、内存映射、权限等。进程上下文是切换和管理不同的进程之间的重要组成部分，以保证进程之间的隔离和正确执行。
2. **线程上下文（Thread Context）：** 线程上下文类似于进程上下文，它包含了一个正在运行的线程所需的信息。线程上下文通常包括线程栈、寄存器的值、线程私有变量等。线程上下文的切换是在多线程编程中实现并发和共享资源的关键。
3. **中断上下文（Interrupt Context）：** 当硬件设备或异常事件发生时，处理器会跳转到相应的中断处理程序，并在中断上下文中执行。中断上下文包含了在中断处理程序中所需的所有信息，如中断类型、中断向量、中断处理程序地址、寄存器的值等。
4. **虚拟机上下文（Virtual Machine Context）：** 在虚拟化环境中，虚拟机上下文包括了虚拟机的状态信息，如虚拟CPU的寄存器、内存映射、设备状态等。虚拟机上下文的切换用于实现虚拟机的多任务调度和资源隔离。

上下文的保存和恢复是操作系统和编程语言运行时环境的基本功能之一，它允许程序在不同的执行上下文中切换，并在切换后能够继续执行。上下文切换的开销往往较高，因此在设计和实现中要注意提高效率和减少不必要的上下文切换。

### 19 为什么中断上下文不能睡眠

中断发生以后，CPU跳到内核设置好的中断处理代码中去，由这部分内核代码来处理中断。这个处理过程中的上下文就是**中断上下文**。

**为什么可能导致睡眠的函数都不能在中断上下文中使用呢？** 

首先睡眠的含义是将进程置于“睡眠”状态，在这个状态的进程不能被调度执行。然后，在一定的时机，这个进程可能会被重新置为“运行”状态，从而可能被调度执行。

 可见，“睡眠”与“运行”是针对进程而言的，代表进程的task_struct结构记录着进程的状态。内核中的“调度器”通过task_struct对进程进行调度。
但是，中断上下文却不是一个进程，它并不存在task_struct，所以它是不可调度的。所以，在中断上下文就不能睡眠。

**那么，中断上下文为什么不存在对应的task_struct结构呢？**
中断的产生是很频繁的（每毫秒级会产生一个时钟中断），并且中断处理过程会很快。

如果为中断上下文维护一个对应的task_struct结构，那么这个结构频繁地分配、回收，并且影响调度器的管理，这样会对整个系统的吞吐量有所影响。


---



### 20 什么是RCU机制

RCU（Read-Copy-Update）是一种用于并发访问共享数据的同步机制。它旨在提供高效的**读取**操作，并在进行写操作时保证数据的一致性。RCU机制经常用于操作系统内核、数据库系统和并发编程中。

RCU机制的基本原理如下：

1. **读操作（Read）**：
   - 在RCU中，读操作是无锁的，多个线程可以同时进行读取操作，而不会相互阻塞。
   - 当进行读取操作时，不会对数据进行任何拷贝或同步操作。读取操作者可以自由地访问共享数据。
2. **拷贝操作（Copy）**：
   - 当需要对共享数据进行修改时，RCU使用"Copy"的方式来保持数据的一致性。
   - 在修改之前，RCU会创建一个数据的副本，并将修改应用到副本上，而不是直接修改原始数据。这样，读操作可以继续访问原始数据，而不受到写操作的影响。
3. **更新操作（Update）**：
   - 在RCU机制中，更新操作是延迟进行的。
   - 在更新操作时，旧版本的数据仍然可以被读取操作访问，而不会被阻塞。
   - 更新操作完成后，会将新版本的数据发布，并且随后的读操作将使用新版本的数据。

通过使用上述的原理，RCU机制提供了以下优点：

- **高并发性能**：RCU机制避免了读取操作之间的互斥和同步，使得多个线程可以同时进行读取操作，提高了并发性能。
- **低延迟**：读取操作不会被阻塞，而且写操作的同步和实际修改操作是异步的，从而减少了操作的延迟。
- **无锁设计**：RCU机制不需要使用传统的锁机制，减少了锁的开销和并发冲突的可能性。
- **数据的一致性**：通过延迟更新操作、创建副本，RCU保证了数据的一致性，使得读操作可以安全地访问数据。

总之，RCU是一种高性能、低延迟的并发编程机制，通过延迟更新和数据拷贝的方式保证了共享数据的一致性，并提供了无锁的读取操作。



---



### 21 惊群现象

##### 是什么

​		惊群效应（thundering herd）是指多进程（多线程）在同时阻塞等待同一个事件的时候（休眠状态），如果等待的这个事件发生，那么他就会唤醒等待的所有进程（或者线程），但是最终却只能有一个进程（线程）获得这个时间的“控制权”，对该事件进行处理，而其他进程（线程）获取“控制权”失败，只能重新进入休眠状态，这种现象和性能浪费就叫做惊群效应。

##### **惊群效应消耗了什么**

- Linux 内核对用户进程（线程）频繁地做无效的调度、[上下文切换](https://www.zhihu.com/search?q=上下文切换&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A545048210})等使系统性能大打折扣。

  ​		上下文切换（[context switch](https://www.zhihu.com/search?q=context switch&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A545048210})）过高会导致 CPU 像个搬运工，频繁地在寄存器和运行队列之间奔波，更多的时间花在了进程（线程）切换，而不是在真正工作的进程（线程）上面。直接的消耗包括 CPU 寄存器要保存和加载（例如[程序计数器](https://www.zhihu.com/search?q=程序计数器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A545048210})）、系统调度器的代码需要执行。间接的消耗在于多核 cache 之间的共享数据。

- 为了确保只有一个进程（线程）得到资源，需要对资源操作进行加锁保护，加大了系统的开销。

  ​		目前一些常见的[服务器软件](https://www.zhihu.com/search?q=服务器软件&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A545048210})有的是通过锁机制解决的，比如 Nginx（它的锁机制是默认开启的，可以关闭）；还有些认为惊群对系统性能影响不大，没有去处理，比如 Lighttpd。

##### 解决方案

- **Linux解决方案——Accept**

​		Linux 2.6 版本之前，监听同一个 socket 的进程会挂在同一个等待队列上，当请求到来时，会唤醒所有等待的进程。

​		Linux 2.6 版本之后，通过引入一个标记位 WQ_FLAG_EXCLUSIVE，解决掉了 accept 惊群效应。

​		WQ_FLAG_EXCLUSIVE 标记告诉内核进行排他性的唤醒，即唤醒一个进程后即退出唤醒的过程

- **Linux解决方案——Epoll**

  
  
- ##### Nginx 解决方案——锁的设计

  Nginx 中使用的锁是自己来实现的，这里锁的实现分为两种情况，一种是支持原子操作的情况，也就是由 NGX_HAVE_ATOMIC_OPS 这个宏来进行控制的，一种是不支持原子操作，这是是使用文件锁来实现。

  - **锁结构体**

    ```C
    typedef struct {  
    #if (NGX_HAVE_ATOMIC_OPS)  
        ngx_atomic_t  *lock;  
    #else  
        ngx_fd_t       fd;  
        u_char        *name;  
    #endif  
    } ngx_shmtx_t;
    ```

    

​		

