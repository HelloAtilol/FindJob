# NLP的任务与方法
---

## 词法分析

### 1. 分词(Word Segmentation/Tokenization)
> 对没有明显边界的文本进行切分，得到词序列；     

**示例**

```python
>>> import jieba
>>> # 默认模式(精准模式)
>>> a = jieba.cut("最美的不是下雨天，是和你一起躲过雨的屋檐。")
>>> print("/".join(a))
最美/的/不是/下雨天/，/是/和/你/一起/躲过/雨/的/屋檐/。

>>> # 全模式
>>> b = jieba.cut("最美的不是下雨天，是和你一起躲过雨的屋檐。", cut_all=True)
>>> print("/".join(b))
最美/美的/不是/下雨/下雨天/雨天/，/是/和/你/一起/躲过/雨/的/屋檐/。

>>> # 搜索引擎模式
>>> c = jieba.cut_for_search("最美的不是下雨天，是和你一起躲过雨的屋檐。", cut_all=True)
>>> print("/".join(c))
最美/的/不是/下雨/雨天/下雨天/，/是/和/你/一起/躲过/雨/的/屋檐/。
```

**算法**

- **基于词表的分词算法**
基于词表的分词，依托于Trie树，又称字典树，单词查找树。
假设定义字符串集合如$ X\{的确, 确实, 实在, 实不相瞒, 在理\} $，则组成的标准Trie树结构如下图：
![标准Trie树](D:\workspace\Python\leetcode\img\标准Trie树.webp)

	
	* **最大匹配法(MM)**
	  而最大匹配法的原理就是**“单词的颗粒度越大，越能表示的含义越确切”**。
	  对于输入的一段文本从左至右(从右至左)，以贪心的方式切分出当前位置上长度最大的词。例如：拆分"他说的确实在理"，从左至右也叫**正向匹配算法(FMM)**，结果是“他/说/的确/实在/理”；从右至左叫**反向匹配算法(BMM)**，结果是“他/说/的/确实/在理”
	
	* **最短路径分词法**
	  > 最短路径分词算法首先将一句话中的所有词匹配出来，构成词图，之后寻找从起始点到终点的最短路径作为最佳组合方式。   
	  
	  ![最短路径_分词](D:\workspace\Python\leetcode\img\最短路径_分词.webp)
	  求解最短路径有两种方法：Dijkstra算法和N-最短路径，N-最短路径分词是对Dijkstra算法的扩展，在每一步保存最短的N条路径，并记录这些路径上当前节点的前驱，在最后求得最优解时回溯得到最短路径。该方法的准确率优于Dijkstra算法，但在时间和空间复杂度上都更大。

- **基于统计模型的分析算法**
	* **基于N-gram语言模型的分词方法**
	  最短路径分词法中每个节点之间的联系的权值是1，在现实场景中，常用词与罕见词的权重是不同的。因此最短路径分词可以转换为最大概率求解，“他说的确实在理”可以转为：
	  $$ p(他说的确实在理)=p(他)p(说|他)p(的|他说)...p(理|他说的确实在) $$
	  根据条件概率，可以简化为：
	  $$ p(s_1s_2...s_n)=\prod_{i=1}^np(w_i|w_{i-1}) $$
	  那么“他说的确实在理”可以表达成下图：
	  
	  ![二元语言模型](D:\workspace\Python\leetcode\img\二元语言模型.webp)
	
- **基于序列标注的分词算法**
	* **基于HMM的分词方法**
	
	  隐马尔可夫模型(HMM)把每句话中的每一个字都进行了标注，标注状态有四个`{B,M,E,S}`B代表词语开始，M代表词语中间，E代表词语结束，S代表单字成词。
	  而HMM是一个生成式模型，X为观测序列(即句子),Y为隐藏序列(即标注的序列)，公式为：
	  $$ P(X,Y)=\prod_{t=1}^TP(y_t|y_t-1)*P(x_t|y_t) $$
	  
	  而分词对应了HMM的解码问题，即已知模型参数，寻找最可能的标注序列，即求$P(X,Y)$。可以使用Biterbi算法。[详情](http://www.52nlp.cn/tag/hmm%E4%B8%AD%E6%96%87%E5%88%86%E8%AF%8D)
	  HMM有一个缺点：由于输出独立性假设，导致不能考虑上下文的特征，限制了特征的选择。
	  
	* **基于CRF的分词方法**
	
	  条件随机场(CRF)与HMM的方向相似，都是对句子中的字进行标注成`{B,M,E,S}`只是标注的方法是用的CRF。CRF的原理如下：
	  句子s
	  i表示句子s中的第i个单词
	  $l_i$表示要评分的标注系列给第i个单词标注的词性；
	  $\lambda_j$表示第j个特征函数的权重
	  则句子s对应的序列$l$评分公式：
	  $$ score(l|s)=\sum_{j=1}^m\sum_{i=1}^n\lambda_jf_j(s,i,l_i,l_i-1) $$
	  那么CRF与HMM的区别：
	  	每个HMM都可以当作是一个CRF的特征函数；
	  	HMM要求每个字当前标签只依赖于前一个标签；
	  CRF相较于HMM的优势：
	  	CRF可以定义数量更多，种类更丰富的特征函数；
	  	CRF可以使用任意的权重。
	  只使用CRF可能并不能获得最好的结果，现在常用的做法是，BiLSTM+CRF对字进行标注，更加兼顾全文的信息，进而获得更好的结果。

### 2. 新词发现(New Words Identification)
> 找到文本中具有新形势、新意义或是 新用法的词；

### 3. 形态分析(Mrophological Analysis)
> 分析单词的形态组成，包括词干(Sterms)、词根(Roots)、词缀(Prefixes and Suffixes)等； 

### 4. 词性标注(Part-of-speech Tagging)
> 确定文本中每个词的词性。包括动词(Verb)、名词(Noun)、代词(pronoun)等； 

### 5. 拼写校正(Spelling Correction)

---

## 句子分析

### 1. 组块分析(Chunking)
> 标出句子中的短语块，例如名词短语(NP)，动词短语(VP)等;

### 2. 超级标签标注(Super Tagging)
> 给每个句子中的每个词标注上超级标签，超级标签是句法树中与该词相关的树形结构；

### 3. 成分句法分析(Constituency Parsing)
> 分析句子的成分，给出一棵树由终结符和非终结符构成的句法树；

### 4. 依存句法分析(Dependency Parsing)
> 分析句子中词与词之间的依存关系，给一颗由词语依存关系构成的依存句法树；

### 5. 语言模型(Language Modeling)
> 对给定的一个句子进行打分，该分数代表句子合理性(流畅度)的程度 

### 6. 语种识别(Language Identification)

### 7.句子边界检测(Sentence Boundary Detection)
> 给没有明显句子边界的文本加边界

---

## 语义分析

### 1.  词义消歧(Word Sense Disambiguation)
> 对有歧义的词，确定其准确的词义；

### 2. 语义角色标注(Semantic Role Labeling)
> 标注句子中的语义角色类标，语义角色，语义角色包括施事、受事、影响等；

### 3. 抽象语义表示分析(Abstract Meaning Representation Parsing)
> AMR是一种抽象语义表示形式，AMR parser把句子结构成AMR结构；

### 4. 一阶谓词逻辑推演(First Order Predicate Calculus)
> 使用一阶谓语逻辑系统表达语义；

### 5. 框架语义分析(Frame Semantic Parsing)
> 根据框架语义学的观点，对句子进行语义分析；

### 6. 词汇/句子/段落的向量化表示(Word/Sentence/Paragraph Vector)
> 研究词汇/句子/段落的向量化方法，向量的性质和应用；

---

## 信息抽取

### 1. 命名实体识别(Named Entity Recognition)
> 从文本中识别出命名实体，实体一般包括人名、地名、机构名、日期、时间、货币、百分比等；

### 2. 实体消歧(Entity Disambiguation)
> 确定实体指代的现实世界中的对象；

### 3. 术语抽取(Terminology/Giossary Extraction)
> 从文本中确定术语；

### 4. 共指消解(Coneference Resolution)
> 确定不同实体的等价描述，包括代词消解和名词消解；

### 5. 关系抽取(Relationship Extraction)
> 确定文本中两个实体之间的关系类型；

### 6. 事件抽取(Event Extraction)
> 从无结构的文本中抽取结构化事件；

### 7. 情感分析(Sentiment Analysis)
> 对文本的主观性情绪进行提取；

### 8. 意图识别(Intent Detection)
> 对话系统中的一个重要模块，对用户给定的对话内容进行分析，识别用户意图；

### 9. 槽位填充(Slot Filling)
> 对话系统和知识图谱的重要模块，从对话内容中分析出与用户意图相关的有效信息；

---

## 顶层任务

### 1. 机器翻译(Machine Transation)
> 通过计算机自动化的把一种语言翻译成另外一种语言；

### 2. 文本摘要(Text Summarization/Simplication)
> 对较长文本进行内容梗概的提取；

### 3. 问答系统(Question-Answering System)
> 针对用户提出的问题，给出相应的答案；

### 4. 对话系统(Dialogue System)
> 能够和用户进行交流对话，从对话中捕获用户的意图，并分析执行；

### 5.阅读理解(Reading Comprehension)
> 阅读一篇文章，能够回答一些给定的文章相关问题；

### 6. 自动文章分级(Automatic Essay Grading)
> 给定一篇文章，对文章的质量进行打分或分级

---
---
BERT
1.attention
2.transformer
3.bert