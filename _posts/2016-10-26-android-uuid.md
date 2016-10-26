---
layout: post
title:  "Andriod UUID"
date:   2016-10-26 17:48:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### JAVA UUID 生成

* GUID（Globally Unique Identifier）是UUID的别名；但在实际应用中，GUID通常是指微软实现的UUID。GUID是一个128位长的数字，一般用16进制表示。算法的核心思想是结合机器的网卡、当地时间、一个随即数来生成GUID。从理论上讲，如果一台机器每秒产生10000000个GUID，则可以保证（概率意义上）3240年不重复。

* UUID是1.5中新增的一个类，在java.util下，用它可以产生一个号称全球唯一的ID

        import java.util.UUID;

        public class UTest {
            public static void main(String[] args) {
                UUID uuid = UUID.randomUUID();
                System.out.println(uuid);
            }
        }

#### UUID特点

* 经由一定的算法机器生成

    - 为了保证UUID的唯一性，规范定义了包括网卡MAC地址、时间戳、名字空间（Namespace）、随机或伪随机数、时序等元素，以及从这些元素生成UUID的算法。UUID的复杂特性在保证了其唯一性的同时，意味着只能由计算机生成。

* 非人工指定，非人工识别

    - UUID是不能人工指定的，除非你冒着UUID重复的风险。UUID的复杂性决定了“一般人“不能直接从一个UUID知道哪个对象和它关联。

* 在特定的范围内重复的可能性极小

    - UUID的生成规范定义的算法主要目的就是要保证其唯一性。但这个唯一性是有限的，只在特定的范围内才能得到保证，这和UUID的类型有关（参见UUID的版本）。

* UUID是16字节128位长的数字，通常以36字节的字符串表示

        3F2504E0-4F89-11D3-9A0C-0305E82C3301

        其中的字母是16进制表示，大小写无关。

#### UUID版本

* UUID具有多个版本，每个版本的算法不同，应用范围也不同。

* UUID Version 1：基于时间的UUID

> 基于时间的UUID通过计算当前时间戳、随机数和机器MAC地址得到。由于在算法中使用了MAC地址，这个版本的UUID可以保证在全球范围的唯一性。但与此同时，使用MAC地址会带来安全性问题，这就是这个版本UUID受到批评的地方。如果应用只是在局域网中使用，也可以使用退化的算法，以IP地址来代替MAC地址－－Java的UUID往往是这样实现的（当然也考虑了获取MAC的难度）。

* UUID Version 2：DCE安全的UUID

> DCE（Distributed Computing Environment）安全的UUID和基于时间的UUID算法相同，但会把时间戳的前4位置换为POSIX的UID或GID。这个版本的UUID在实际中较少用到。

* UUID Version 3：基于名字的UUID（MD5）

> 基于名字的UUID通过计算名字和名字空间的MD5散列值得到。这个版本的UUID保证了：相同名字空间中不同名字生成的UUID的唯一性；不同名字空间中的UUID的唯一性；相同名字空间中相同名字的UUID重复生成是相同的。

* UUID Version 4：随机UUID

> 根据随机数，或者伪随机数生成UUID。这种UUID产生重复的概率是可以计算出来的，但随机的东西就像是买彩票：你指望它发财是不可能的，但狗屎运通常会在不经意中到来。

* UUID Version 5：基于名字的UUID（SHA1）

> 和版本3的UUID算法类似，只是散列值计算使用SHA1（Secure Hash Algorithm 1）算法。


#### UUID应用

* 从UUID的不同版本可以看出，Version 1/2适合应用于分布式计算环境下，具有高度的唯一性；Version 3/5适合于一定范围内名字唯一，且需要或可能会重复生成UUID的环境下；至于Version 4，我个人的建议是最好不用（虽然它是最简单最方便的）。

* 通常我们建议使用UUID来标识对象或持久化数据，但以下情况最好不使用UUID：

    - 映射类型的对象。比如只有代码及名称的代码表。
    - 人工维护的非系统生成对象。比如系统中的部分基础数据。

* 对于具有名称不可重复的自然特性的对象，最好使用Version 3/5的UUID。比如系统中的用户。如果用户的UUID是Version 1的，如果你不小心删除了再重建用户，你会发现人还是那个人，用户已经不是那个用户了。（虽然标记为删除状态也是一种解决方案，但会带来实现上的复杂性。）


#### UUID生成器

* Java UUID Generator (JUG)：开源UUID生成器，LGPL协议，支持MAC地址。

* [UUID](http://johannburkard.de/blog/programming/java/Java-UUID-generators-compared.html)：特殊的License，有源码。

* Java 5以上版本中自带的UUID生成器：好像只能生成Version 3/4的UUID。

* [在线生成器](http://www.uuid.online/)

---

> 参考文章：[深入解析UUID及其应用](http://blog.csdn.net/yipiankongbai/article/details/25243531)

---

### 结语：

坚持每天进步一点点...

---