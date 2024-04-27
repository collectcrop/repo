# MarkDown  

---

转载整理自https://markdown.com.cn/basic-syntax/
## 标题语法  
要创建标题，请在单词或短语前面添加井号 (#) 。# 的数量代表了标题的级别。例如，添加三个 # 表示创建一个三级标题。  

![Error](http://m.qpic.cn/psc?/V51lhW9d4PA0CG0LznEN0Kc5h611DBx1/ruAMsa53pVQWN7FLK88i5tnthLVOm.o4hOR1dMNYK209ziGgrXT4*M4CwQDx2QuT21QR8AcuRobLAcxmNnN8X9xIrAvT0Cm9gUfGoixTTxc!/b&bo=WAPmAgAAAAADB50!&rf=viewer_4&t=5 "Title")

## 段落语法  
要创建段落，请使用空白行将一行或多行文本进行分隔。  

![Error](http://m.qpic.cn/psc?/V51lhW9d4PA0CG0LznEN0Kc5h611DBx1/ruAMsa53pVQWN7FLK88i5ugZ0cPQJKQ3Cd8g1UpvTLaWWOXdhlNQHhlSWoJoOQIvu61oAh1V1lnGETPhZSLlaixHCS0Uxr3T9pF*tpIxeHc!/b&bo=pAM3AQAAAAADB7M!&rf=viewer_4&t=5 "paragraph")

## 换行语法  
在一行的末尾添加两个或多个空格，然后按回车键,即可创建一个换行。  

![Error](http://m.qpic.cn/psc?/V51lhW9d4PA0CG0LznEN0Kc5h611DBx1/ruAMsa53pVQWN7FLK88i5ugZ0cPQJKQ3Cd8g1UpvTLYSJo1EBJ6UpuHAo.fMVZ50qK2l0AgfY.*RlLrDjzGMwL3XsqBlAEFFJkrdUcY2Vxo!/b&bo=oQPEAAAAAAADB0Q!&rf=viewer_4&t=5 "Line break")

## 强调语法  
要加粗文本，请在单词或短语的前后各添加两个星号（asterisks）或下划线（underscores）。如需加粗一个单词或短语的中间部分用以表示强调的话，请在要加粗部分的两侧各添加两个星号（asterisks）。  

![Error](http://m.qpic.cn/psc?/V51lhW9d4PA0CG0LznEN0Kc5h611DBx1/ruAMsa53pVQWN7FLK88i5ugZ0cPQJKQ3Cd8g1UpvTLazv1uZshnJAaslhl0RsUm2RgzR2y8ezhFP222EtOZ8t3L7sfukUHWLsksXv9Feoh4!/b&bo=mwP7AAAAAAADB0E!&rf=viewer_4&t=5 "Bold")

## 引用语法  
要创建块引用，请在段落前添加一个 > 符号。  
如> Dorothy followed her through many of the beautiful rooms in her castle. 效果如下  

> Dorothy followed her through many of the beautiful rooms in her castle.

块引用可以嵌套。在要嵌套的段落前添加一个 >> 符号。

如> Dorothy followed her through many of the beautiful rooms in her castle.  
\>  
\>> The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.  
效果如下  

> Dorothy followed her through many of the beautiful rooms in her castle.  
>  
>> The Witch bade her clean the pots and kettles and sweep the floor and keep the fire fed with wood.  

## 分隔线语法  
要创建分隔线，请在单独一行上使用三个或多个星号 (***)、破折号 (---) 或下划线 (___) ，并且不能包含其他内容。  

## 图片语法  
要添加图像，请使用感叹号 (!), 然后在方括号增加替代文本，图片链接放在圆括号里，括号里的链接后可以增加一个可选的图片标题文本。  
插入图片Markdown语法代码：\!\[图片alt](图片链接 "图片title")。  
对应的HTML代码：\<img src="图片链接" alt="图片alt" title="图片title"\>  
其中图片alt中存的是当图片加载不出来时显示的文本，图片title是鼠标悬停时显示的内容  

给图片增加链接，请将图像的Markdown 括在方括号中，然后将链接添加在圆括号中。  
如\[\!\[沙漠中的岩石图片](/assets/img/shiprock.jpg "Shiprock")](https://markdown.com.cn)

## 链接语法  
链接文本放在中括号内，链接地址放在后面的括号中，链接title可选。

超链接Markdown语法代码：\[超链接显示名](超链接地址 "超链接title")

对应的HTML代码：\<a href="超链接地址" title="超链接title">超链接显示名</a>  
如这是一个链接 \[Markdown语法](https://markdown.com.cn)。  
效果如下  
[Markdown语法](https://markdown.com.cn)。  

可以给链接加一个title，链接title是当鼠标悬停在链接上时会出现的文字，这个title是可选的，它放在圆括号中链接地址后面，跟链接地址之间以空格分隔。  
如 \[Markdown语法](https://markdown.com.cn "title")。  
效果如下  
[Markdown语法](https://markdown.com.cn "title")。  

使用尖括号可以很方便地把URL或者email地址变成可点击的链接。  
如 \<https://markdown.com.cn\>  \<fake@example.com\>  
效果如下  
<https://markdown.com.cn>  <fake@example.com>  

To be continue ...







