## 目录
--
1. ####  **第一节课的实操**  
    - 使用`Wordcloud`制作词云

    - 使用jieba进行分词
    - 综合运用
2.  #### **常见问题**  
    - 环境问题
    - 代码报错
3. ####**拓展**
    - Markdown使用技巧
    - Jupyter使用技巧

> ### 写在前面
> 回忆起第一次接触 Python ，还是在疫情期间，跟着网课一点一点地了解了 Python 的基础语法。后来的计算广告课程，算是开始使用 Python 进行实战，真正解决应用问题。时至今日，我、我们还在思考，我们学习 Python 的意义是什么？它能为我们带来什么？  <br>
如果我们只是把 Python 当成一种编程语言，一种解决问题的工具，我想这对于我们来说远远不够，也不是这门课的终极奥义。
<br>廖祥忠校长在今年开学典礼时说：大学，要学习游泳、编程，还要及时清理内存。听君一席话，我开始试着将编程放在一个更宏观的视野之下去理解它，感受它。回到最初的问题，我们为什么学习 Python ？
<br>希望这门课会给我一个答案，我也用这种方式记录我的学习和想法。共勉！

<br>
# 一、Class1 实操
--
>第一节课的上机实操，通过对文本进行分词并制作词云图，对环境和库的安装、基本代码、基本操作进行了复习。
> <br>环境`Python3.7` `Jupyter Notebook` 用到的库 `Wordcloud` `jieba` `os`  `matplotlib.pyplot`

##1.使用`Wordcloud`制作词云
--

需求：读取一段文档，使用`Wordcloud`生成一张词云图。代码如下：

```py
import os
print(os.getcwd())        #获取根目录以便存放相关文件
from wordcloud import WordCloud
import matplotlib.pyplot as plt        #方便在Jupyter中预览生成的图片
text=open("123.txt",'r',encoding='utf-8').read()
font=r'msyh.ttc'
wc=WordCloud(font_path=font,width=800,height=600).generate(text)

plt.imshow(wc)
plt.axis('off')
plt.show()
```
我的文本选用的廖祥忠校长的讲话：“大学是什么”。使用`Wordcloud`傻瓜式生成的图片如下：<br>
![“大学是什么”词云](https://raw.githubusercontent.com/EldenBob/Python-Class-Blog/main/image/class1/1.png)

## 2.使用`jieba`进行分词
---
需求：观察上图可知，`Worldcloud`直接生成的词云图中，分词出现了一些问题。那么这个时候就需要引入中文分词库`jieba`来帮助`Worldcloud`更好的生成图片。代码参考：

```py
import jieba
jieba.add_word("传媒大学")        
words = jieba.lcut(text)
```
这里我使用了`.add_word`的功能，目的是为了更好的匹配文本，将特定词语如“中传人”加入词库。这时如果直接生成词云，结局会惨不忍睹（可以亲自试试）。<br>对于中文分词来说，需要使用停用词表。但是我这里用了另一种本办法实现类似的目的，并且可以有选择性的对于前N项的词进行词云制作：

```py
counts = {}      #建立空字典进行计数、排序
for word in words:
    counts[word] = counts.get(word,0) + 1
items = list(counts.items())
items.sort(key = lambda x:x[1],reverse = True)
newitems = []        #筛选掉长度小于2的词
for i in items:
    word1,count = i
    if len(word1) > 1:
        newitems.append(i)
print(newitems)
```
> 吐槽：简书对于MarkDown代码块的文本高亮支持的好差。

生成结果如下：
![前几个分词结果](https://raw.githubusercontent.com/EldenBob/Python-Class-Blog/main/image/class1/3.png)

## 3.综合运用
---
现在有了合理的分词结果，就可以使用WordCloud进行词云制作了：
首先需要将上面的存有分词结果的list通过`dict`转换为字典，然后就可以使用`.generate_from_frequencies`进行词云制作了。代码如下：

```Python
newitems_dict = dict(newitems)
wc=WordCloud(font_path=font,width=800,height=600).generate_from_frequencies(newitems_dict)
plt.imshow(wc)
plt.axis('off')
plt.show()
```
![分词后的结果](https://raw.githubusercontent.com/EldenBob/Python-Class-Blog/main/image/class1/2.png)

当然，这样的图片还是太丑了。老师上课对图片进行了美化，这里有一个教程，详细地介绍了如何通过`Wordcloud`对象进行词云美化。我就不copy代码了，上链接：
[数据美化 | 更清晰的Python词云wordcloud](https://blog.csdn.net/qq_40442753/article/details/109717664)
通过老师的案例和其它的参考，我最终也生成了一张美化的图片：
![中传棒棒](https://raw.githubusercontent.com/EldenBob/Python-Class-Blog/main/image/class1/4.png)

# 二、遇到的问题
---
## 1. 环境和库
---

- #### 为什么我使用 pip 安装了库，但是在 Jupyter 或是 spider 里面仍然现实该库未安装？
  - 注意库的安装环境。我的电脑中，如果使用pip安装了某库，在conda环境中是没办法使用的。
  - 解决方法：使用 conda 命令`anaconda propmt`安装库。

- #### 我的 Jupyter Notebook 默认路径在 C 盘 User 文件夹。咋改？
  - [如何更改Jupyter Notebook的默认工作路径？](https://zhuanlan.zhihu.com/p/59738776) 这篇文章说的不错。但是值得注意的是，如果你是使用 anaconda 中的 Jupyter Notebook ，道理同上，请不要使用cmd命令，而是使用使用 conda 命令`anaconda propmt`。

## 2. 代码错误
---
- #### 简单归纳一些新手（就是我们）容易犯的错误
  - 文件路径错误。要注意区分相对路径和绝对路径。使用`os`查询目前运行的根路径；
  - 报错：该库没有命名为xx的功能。通常情况下，这是由于命名的 .py 文件与库的名字撞了。比如  jieba.py ；
  -  语法不完整，缺少`'`，`:`，括号不闭合等等问题而报错；
  - 缩进问题；
  - 字体路径，字符编码等问题。

# 三、拓展一下
---
## 1. MarkDown 的使用
---
> Markdown 是一种轻量级标记语言，它允许人们使用易读易写的纯文本格式编写文档。
> <br>当前许多网站都广泛使用 Markdown 来撰写帮助文档或是用于论坛上发表消息。例如：GitHub、简书、reddit、Diaspora 等。

这一课程 Blog 本来想写在 B 站专栏，但是奈何 B 站专栏不支持 Markdown，只能使用自带的极其难用的文本编辑器，而我又想体会一下使用 Markdown 写作的快感，于是就使用了简书。

如果方便的话，未来说不定会将我的 Blog 同步到 CSDN 或是 GitHub 上面，应该会很方便。

MarkDown真的很简单而且好用，教程一键直达：[MarkDown | 菜鸟教程](https://www.runoob.com/markdown/md-tutorial.html)。

## 2. Jupyter Note 快捷操作

用快捷键真的很快！快速查表：[jupyter notebook常用快捷键](https://www.cnblogs.com/sui776265233/p/9759303.html)
<br>

---
## See you next time!
