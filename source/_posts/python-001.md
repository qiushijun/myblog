---
title: Python学习笔记
date: 2017-12-17 22:24:20
tags: 
	- Python
	- 学习笔记
	- 脚本语言
---
### Python学习笔记
斐波那契数列实验
```
x = int(input('请输入一个数字: '))
def test():
    n1 = 0
    n2 = 1
    counter = 0
    list = [n1,n2]
    while counter < x :
        nth = n1 + n2
        n1 = n2
        n2 = nth
        counter +=1
        list.append(nth)
    print(list)

test()

def test1():
    n1,n2=0,1
    list = [n1,n2]
    for i in range(0,x):
        nth = n1 +n2
        n1 = n2
        n2 = nth
        list.append(nth)
        #print(nth)
    print (list)

test1()
```
