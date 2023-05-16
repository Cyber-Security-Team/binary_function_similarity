### AFL++的特点：
AFL++ 是 Google 的 AFL 的高级分支 - 更快、更多、更好的突变，包含了源代码fuzzing和闭源的二进制fuzzing，对于固件（二进制）的fuzzing，仿真模式分为了QEMU模式、nyx模式、unicore模式。

### AFL整体实现框架：
![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/AFL%E6%95%B4%E4%BD%93%E6%A1%86%E6%9E%B6.png)

### AFL++fuzzing部分的内部实现细节：

#### 1、插桩
1）、对闭源二进制程序采用的是动态插桩，QEMU先将程序加载在guest_base中，qemu和目标程序公共运行，并在目标程序运行期间能够在程序中进行插桩，
qemu插桩过程中的fork_server：新基本块的首次运行也会产生大量延迟, 为了消除这个问题, AFL fork server 在运行的模拟器与父进程之间提供了一个管道. 这个管道用于通知父进程有关于任何新遇到的块的地址, 并把他们添加入为将来子进程复制的翻译缓存中.
#### 2、fork_server：
forkserver的好处：
其基本思路是：fuzzer并不负责fork子进程，而是与这个fork server通信，并由fork server通过复制父进程的方式创建子进程，实现共享进程以及共享内存。按照原本的做法，fuzzer每次fork一个子进程，都要execve()函数启动目标程序来代替自己，来重新启动此程序。这样设计的最大好处，就是不需要执行execve()函数的多次调用，以往从而节省了载入目标二进制程序和库、解析符号地址等重复性工作，因为是直接从forkserver复制过来的，程序本身就是打开的，各种库也已经加载好了。所以会节省时间。并且这些fuzzer之间共享测试用例和其他状态信息，从而就知道哪些用例被测试了，避免重复工作。

![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/fork_server_1.png)    
![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/fork_server_2.png)
![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/fork_server_3.png)
#### 3、共享内存：
共享内存的好处：减少内存占用、支持并发访问、避免数据复制。
在共享内存中，操作系统会将一段物理地址映射到多个进程的虚拟地址空间中，实现了多个进程访问同一片内存区域的功能，每个进程将测试数据加载到自己的虚拟内存中，所以也不会造成进程间的阻塞。AFL使用共享内存，来完成执行过程中的分支信息在fuzzer和target之间的传递。具体地，fuzzer在启动时，会执行setup_shm()方法进行配置。其首先调用shmget()分配一块共享内存，大小MAP_SIZE为64K。，并将返回的变量以环境变量的方式加载到环境中，其他fuzzer可以向forkserver发送命令获取变量以获得共享内存的首地址的指针。在一个进程结束之后覆盖率等信息刷新到物理内存中，所以测试用例都结束之后父进程进行汇总后会将总的覆盖率写到物理内存中，这样减少了重复的修改内存。

#### 4、分支信息的记录和分析(buckets机制)
具体地，fuzzer通过哈希的方式将每个测试用例的探索的路径数量、探索的深度等归入以下的buckets中。一个bucket包含了一组相似的用例（访问了相同的代码块）。

![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/%E5%88%86%E6%94%AF%E4%BF%A1%E6%81%AF%E7%9A%84%E8%AE%B0%E5%BD%95.png)


### 复现结果
![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/%E7%BD%91%E7%BB%9C%E6%91%84%E5%83%8F%E5%A4%B4%E5%9B%BA%E4%BB%B6.png)
![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/RV130.png)
