---
layout: post
title:  "Python之unittest模块"
date:   2015-07-26 12:30:05
categories: python
excerpt: 
---

* content
{:toc}


### 学习一下unittest测试模块

---

####概述：

* unittest 与 doctest 一样也是 Python 发行版自带的包。doctest 不管是看起来还是用起来都显得十分简单，可以与源码写在一起，比较适合用作验证性的功能测试。而本篇的 unittest 从名字上看，它是一个单元测试框架；从官方文档的字数上看，它的能力应该比 doctest 强一些。

* 使用 unittest 的标准流程为：

 - 从 unittest.TestCase 派生一个子类
 
 - 在类中定义各种以 “test_” 打头的方法
 
 - 通过 unittest.main() 函数来启动测试
 
> unittest 的一个很有用的特性是 TestCase 的 setUp() 和 tearDown() 方法，它们提供了为测试进行准备和扫尾工作的功能，听起来就像上下文管理器一样。这种功能很适合用在测试对象需要复杂执行环境的情况下。

* 贴一段写的代码，已上传至github上的[firstweb](https://github.com/snowdream1314/firstweb/tree/master/flasky)

        #-*-coding:utf-8-*-
        #-------------------------------------
        # Name: 单元测试
        # Purpose: 
        # Author:
        # Date:
        #-------------------------------------

        import unittest
        from flask import current_app
        from app import create_app, db


        class BasicsTestCase(unittest.TestCase):
            def setUp(self):    #创建测试环境，包括测试实例，上下文和数据库对象
                self.app = create_app('testing')
                self.app_context = self.app.app_context()
                self.app_context.push()
                db.create_all()

            def tearDown(self):  #删除数据库和程序上下文
                db.session.remove()
                db.drop_all()
                self.app_context.pop()

            def test_app_exists(self):  #确保程序实例存在
                self.assertFalse(current_app is None)

            def test_app_is_testing(self):  #确保程序在测试配置中运行
                self.assertTrue(current_app.config['TESTING'])

* 这个例子里，我们使用了 assertTrue() 方法。unittest 中还有很多类似的 assert 方法，比如 NotEqual、Is(Not)None、True(False)、Is(Not)Instance 等针对变量值的校验方法；另外还有一些如 assertRaises()、assertRaisesRegex() 等针对异常、警告和 log 的检查方法；以及如 assertAlmostEqual() 等一些奇怪的方法。

较详细的 assert 方法可以参考 unittest 的[文档页面](https://docs.python.org/3/library/unittest.html?highlight=test#assert-methods)。

---

####启动测试：

* 上面是整个测试模块的一部分，所以没有单独的启动测试，而是在[启动脚本](https://github.com/snowdream1314/firstweb/blob/master/flasky/manage.py)模块里用了TestLoader.discover() 方法，这个后面会讲到。

* 上例中的结尾处，我们可以加一个对 unittest.main() 的调用： 

        if __name__ == '__main__':
            unittest.main()
            
* 这样这个脚本就可以直接运行：

        python test_app_test.py
        ..
        --------------------------------------
        Ran 2 tests in 0.01s

        OK
        
---

####Test Discovery：

* Test Discovery 的作用是：假设你的项目文件夹里面四散分布着很多个测试文件。当你做回归测试的时候，一个一个地执行这些测试文件就太麻烦了。TestLoader.discover() 提供了一个可以在项目目录下自动搜索并运行测试文件的功能，并可以直接从命令行调用：

        cd project_directory
        python -m unittest discover
        
* discover 可用的参数有 4 个（-v -s -p -t），其中 -s 和 -t 都与路径有关，如上例中提前 cd 到项目路径的话这俩参数都可以无视；-v 喜闻乐见；-p 是 --pattern 的缩写，可用于匹配某一类文件名。

---

##### 测试环境

> * 当类里面定义了 setUp() 方法的时候，测试程序会在执行**每条测试项**前先调用此方法；同样地，在全部测试项执行完毕后，tearDown() 方法也会被调用。

* 验证一下：

        import unittest

        class simple_test(unittest.TestCase):
            def setUp(self):
                self.foo = list(range(10))

            def test_1st(self):
                self.assertEqual(self.foo.pop(),9)

            def test_2nd(self):
                self.assertEqual(self.foo.pop(),9)

        if __name__ == '__main__':
            unittest.main()
            
* 注意这里两次测试均对同一个实例属性 self.foo 进行了 pop() 调用，但测试结果均为 pass，即说明，test_1st 和 test_2nd 在调用前都分别调用了一次 setUp()。

* 那如果我们想全程只调用一次 setUp/tearDown 该怎么办呢？就是用 setUpClass() 和 tearDownClass() 类方法啦。注意使用这两个方法的时候一定要用 @classmethod 装饰器装饰起来：

* 再来验证一下：

        import unittest

        class simple_test(unittest.TestCase):
            @classmethod
            def setUpClass(self):
                self.foo = list(range(10))

            def test_1st(self):
                self.assertEqual(self.foo.pop(),9)

            def test_2nd(self):
                self.assertEqual(self.foo.pop(),8)

        if __name__ == '__main__':
            unittest.main()
    
* 这个例子里我们使用了一个类级别的 setUpClass() 类方法，并修改了第二次 pop() 调用的预期返回值。运行结果显示依然是全部通过，即说明这次在全部测试项被调用前只调用了一次 setUpClass()。

* 再往上一级，我们希望在整个文件级别上只调用一次 setUp/tearDown，这时候就要用 setUpModule() 和 tearDownModule() 这两个函数了，注意是函数，与 TestCase 类同级：

        import unittest

        def setUpModule():
            pass

        class simple_test(inittest.TestCase):
            ...
            
---
            
##### 测试异常：

> 一般 assert*() 方法如果抛出了未被捕获的异常，那么这条测试用例会被记为 fail，测试继续进行。但如果异常发生在 setUp() 里，就会认为测试程序自身存在错误，后面的测试用例和 tearDown() 都不会再执行。即，tearDown() 仅在 setUp() 成功执行的情况下才会执行，并一定会被执行。

---

> 参考文章：[Python 各种测试框架简介](http://my.oschina.net/lionets/blog/268704)、我的Github上的小博客程序[Flasky](https://github.com/snowdream1314/firstweb/tree/master/flasky)

---

### 结语：

坚持每天进步一点点...

---