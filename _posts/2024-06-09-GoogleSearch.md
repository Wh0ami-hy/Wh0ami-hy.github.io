---
layout: post   	
catalog: true 	
tags:
    - Google
---



**site:url**

指定网站搜索

例如我要屏蔽谷歌搜索的 csdn.net，修改后的查询网址如下：（加粗部分为新添加的内容） 

{google:baseURL}search?q=+-site:***.csdn.net**+%s&{google:RLZ}{google:originalQueryForSuggestion}{google:assistedQueryStats}{google:searchFieldtrialParameter}{google:iOSSearchLanguage}{google:prefetchSource}{google:searchClient}{google:sourceId}{google:contextualSearchVersion}ie={inputEncoding}{google:baseURL}search?q=%s&{google:RLZ}{google:originalQueryForSuggestion}{google:assistedQueryStats}{google:searchFieldtrialParameter}{google:iOSSearchLanguage}{google:prefetchSource}{google:searchClient}{google:sourceId}{google:contextualSearchVersion}ie={inputEncoding}

**before**

before:2023

**-关键词**

排除法

`tuihou123321 -大前端 -前端面试宝典 -iphone site:segmentfault.com/a/` 实现在`segmentfault.com/a/`中 搜索关键为tuihou123321的内容，并且 排除关键 大前端， 前端面试宝典， iphone关键词 

**"搜索关键词"**

全词搜索，搜索内容中必须包含引号中的内容

