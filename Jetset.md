# [Jetset复现](https://github.com/aerosec/jetset/)
## 仿真部分：
### 构建Jetset
    make clone
    make config_qemu
    make build_qemu
    make virtualenv
    make build_jetset_engine
### 仿真结果
![jetset仿真结果](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/jetset%E4%BB%BF%E7%9C%9F%E7%BB%93%E6%9E%9C.png)
![image](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/1.png)
## [fuzz部分](https://github.com/aerosec/jetset_fuzzer/tree/master)
### docker构建
![构建docker](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/%E6%9E%84%E5%BB%BAdocker.png)
### jetset击中目标位置
![击中目标](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/%E7%9B%AE%E6%A0%87%E5%87%BB%E4%B8%AD.png)
![PC寄存器](https://github.com/Cyber-Security-Team/binary_function_similarity/blob/main/image/PC%E6%8C%87%E9%92%88%E5%AF%84%E5%AD%98%E5%99%A8.png)
### 目前存在的问题
这篇文章它本身是对CMU-900和树莓派进行fuzz的，但是相关的配置文件数据没有公开。对于P2IM的10个常规固件进行fuzz需要修改相应的脚本和配置文件，否则afl的默认脚本还是对cmu-900fuzz，就会出现错误，目前还在找应该修改哪几个脚本和配置文件。
