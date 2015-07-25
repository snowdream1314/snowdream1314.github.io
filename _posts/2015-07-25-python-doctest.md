---
layout: post
title:  "Python测试之doctest模块"
date:   2015-07-25 23:06:05
categories: python
excerpt: 
---

* content
{:toc}


### 今天看到一个关于贪心算法的帖子，学习一下

* 代码：

        #-*-coding:utf-8-*-

        def sameSums(int_list):
            """
            >>> sameSums([4, 7, 6, 3])
            True
            >>> sameSums([3, 3])
            True
            >>> sameSums([4, 12, 16])
            True
            >>> sameSums([5, 1])
            True
            """
            new_lst = sorted(int_list, reverse=True)
            list1 = list()
            list2 = list()
            for n in new_lst:
                if sum(list1) < sum(list2):
                    list1.append(n)
                else:
                    list2.append(n)
            return sum(list1) == sum(list2)
         
        if __name__ == "__main__":
            import doctest
            doctest.testmod()
            lst = [3, 9, 10, 30, 8]
            print sameSums(lst)

---

####学习点：

---

##### sorted函数用法：

> * sorted 函数用来对给定的List进行排序，它有四个可选参数，这里就介绍参数reverse，它决定排序规则：True降序，False升序

##### 简单的贪心算法：

> * 贪心算法（又称贪婪算法）是指，在对问题求解时，总是做出在当前看来是最好的选择。
也就是说，不从整体最优上加以考虑，他所做出的是在某种意义上的局部最优解。
这个题目：先将list从大到小排序，中间设置2个空的list，从大的开始选，下一次选的时候，需要
比较一下和，如果谁的和小，再添加一个，直到最后一个元素。

##### doctest测试模块：

* 在 Python 的官方文档中，对 doctest 的介绍是这样的：

> * doctest 模块会搜索那些看起来像交互式会话的 Python 代码片段，然后尝试执行并验证结果。（注：即上面代码中引号"""里面的内容）

* doctest测试模块是Python 发行版自带的标准模块，有2种使用方式：嵌入到源代码中和做成独立文件。上面是嵌入到源码中。

>doctest的测试用例就像文档字符串一样，这句话的内涵在于：测试用例的位置必须放在整个模块文件的开头，或者紧接着对象声明语句的下一行。也就是可以被 __doc__ 这个属性引用到的地方。并非像普通注释一样写在哪里都可以。另：verbose 参数用于控制是否输出详细信息，默认为 False，如果不写，那么运行时不会输出任何东西，除非测试 fail。

* 上例中启动测试的方式是在 __main__ 函数里调用了 doctest.testmod() 函数。对于__main__函数另有他用的情况，则还可以通过命令行来启动测试：

        python -m doctest XXX.py
        python -m doctest -v XXX.py
        （XXX为文件名）
        其中 -m 表示引用一个模块，-v 等价于 verbose=True。

* 独立文件模式：

> * 直接把测试用例写在一个独立的文本文件里，如 **example.txt**，运行方法：

        Python shell 里：
        >>> import doctest
        >>> doctest.testfile('example.txt')
        
        或者在系统 shell 里：
        python -m doctest -v example.txt

---

> 参考文章：[如何捅破python编程的那层纸之二](http://www.oschina.net/code/snippet_1448389_49635) 、[Python 各种测试框架简介](http://my.oschina.net/lionets/blog/268542)

---

### 结语：

坚持每天进步一点点...

---