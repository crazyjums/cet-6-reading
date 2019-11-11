#### 对于需要提升英语六级的朋友，我想这个爬虫是最适合你不过了
这是一个爬取英语六级阅读的英文文章，一共99篇，有中文和英文，可以供学习使用，每天读5篇，一个月之后，英语阅读水平一定会提高的。英文来自以下网站：[http://www.en8848.com.cn/cet6/read/liujibidu100/](http://www.en8848.com.cn/cet6/read/liujibidu100/)
爬取的英文文章的内容放在TXT和Word文件中，你们可以各取所需。
本爬虫使用Python语言完成。关键代码如下：

``` Python
# -*- coding: utf-8 -*-
"""
Created on Mon Nov 11 14:29:00 2019

@author: jums
"""

import requests
import re
from bs4 import BeautifulSoup as bs

def get_tile_list():
    useLinks = []
    passageLinks = []
    for page in range(1,5):
        if page != 1: 
     	    url="http://www.en8848.com.cn/cet6/read/liujibidu100/index_{0}.html".format(str(page))
            res = requests.get(url).content.decode("utf-8")
            soup = bs(res,"lxml")
            links = soup.select("a")
            for link in links:
                if "英语六级必读美文" in link.get_text():
                    useLinks.append(link)
                else:
                    pass
        else:
            url = "http://www.en8848.com.cn/cet6/read/liujibidu100/index.html"
            res = requests.get(url).content.decode("utf-8")
            soup = bs(res,"lxml")
            links = soup.select("a")
            for link in links:
                if "英语六级必读美文" in link.get_text():
                    useLinks.append(link)
                else:
                    pass
    
    for link in useLinks:
        passageLinks.append(link.get("href"))
    return passageLinks

def get_one_passage_content(passageUrl):
    res = requests.get(passageUrl).content.decode("utf-8")
    soup = bs(res,"lxml")
    englishContent = soup.select('div[class="qh_en"]')
    chineseContent = soup.select('div[class="qh_zg"]')
    englishPassage = ""
    chinesePassage = ""
    for i in englishContent:
        _englishSoup = bs(str(i),"lxml")
        englishTemp = _englishSoup.select("p")
        if len(englishTemp) != 0:
            englishPassage += englishTemp[0].text.strip()
    for i in chineseContent:
        _chineseSoup = bs(str(i),"lxml")
        chineseTemp = _chineseSoup.select("p")
        if len(chineseTemp) != 0:
            chinesePassage += chineseTemp[0].text.strip()
    
    return {"english":re.sub("\xa0","",englishPassage),"chinese":re.sub("\xa0","",chinesePassage)}

def get_all_passage_contents():
    passageLinks = get_tile_list()
    allPassageContents = []
    for link,i in zip(passageLinks,range(1,len(passageLinks) + 1)):
        allPassageContents.append(get_one_passage_content(link))
        print("get passage content bar:{0}/{1}".format(str(i),str(len(passageLinks))))
        
    return allPassageContents

def write_to_file(filename,content):
    with open(filename,"w") as file:
        for i,j in zip(content,range(1,100)):
            file.write("NO.{0}:".format(str(j)))
            file.write("\n")
            file.write("English:")
            file.write("\n")
            file.write(i["english"])
            file.write("\n")
            file.write("_"*20)
            file.write("\n")
            file.write("Chinese:")
            file.write("\n")
            file.write(i["chinese"])
            file.write("\n")
            file.write("="*20)
            file.write("\n")
            print("write done: {0}/{1}".format(str(j),str(len(content))))
    
if __name__ == '__main__':   
        
    write_to_file("cet-6-reading.txt",get_all_passage_contents())
      
```

