## 崩溃分析
### 1、crash exploration mode（afl-fuzz -C）(崩溃探索模式)
将一个崩溃的测试用例交给afl-fuzz作为自动运行的起点。然后，模糊器使用其通常的反馈机制和遗传算法来查看它在检测代码库中能走多远，同时仍将程序保持在崩溃状态。阻止崩溃发生的突变被丢弃；那些不会以任何明显的方式改变执行路径的也是如此。使崩溃以微妙不同的方式发生的偶然突变将被保留，并用于稍后为后续的模糊测试轮播种。
### 2、crashwalk
得到更细致的crashes分类结果，以及导致crashes的具体原因，也集成到了AFL中。（根据崩溃发生返回的信号（如SIGSEGV 11））
### 3、 afl-collect
afl-collect也是afl-utils套件中的一个工具，同样也是基于exploitable来检查crashes的可利用性。它可以自动删除无效的crash样本、删除重复样本以及自动化样本分类。
### 4、GDB
### 5、Aurora: Statistical Crash Analysis for Automated Root Cause Explanation（2020 Usenix Security）
从单个崩溃输入，生成一组不同的类似输入，然后，执行每个找到的输入时跟踪程序的状态，捕获崩溃和非崩溃输入之间行为差异的简单布尔表达式。对所有谓词的统计分析使我们能够确定确定根本原因的谓词，从而揭示了根本原因的位置以及对应的解释。

## 崩溃的形成
