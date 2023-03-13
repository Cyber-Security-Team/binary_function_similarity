# binary_function_similarity
一、论文汇总：

论文一：Order Matters: Semantic-Aware Neural Networksfor Binary Code Similarity Detection内容（2020）
    三个模块：语义感知模块、结构感知模块、顺序感知模块 语义感知模块：在语义感知模块，使用BERT模型提取语义信息。使用BERT对控制流图作预训练，获得基本块的语义信息。控制流图的基本块可以看作句子，基本块中的token可以看作词语。 结构感知模块（MPNN + GRU）：使用MPNN计算控制流图的graph semantic & structural embedding。MPNN有三个步骤：message function（M），update function（U）以及readout function（R）。 顺序感知模块（邻接矩阵+CNN） 使用CNN模型提取节点顺序的信息。

论文二：How Machine Learning Is Solving the Binary Function Similarity Problem（Usenix Security 2022）
    论文选择、比较并实现十种最具代表性的方法及其可能的变体。通过重新实现各种方法我们分离出现有的“原语”，并在独立使用或相互结合时对它们进行评估，以获得见解并查明隐藏在先前工作复杂性中的重要因素，并回答各种开放的研究问题。

二、研究现状二进制代码相似性检测技术的分类:

2.1、基于文本：
    基于标识符的检测（karta）：（需要反汇编）首先模糊通用寄存器名和内存地址，然后提取指令的操作码和操作数，或者提取字符串以生成标识符序列，最后通过子序列匹配来判断不同代码是否相似。
    基于字节流的检测（aDiff）：不对二进制代码进行反汇编，直接比较程序的二进制字节流是否相近。

2.2、基于属性度量
    基于属性度量的检测技术关注程序汇编代码中可度量的属性和特征，提取属性和特征的度量值，构成多维向量(该方案认为特征向量能够从不同维度标识代码段)，以特征向量之间的距离来度量代码段之间的相似度大小。这类方法需要确定检测的最小粒度(如基本块或函数)，在最小粒度范围内通过对属性和特征的统计计数来获得度量值。度量值可能是但不限于是特定常量和指令等标识、函数的输入和输出等对象的计数。

2.3、基于程序逻辑的检测技术
    利用列表、树形或图形等数据结构来记录和描绘程序的数据流或控制流信息(该类技术认为所得的中间表示能够捕获程序中一定的语法和语义信息)，通过匹配相似的序列、节点或边、公共子树或子图来寻找逻辑或功能相似的程序基本块或函数。

2.4、基于语义
    
    1、Gemini 的系统中，利用改进的 Structure2Vec模型(Structure2Vec 结构感知模型的灵感来自图形模型推理算法，该算法根据图的拓扑结构递归地聚合得到特定于顶点的特征向量)来构成暹罗架构网络，将函数的CFG嵌入高维向量，通过计算向量的余弦距离来度量函数间的相似性。
    
    2、Asm2vec模型，是基于改进的 PV-DM[33]模型(PV-DM神经网络模型是为文本数据而设计的，基于文档中的标识来学习文档表征)的汇编代码表征学习模型。该模型利用自定义的函数内联和随机漫步机制，将函数的CFG建模为汇编指令的线性序列，以该汇编文本为输入，不需要任何先验知识，学习指令的语义，构建指令嵌入向量，最终得到函数的语义嵌入向量。Asm2vec是第一个将表征学习作为汇编代码构建特征向量的方案，具有优秀的抗混淆和抗编译器优化特性，但其不能用于跨架构比较。
    
    3、SAFE 网络，先利用 NLP 的 word2vec模型(word2Vec 是谷歌于2013年推出的一款 NLP 工具，其特点是能将单词向量化，定量度量单词之间的关系[35])来实现汇编语言的指令嵌入，再利用循环神经网络(RNN)来捕获指令序列的上下文关系，最终实现函数的嵌入，SAFE摒弃了CFG作为中间表示，汇编代码的语义信息直接由深度神经网络嵌入到高维向量中，这样既省去了会消耗大量时间的CFG提取过程，又避免了人为偏见的引入。SAFE模型利用RNN网络构建了暹罗架构并采用有监督的学习方法来训练模型，虽然其可以由程序自动随机构造正样本对和副样本对，但针对跨架构检测任务的训练，随着系统支持指令架构种类的增加，在理论上，其训练样本库的规模需要根据不同架构的组合成倍地扩大，这就限制了该模型的可扩展性。
    
    4、GeneDiff[37]是一种使用语义表征模型来学习二进制代码中间语言，从而实现跨架构克隆检测研究的方法。它借助动态分析VEX IR(VEX IR是动态分析框架Valgrind[38-39]的中间语言，是一种二地址形式的中间表示，支持多种指令架构)的中间语言消除了不同指令架构之间的差异，通过改进的PV-DM模型来为函数的VEX IR生成语义以嵌入向量。因为每一条汇编指令会被翻译成多条VEX指令，所以该模型将由同一条汇编指令翻译而来的多条VEX指令组合看作一个单词，将基本块看作句子，将函数看作段落，使用向量间的余弦距离来度量函数间的相似度。
    
    5、ordermatters首先利用改进的BERT模型将函数CFG节点转换为语义嵌入向量，然后利用改进的MPNN模型来获得CFG的语义和结构以嵌入向量，接着利用CNN模型获得CFG的顺序嵌入向量，最后将后两者的综合作为最终的函数嵌入向量，用于函数间的相似性度量。
    
    6、jTrans将二进制代码的控制流信息嵌入到基于Transformer的语言模型中的解决方案，通过使用分析的二进制文件的新的跳跃感知表示和新设计的预训练任务，但是在指令的跳转上，它只能识别直接跳转，并不能识别间接跳转。由于缺乏通用的大型基准，以往每个研究都创建了自己的数据集，通常来自GNUtils、coreutils和openssl等小型存储库。这些小数据集具有相似的代码模式，因此缺乏多样性，这反过来会导致模型过于复杂，并给人留下高性能的错误印象。

三、二进制代码相似性检测的难题
    
    难题1 跨编译器问题。由于设计目的和采用的算法不同，不同的编译器产生的二进制代码不尽相同。例如，虽然寄存器的选择过程是通过各种启发方式和复杂的代码来传递驱动的，但在某些情况下，即使存在公共约定，编译器也不会完全遵守。这些都导致了寄存器选择的任意性。
    
    难题2 跨编译优化配置问题。同一版本的编译器采用不同的编译优化配置所产生的二进制代码不尽相同。例如，可执行程序的调试版本相比发行版本会增加许多与调试相关的信息和调用。不同的优化等级，其区别在于增加或减少了对堆栈的检查和清理等操作，会导致最终的可执行文件的长度发生显著变化。再比如在x86-64架构下的GCC编译器，只有在-O0不优化的编译条件下，寄存器rbp才具有帧指针的特殊用途。
    
    难题3 跨指令架构问题。不同指令架构的二进制代码不同。不同架构的指令集、寄存器、机器字长都有较大差异，从而导致相应的二进制代码差异巨大。

四、BCSD应用场景
    
    1、漏洞发现
    
    2、恶意软件检测
    
    3、聚类
    
    4、软件剽窃检测
    
    5、补丁分析
    
    6、软件供应链分析
五、可能的创新点

        
