### AFL++的特点：
AFL++ 是 Google 的 AFL 的高级分支 - 更快、更多、更好的突变，包含了源代码fuzzing和闭源的二进制fuzzing，对于固件（二进制）的fuzzing，仿真模式分为了QEMU模式、nyx模式、unicore模式。
AFL++fuzzing部分的内部实现细节：
    
    1、fork_server：
