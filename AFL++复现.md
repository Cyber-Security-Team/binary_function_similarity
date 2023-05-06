### AFL++的特点：
AFL++ 是 Google 的 AFL 的高级分支 - 更快、更多、更好的突变，包含了源代码fuzzing和闭源的二进制fuzzing，对于固件（二进制）的fuzzing，仿真模式分为了QEMU模式、nyx模式、unicore模式。

### AFL++fuzzing部分的内部实现细节：
#### 1、fork_server：
其基本思路是：启动target进程后，target会运行一个fork server；fuzzer并不负责fork子进程，而是与这个fork server通信，并由fork server来完成fork及继续执行目标的操作。这样设计的最大好处，就是不需要调用execve()，从而节省了载入目标文件和库、解析符号地址等重复性工作。

![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/fork_server_1.png)    
![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/fork_server_2.png)

#### 2、共享内存：
run_target会记录执行过程中的分支信息；随后，fuzzer便可以根据这些信息，判断这次执行的整体流程和代码覆盖情况。AFL使用共享内存，来完成以上信息在fuzzer和target之间的传递。具体地，fuzzer在启动时，会执行setup_shm()方法进行配置。其首先调用shemget()分配一块共享内存，大小MAP_SIZE为64K。

#### 3、分支信息的记录和分析
具体地，target是将每个分支的执行次数用1个byte来储存，而fuzzer则进一步把这个执行次数归入以下的buckets中

![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/%E5%88%86%E6%94%AF%E4%BF%A1%E6%81%AF%E7%9A%84%E8%AE%B0%E5%BD%95.png)

### 复现结果
![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/%E7%BD%91%E7%BB%9C%E6%91%84%E5%83%8F%E5%A4%B4%E5%9B%BA%E4%BB%B6.png)
![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/RV130W%20Wireless-N%20%E5%A4%9A%E5%8A%9F%E8%83%BD%20VPN%20%E8%B7%AF%E7%94%B1%E5%99%A8%E5%9B%BA%E4%BB%B6josnparse.png)
