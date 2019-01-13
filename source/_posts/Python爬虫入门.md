---
title: Python爬虫入门
date: 2019-01-13 15:34:11
tags: python
---

# 1.下载Python

[https://www.anaconda.com/download/](https://www.anaconda.com/download/)

# 2.爬取《悲伤逆流成河》猫眼评论信息

## 1.猫眼电影短评接口分析

[http://m.maoyan.com/movie/1217236](http://m.maoyan.com/movie/1217236 "电影地址")

通过多次下滑，可以发现一下加载数据的规律：

```
次数      offset
第一次      0
第二次      15
第三次      30
第N次    (n-1)*15
第N+1次    n*15
```

## 2.打印第一页评论数据

C:\20161031\yun\PythonDemo>jupyter notebook

```
import requests
from fake_useragent import UserAgent
import json
import os
import pandas as pd
import csv
ua = UserAgent(use_cache_server=False)
headers={
    'User-Agent':UserAgent(verify_ssl=False).random,
    'Host':'m.maoyan.com',
    'Referer':'http://m.maoyan.com/movie/1217236/comments?_v_=yes'
}

comment_api='http://m.maoyan.com/review/v2/comments.json?movieId=1217236&userId=-1&offset=0&limit=15&ts=0&type=3'
res_comment=requests.get(comment_api,headers=headers)
json_comment=res_comment.text
print(json_comment)
```

## 3.保存评论的第一页数据到work文档中

```
import requests
from fake_useragent import UserAgent
import json
import os
import pandas as pd
import csv
ua = UserAgent(use_cache_server=False)
headers={
    'User-Agent':UserAgent(verify_ssl=False).random,
    'Host':'m.maoyan.com',
    'Referer':'http://m.maoyan.com/movie/1217236/comments?_v_=yes'
}

comment_api='http://m.maoyan.com/review/v2/comments.json?movieId=1217236&userId=-1&offset=0&limit=15&ts=0&type=3'
res_comment=requests.get(comment_api,headers=headers)
json_comment=res_comment.text
json_comment=json.loads(json_comment)
contentList=[]
data=json_comment['data']
comments=data['comments']
for item in comments:
    content=item['content']
    nickName=item['nick']
    contentList.append('nickName: '+nickName)
    contentList.append('content: '+content)

print(contentList)

from docx import Document
def file_do(list_info):
    document = Document()
    for value in list_info:
        document.add_paragraph(value)
    document.save("contentFirstPage.docx")

file_do(contentList)
```

## 4.保存评论的N页数据到work文档中

```
import requests
from fake_useragent import UserAgent
import json
import os
import pandas as pd
import csv
ua = UserAgent(use_cache_server=False)
headers={
    'User-Agent':UserAgent(verify_ssl=False).random,
    'Host':'m.maoyan.com',
    'Referer':'http://m.maoyan.com/movie/1217236/comments?_v_=yes'
}

offset=0
pageNum=int(57675/15)
contentList=[]
for i in range(10):
    comment_api='http://m.maoyan.com/review/v2/comments.json?movieId=1217236&userId=-1&offset={0}&limit=15&ts=1544957345285&type=3'.format(offset)
    res_comment=requests.get(comment_api,headers=headers)
    json_comment=res_comment.text
    json_comment=json.loads(json_comment)
    data=json_comment['data']
    comments=data['comments']
    for item in comments:
        content=item['content']
        nickName=item['nick']
        contentList.append('nickName: '+nickName)
        contentList.append('content: '+content)
    offset=offset+15

print(contentList)

from docx import Document
def file_do(list_info):
    document = Document()
    for value in list_info:
        document.add_paragraph(value)
    document.save("contentNPage.docx")

file_do(contentList)
```