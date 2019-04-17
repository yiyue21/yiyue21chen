# yiyue21chen
# -*- coding: utf-8 -*-
"""Python3"""
import numpy  as np
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib
import os
os.chdir(r'F:\yiyue21chen\信用卡客户画像')
clients = pd.read_csv('clients.csv',encoding='gbk')
card = pd.read_csv('card.csv',encoding='gbk')
disp = pd.read_csv('disp.csv',encoding='gbk')
import sqlite3 
con = sqlite3.connect(':memory:')
card.to_sql('card', con)
disp.to_sql('disp', con)
clients.to_sql('clients', con)
car_sql='''
select a.*,c.sex,c.birth_date,c.district_id
  from card as a
  left join disp as b on a.disp_id = b.disp_id
  left join clients as c on b.client_id = c.client_id
  where b.type ="所有者"
'''
card_t = pd.read_sql(car_sql,con)
from pylab import mpl
mpl.rcParams['font.sans-serif'] = [SimHei']
mpl.rcParams['axes.unicode_minus'] = False
from datetime import *
card_t['issued_date']=pd.to_datetime(card_t['issued'])
card_t['issued_year']=card_t['issued_date'].map(lambda x:x.year)
card_t.card_id.groupby(card_t['issued_year']).count().plot(kind="bar")
card_t.type.value_counts().plot(kind="pie",autopct='%.1f%%')
pd.crosstab(card_t.issued_year,card_t.type).plot(kind = 'bar')
t1=pd.crosstab(card_t.issued_year,card_t.type)
t1["sum1"]=t1.sum(1)
t2=t1.div(t1.sum1,axis = 0)
t2.drop("sum1",1).plot(kind = 'bar',stacked= True)
import matplotlib.pyplot as plt
labels=["青年卡","普通卡","金卡"]
y1=t1.loc[:,"青年卡"].astype('int')#astype实现变量类型转换
y2=t1.loc[:,"普通卡"].astype('int')
y3=t1.loc[:,"金卡"].astype('int')
x=t1.index.astype('int')
plt.stackplot(x,y1,y2,y3,labels = labels)
plt.title('发卡趋势')
plt.ylabel('发卡量')
plt.legend(loc = 'upper left')
plt.show()


