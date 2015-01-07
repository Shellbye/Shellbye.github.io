---
author: shellbye
comments: true
date: 2014-11-27 09:07:38+00:00
layout: post
slug: python-sort
title: python sort
wordpress_id: 1128
categories:
- 技术世界
tags:
- Python
- sort
---

之前写过一个java的[sort笔记](http://www.shellbye.com/blog/%e6%8a%80%e6%9c%af%e4%b8%96%e7%95%8c/java-object-sort/)，今天整理了一些博客和stackoverflow上的问答，总结了如下的python sort，源码在[这里](https://github.com/Shellbye/Core_Python_Programming/blob/master/pysort/views.py)。

pysort.py

{% highlight python %}
__author__ = 'shellbye'


def sort_list():
    a = [3, 6, 8, 2, 78, 1, 23, 45, 9]
    # simple return the result and don't change a
    print sorted(a)
    print a
    # return None and changed a
    print a.sort()
    print a

    b = [3, 6, 8, 2, 78, 1, 23, 45, 9]
    print sorted(b, reverse=True)
    print b

    b.sort(reverse=True)
    print b


def sort_tuple():
    tup = (3, 6, 8, 2, 78, 1, 23, 45, 9)
    # tuple cannot be changed so it does not have the sort method
    print sorted(tup)


def sort_list_by_inside_list():
    # tuple is the same
    def get_first_key(item):
        return item[0]

    def get_second_key(item):
        return item[1]

    l = [[2, 3], [6, 7], [3, 34], [24, 64], [1, 43]]
    print sorted(l, key=get_first_key)
    print sorted(l, key=get_second_key)


def sort_list_by_inside_list_with_lambda():
    # tuple is the same
    l = [[2, 3], [6, 7], [3, 34], [24, 64], [1, 43]]
    print sorted(l, key=lambda item: item[0])
    print sorted(l, key=lambda item: item[1])


def sort_list_by_object():
    # http://www.pythoncentral.io/how-to-sort-a-list-tuple-or-object-with-sorted-in-python/
    class Custom(object):
        def __init__(self, name, number):
            self.name = name
            self.number = number

        def __repr__(self):
            return self.name + "--" + str(self.number)

    def get_key(custom):
        return custom.number

    custom_list = [
        Custom('object', 99),
        Custom('michael', 1),
        Custom('theodore the great', 59),
        Custom('life', 42)
    ]
    print sorted(custom_list, key=get_key)


def sort_list_by_different_object():
    # http://www.pythoncentral.io/how-to-sort-a-list-tuple-or-object-with-sorted-in-python/
    class Custom(object):
        def __init__(self, name, number):
            self.name = name
            self.number = number

        def __repr__(self):
            return self.name + "--" + str(self.number)

        def __cmp__(self, other):
            if hasattr(other, 'get_key'):
                return self.get_key().__cmp__(other.get_key())

        def get_key(self):
            return self.number

    class AnotherObject(object):
        def __init__(self, tag, age, rate):
            self.tag = tag
            self.age = age
            self.rate = rate

        def __repr__(self):
            return self.tag + "--" + str(self.age)

        def __cmp__(self, other):
            if hasattr(other, 'get_key'):
                return self.get_key().__cmp__(other.get_key())

        def get_key(self):
            return self.age

    custom_list = [
        Custom('object', 99),
        Custom('michael', 1),
        Custom('theodore the great', 59),
        Custom('life', 42),
        AnotherObject('bananas', 37, 2.2),
        AnotherObject('pants', 73, 5.6),
        AnotherObject('lemur', 44, 9.2)
    ]

    print sorted(custom_list)


def sort_list_by_inside_dict():
    dl = [
        {"name": "shellbye", "grade": 99},
        {"name": "jack", "grade": 98},
        {"name": "lee", "grade": 97},
    ]

    print sorted(dl, key=lambda u: u['grade'])


def sort_dict_by_value():
    doc = {
        'key0': 98,
        'key1': 97,
        'key2': 5,
        'key3': 60,
        'key4': -9,
    }
    print doc.get
    sorted_doc = sorted(doc.items(), key=lambda t: t[1])
    print sorted_doc


def sort_dict_by_value_advance():
    # http://stackoverflow.com/questions/575819/sorting-dictionary-keys-in-python
    # http://stackoverflow.com/questions/613183/sort-a-python-dictionary-by-value
    # http://stackoverflow.com/questions/9285930/methods-for-sorting-a-dictionary
    doc = {
        'key0': 98,
        'key1': 97,
        'key2': 5,
        'key3': 60,
        'key4': -9,
    }
    print doc
    sorted_doc = sorted(doc, key=doc.get)
    print sorted_doc


def sort_dict_by_key():
    doc = {
        'key0': 98,
        'key1': 97,
        'key2': 5,
        'key3': 60,
        'key4': -9,
    }
    print doc
    sorted_doc = sorted(doc.keys())
    print sorted_doc


if __name__ == "__main__":
    print "Let's sort!"
    # sort_list()
    # sort_tuple()
    # sort_list_by_inside_list()
    # sort_list_by_inside_list_with_lambda()
    # sort_list_by_object()
    # sort_list_by_different_object()
    # sort_list_by_inside_dict()
    # sort_dict_by_value()
    # sort_dict_by_value_advance()
    sort_dict_by_key()
{% endhighlight %}


参考：

[1].[http://www.pythoncentral.io/how-to-sort-a-list-tuple-or-object-with-sorted-in-python/](http://www.pythoncentral.io/how-to-sort-a-list-tuple-or-object-with-sorted-in-python/)

[2].[http://stackoverflow.com/questions/72899/how-do-i-sort-a-list-of-dictionaries-by-values-of-the-dictionary-in-python](http://stackoverflow.com/questions/72899/how-do-i-sort-a-list-of-dictionaries-by-values-of-the-dictionary-in-python)

[3].[http://stackoverflow.com/questions/575819/sorting-dictionary-keys-in-python#](http://stackoverflow.com/questions/575819/sorting-dictionary-keys-in-python#)

[4].[http://stackoverflow.com/questions/613183/sort-a-python-dictionary-by-value](http://stackoverflow.com/questions/613183/sort-a-python-dictionary-by-value)

[5].[http://stackoverflow.com/questions/9285930/methods-for-sorting-a-dictionary](http://stackoverflow.com/questions/9285930/methods-for-sorting-a-dictionary)
