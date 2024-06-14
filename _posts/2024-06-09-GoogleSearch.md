---
layout: post   	
catalog: true 	
tags:
    - Google
---



**site:url 指定网站搜索**

指定网站搜索

例如我要屏蔽谷歌搜索的 csdn.net，修改后的查询网址如下：（加粗部分为新添加的内容） 

{google:baseURL}search?q=+-site:***.csdn.net**+%s&{google:RLZ}{google:originalQueryForSuggestion}{google:assistedQueryStats}{google:searchFieldtrialParameter}{google:iOSSearchLanguage}{google:prefetchSource}{google:searchClient}{google:sourceId}{google:contextualSearchVersion}ie={inputEncoding}{google:baseURL}search?q=%s&{google:RLZ}{google:originalQueryForSuggestion}{google:assistedQueryStats}{google:searchFieldtrialParameter}{google:iOSSearchLanguage}{google:prefetchSource}{google:searchClient}{google:sourceId}{google:contextualSearchVersion}ie={inputEncoding}

**before搜索某个时间节点前的**

`before:2023`

**搜索指定文件类型**

使用这个技巧，可以快速帮你找到各个格式的文件。

输入：filetype:文档格式 搜索词

比如：filetype:pdf 疫情防控

**在某个时间范围内搜索**

如果你想找出某段时间内的搜索结果，可以在搜索引擎中输入：`搜索词 年份..年份（搜索词和年份之间有空格）`。

比如：`扩展迷油猴脚本 2020..2021`

**搜索相关网站**

想要知道与某个网站相关的网站还有哪些？你仅需在搜索引擎中输入`related:网址`，就可以了。

比如：`related:www.google.com`

**在网页标题、链接和正文中搜索**

如果你需要找出和搜索词相关的所有网页标题、链接和正文，只需要按下方规则搜索即可：

把搜索范围限定在网页标题中——`intitle:搜索词`

把搜索范围限定在url链接中——`inurl:搜索词`

把搜索范围限定在正文中——`intext:搜索词`

例如，在搜索引擎中输入：`intitle: 扩展迷安装教程`

**站内搜索**

众所周知，许多网站上内容虽然丰富，但欠缺了站内搜索的入口，当我们想要查找网站上的某个资源的话就比较麻烦了。

实际上，搜索引擎也可以帮你完成站内搜索的工作。只需输入：`搜索词 site:网址`

比如搜索：`下载工具 site:www.extfans.com`

或：`site:www.extfans.com 下载工具`

**通配符**

`*`，星号，通配符，可以用作模糊搜索。

如果我们忘记了需要搜索的词句的某一部分，就可以用`*`代替缺失的部分。

比如：`扩*迷Extfans`

**排除指定关键词**

如果在进行准确搜索时没有找到自己想要的结果，你还可以对包含特定词汇的信息进行排除，仅需使用减号，即 `-`就可以了（注意减号前有空格）。

使用排除符号搜索：`扩展迷 -哔哩哔哩`

**OR运算符**

与AND相反，OR可以返回你输入的多个搜索词分别相关的结果，而不仅仅是和多个搜索词都同时相关的结果。

巧妙使用OR运算符，可以让你在未能确定哪个关键词对于搜索结果起决定作用时，依然可以确保搜索结果的准确性。

比如：`扩展迷 OR python`

**AND运算符**

很多人不知道的是，绝大部分搜索引擎都允许在搜索中使用逻辑运算符。

如果你想查询同时包含多个搜索词的所有站点，只需要在搜索引擎中输入：`搜索词 AND 搜索词`。

搜索引擎则会返回包含两者的搜索结果。

比如：`扩展迷 AND 下载`

**强制精确匹配**

当你输入某个长句或者短语进行搜索后，搜索引擎会默认显示所有分别包含各个单词的相关信息。

要么就是，只匹配到了你输入的部分字词，这就很令人糟心了。

其实这时候，你可以使用引号来强制完全匹配所有字符。

如：`"what is extension"`