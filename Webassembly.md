## Webassembly论文总结：

1、Analyzing the Performance of WebAssembly vs. Native Code（Usenix Security 2019）：开发了Browsix-Wasm和Browsix-SPEC，前者可以将unix代码转换为Webassembly，并提高了性能，后者能够详细读取硬件性能计数器信息来进行相似的性能对比。结果证明Webassembly比JavaScript快，但是与本机性能有着非常明显的差异。

2、Everything Old is New Again:Binary Security of WebAssembly（Usenix Security 2020）：对实际二进制文件和编译到WebAssembly的源程序进行了经验风险评估，证明了后者有更大的可利用的安全问题。

3、EOSAFE: Security Analysis of EOSIO Smart Contracts（Usenix Security 2021）提出了EOSAFE，这是第一个静态分析框架，可用于在Wasm字节码级别自动检测EOSIO智能合约中的漏洞。

4、CROW: Code Diversification for WebAssembly（NDSS 2021）：提出了CROW工具，第一个使WebAssembly程序多样化的自动化工作流和工具，它从一个输入程序生成许多不同的WebAssembly二进制文件。

5、MINOS*: A Lightweight Real-Time Cryptojacking Detection System（NDSS 2021）：提出了一个轻量级的加密劫持恶意软件检测系统，使用恶意和良性Wasm二进制文件训练了一个CNN的分类器进行实时监测。

6、
