---
layout: "post"
title: "thegradecade downloader"
date: "2019-01-07 13:26"
---

this is used for crawling application data on the `thegracafe.com`.
```python
import numpy as np
from bs4 import BeautifulSoup
import codecs
import urllib2
import pandas as pd
import csv
import sys
reload(sys)
sys.setdefaultencoding('utf-8')

prefix="https://www.thegradcafe.com/survey/index.php?q=computer+science"
appendix="&t=a&o=&pp=250"

class application(object):
    def __init__(self,inst,prog,dec,st1,date,notes):
        self.inst=inst
        self.prog=prog
        self.dec=dec
        self.st1=st1
        self.date=date
        self.notes=notes

def convert_utf8(string):
    if string and len(string)>=1:
        return string.decode('utf8', 'ignore').strip().lower()
    else:
        return string
import cPickle as pickle

def search_with_universityname(name):
    url=prefix+"+"+name+appendix
    header={'User-Agent':'Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US; rv:1.9.1.6) Gecko/20091201 Firefox/3.5.6',
      'Content-Type': 'application/json;charset=UTF-8'}
    request=urllib2.Request(url,headers=header)
    response=urllib2.urlopen(request,timeout=20)
    content=response.read()
    soup=BeautifulSoup(content,"html.parser")
    table=soup.find("table",{"class":"submission-table"})
    fw=codecs.open('result_'+name+'_phd.csv','w',encoding='utf-8')
    # cw=csv.writer(fw)
    #print table
    apps=[]
    for row in table.findAll("tr"):
        cell=row.findAll("td")
        if len(cell)==6:
            Institition=cell[0].find(text=True)
            Program=cell[1].find(text=True)
            Decision=cell[2].find(text=True)
            St1=cell[3].find(text=True)
            DateAdded=cell[4].find(text=True)
            Notes=cell[5].find(text=True)
            item=[convert_utf8(x) for x in [Institition,Program,Decision,DateAdded,Notes]]
            if 'phd' in item[1]:
                apps.append(item)
    applications=pd.DataFrame(apps)
    applications.to_csv('apps_'+name+"_.csv")
    print('saving to apps_' + name + "_phd.csv")
    fw.close()

if __name__=="__main__":
    search_with_universityname("Toronto")
```
