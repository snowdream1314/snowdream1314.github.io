---
layout: post
title:  "Python之itertools模块"
date:   2015-07-26 10:06:05
categories: python
excerpt: 
---

* content
{:toc}


### 接着昨天关于贪心算法的帖子，学习一下

* 代码：

        #-*-coding:utf-8-*-

        import itertools
  
        def get_all_subset(lst):
            """求list 所有子集合"""
            tmp_lst = []
            length = len(lst)
         
            for i in xrange(1, length):#通过for循环列出所有的子集合
                tmp_lst += (set(itertools.combinations(lst, i)))
            return tmp_lst
                  
        def sameSums(int_list):
            """
         
            >>> sameSums([4, 7, 6, 3])
            True
            >>> sameSums([3, 3])
            True
            >>> sameSums([4, 12, 16])
            True
            >>> sameSums([5, 1])
            False
            """
            sum_of_lsit = sum(int_list)
            sub_sum = sum_of_lsit / 2
            if sum_of_lsit % 2:
                return False
            all_subset = get_all_subset(int_list)
            for item in all_subset:
                if sum(item) == sub_sum:
                    return True
            return False
         
        if __name__ == "__main__":
            import doctest
            doctest.testmod()
            lst1 = [3, 9, 10, 30, 8]
            lst2 = [4, 5, 6, 7, 8]
            lst3 = [2, 2, 2, 3, 3]
            print sameSums(lst1)
            print sameSums(lst2)
            print sameSums(lst3)

---

####学习点：

---

##### itertools用法：

* 这里主要简单介绍itertools模块的排列组合：

        import itertools
        
        有序排列：e.g., 4个数内选2个排列：
        print list(itertools.permutations([1,2,3,4],2))
        #[(1, 2), (1, 3), (1, 4), (2, 1), (2, 3), (2, 4), (3, 1), (3, 2), (3, 4), (4, 1), (4, 2), (4, 3)]
        
        无序组合：e.g.,4个数内选2个：
        print list(itertools.combinations([1,2,3,4],2))
        #[(1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)]
    

##### set函数set()函数返回列表中不同的元素组成的集合（Python 集合不允许重复）

> doctest模块参见[Python测试之doctest模块](http://snowdream1314.github.io/2015/07/25/python-doctest/)

> 参考文章：[如何捅破python编程的那层纸之三](http://www.oschina.net/code/snippet_1448389_49691) 

---

### 结语：

坚持每天进步一点点...

---