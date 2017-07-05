---
title: Crawler Beautiful Soup 4 文档阅读记录
date: 2016-10-23 13:18:12
tags: [Python, Crawler, BeautifulSoup]
category: Python

---

# BeautifulSoup对象

BeautifulSoup 表示一个文档的全部内容

支持：`Navigating the tree`, `Searching the tree`

## 创建BeautifulSoup对象

<!--more-->

### 通过html文本对象 string

    html_doc = """
    <html><head><title>The Dormouse's story</title></head>
    <body>
    <p class="title"><b>The Dormouse's story</b></p>

    <p class="story">Once upon a time there were three little sisters; and their names were
    <a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
    <a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
    <a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
    and they lived at the bottom of a well.</p>

    <p class="story">...</p>
    """

    from bs4 import BeautifulSoup
    soup = BeautifulSoup(html_doc, 'html.parser')

    print(soup.prettify())

### 通过一个html文件 file

    soup_from_file = BeautifulSoup(open('index.html'))

## BeautifulSoup对象的一些简单操作

soup对象的标题

    soup.title
    # <title>The Dormouse's story</title>

soup对象'title'标签标签名

    soup.title.name
    # u'title'

soup对象'title'标签的内容

    soup.title.string
    # u'The Dormouse's story'

soup对象标题的父标签的标签名

    soup.title.parent.name
    # u'head'

soup对象的`p`标签，只获取到第一个`p`标签

    soup.p
    # <p class="title"><b>The Dormouse's story</b></p>

soup对象`p`标签`class`的值

    soup.p['class']
    # u'title'

soup对象的`a`标签，只获取到第一个`a`标签

    soup.a
    # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

soup对象中所有的`a`标签,返回一个list

    soup.find_all('a')
    # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
    #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
    #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup对象中`id="link3"`的标签

    soup.find(id="link3")
    # <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>

获取标签中的某个属性

    for link in soup.find_all('a'):
        print(link.get('href'))

获取文档中所有的内容：

    print(soup.get_text())

# 对象的种类

BeautifulSoup将ntml文档解析成复杂的树形结构，每个节点都是一个Python对象

树形结构对象：

1. Tag
2. NavigableString
3. BeautifulSoup
4. Comment

## Tag

Tag有很多属性和方法

### 获取Tag对象

    tag_obj = soup.b
    type(tag_obj) # <class 'bs4.el ment.Tag'>

### Tag name

获取Tag的名字

    tag_obj.name

改变Tag的名字

    tag_obj.name = 'new_name'

### Attributes
一个tag一般有很多属性

获取属性，根据属性名：

    tag_attr_value = tag_obj['class']

获取tag所有的属性，返回dict

    tag_attrs = tag_obj.attrs

tag属性增删改：

    tag_obj['class'] = 'new_class'
    del tag['class']
    # 增需要指定值
    tag_obj['class'] = ''

### Multi-calued attributes 多值属性
HTML 4定义了一系列可以包含多个值的属性.在HTML5中移除了一些,却增加更多.

获取支持多属性值的属性，返回list：

    css_soup = BeautifulSoup('<p class="body strikeout"></p>')
    css_soup.p['class']
    # ["body", "strikeout"]

如果一个不支持多属性的属性设置成多属性是无效的：

    id_soup = BeautifulSoup('<p id="my id"></p>')
    id_soup.p['id']
    # 'my id'

## NavigableString

BeautifulSoup 使用 NavigableString 包装tag内部的内容：

    tag_obj.string
    type(tag_obj.string)

NavigableString 字符串于 Python 中的 Unicode 字符串相同，并且支持其他的特性。

通过`unicode()`方法将 NavigableString 对象转换成普通的字符串：

    s = unicode(tag_obj.string)
    print(type(s))

NavigableString 不能直接编辑，但是可以使用`replace_with()`替换：

    tag_obj.replace_with("new tag content")

如果想在Beautiful Soup之外使用 NavigableString 对象,需要调用 unicode() 方法,将该对象转换成普通的Unicode字符串,否则就算Beautiful Soup已方法已经执行结束,该对象的输出也会带有对象的引用地址.这样会浪费内存.

## Comments and other special strings 注释和特殊字符串

`Comment` 是一个特殊的  `NavigableString`:

    markup = "<b><!--Hey, buddy. Want to buy a used parser?--></b>"
    soup = BeautifulSoup(markup)
    comment = soup.b.string
    type(comment)
    # <class 'bs4.element.Comment'>

#  Navigating the tree 遍历文档树

## Going down 向下分析，子节点

Tag 里面嵌套了子 Tag

### Navigating using tag names 使用 Tag 名字获取tag

    soup.b
    soup.find_all('b')

### .contents and .children

获取tag中的子tag`.contents`，返回list

BeautifulSoup 对象有一个 children

    len(soup.contents) # 1
    soup.contents[0].name # html

遍历：

    for child in title_tag.children:
        print(child)

### .descendants  包含子孙节点

    for child in head_tag.descendants:
        print(child)
    # <title>The Dormouse's story</title>
    # The Dormouse's story

### .string

1. 如果一个 tag 的没有子tag，但是有内容，那么`.string`表示内容
2. 如果一个 tag 只有一个子tag，那么`.string`表示子tag的内容
3. 如果一个 tag 的 child 由多个，那么`.string`为 None

### .strings and stripped_strings

`.strings`:获取子节点中所有的内容

`stripped_strings`:去除多余的空格或者换行


## Going up 分析父节点

每个 tag 有它的父节点

### .parent

获取父节点`.parent`

    title_tag = soup.title
    title_tag.parent
    # <head><title>The Dormouse's story</title></head>

 `html` tag 的父节点是 BeautifulSoup 对象

     html_tag = soup.html
    type(html_tag.parent)
    # <class 'bs4.BeautifulSoup'>

BeautifulSoup 对象的父节点为 None：

    print(soup.parent)
    # None

### .parents

    link = soup.a
    link
    # <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
    for parent in link.parents:
        if parent is None:
            print(parent)
        else:
            print(parent.name)
    # p
    # body
    # html
    # [document]
    # None

## Going sideways 兄弟节点

### .next_sibling and .previous_sibling

### .next_siblings and .previous_siblings

    for sibling in soup.a.next_siblings:
        print(repr(sibling)) # repr 去除空格

## Going back and forth 回退和前进

按照 BeautifulSoup 的解析过程为流程

HTML解析器把这段字符串转换成一连串的事件: “打开<html>标签”,”打开一个<head>标签”,”打开一个<title>标签”,”添加一段字符串”,”关闭<title>标签”,”打开<p>标签”,等等.

### .next_element and .previous_element

`.next_element`:可能是内容 string，也可能是下一个元素（这个时候和`.next_sibling`结果一样）

### .next_elements 和 .previous_element


# Searching the tree 搜索文档树

## Kinds of filters 过滤器

### string 字符串 过滤器
使用字符串为过滤器

    soup.find_all('b')

### A regular expression 正则表达式作为过滤器
正则表达式对象作为参数

    import re
    for tag in soup.find_all(re.compile("^b")):
        print(tag.name)

### A list 集合作为过滤器
集合内部是 string， 认为其中一个符合就是符合

    soup.find_all(["a", "b"])

### True
可以匹配任何值，但是不会返回字符串节点

### A function 方法作为过滤器

定义一个方法，如果适合就返回 True，不适合就返回 False

    def has_class_but_no_id(tag):
        return tag.has_attr('class') and not tag.has_attr('id')

    soup.find_all(has_class_but_no_id)

    def not_lacie(href):
    return href and not re.compile("lacie").search(href)
    soup.find_all(href=not_lacie)

## find_all()
    def find_all(self, name=None, attrs={}, recursive=True, text=None, limit=None, **kwargs):

搜索当前tag的所有tag子节点,并判断是否符合过滤器的条件.

    import re
    soup.find(text=re.compile("sisters"))
    # u'Once upon a time there were three little sisters; and their names were\n'

### name 参数

查找 tag 的名字，忽略字符串对象

    soup.find_all("title")

### keyword arguments

用于搜索 tag 中的属性

    soup.find_all(id='link2')
    soup.find_all(href=re.compile('elsie'))

属性为 True 表示所有有这个属性的 tag

    soup.find_all(id=True)

但是 HTML 5 中有些属性不能用来作为 keyword

需要将其设置为 dict：

    soup.find_all(attrs={"data-foo": "value"})

### Searching by CSS class 按 CSS 搜索

使用 CSS 搜索 tag

 Beautiful Soup 4.1.2 中使用 `class_` 代替 `class`，因为 class 是 Python 的保留字

 当 tag 中 class 有多个值的时候，在搜索中有一个满足就为满足

 如果同时搜索多个值，其顺序是有要求的，必须和定义的顺序一样。

 If you want to search for tags that match two or more CSS classes, you should use a CSS selector:

    css_soup.find_all("p", class_="strikeout body")

### string arguments
can pass in a string, a regular expression, a list, a function, or the value True

### limit arguments

设置搜索结果返回的最大个数

    soup.find_all("a", limit=2)

### recursive arguments

递归，设置成 False 表示查找一个子项

    soup.html.find_all("title")
    # [<title>The Dormouse's story</title>]

    soup.html.find_all("title", recursive=False)
    # []

## Calling a tag is like calling find_all() 像调用 tag 一样调用 find_all()

    soup.title.find_all(string=True)
    soup.title(string=True)

## find()

    find(name, attrs, recursive, string, **kwargs)

寻找一个结果，下面两个结果一样：但是第一个返回的是list

    soup.find_all('title', limit=1)
    # [<title>The Dormouse's story</title>]

    soup.find('title')
    # <title>The Dormouse's story</title>

如果没有结果 find 返回 None， fang_all 返回 list空

## find_parents() and find_parent()

     find_parents(name, attrs, string, limit, **kwargs)
     find_parent(name, attrs, string, **kwargs)

## find_next_siblings() and find_next_sibling()

## find_previous_siblings() and find_previous_sibling()

## find_all_next() and find_next()

## find_all_previous() and find_previous()

## CSS selectors

Beautiful Soup支持大部分的CSS选择器

在 Tag 或 BeautifulSoup 对象的 .select() 方法中传入字符串参数,即可使用CSS选择器的语法找到tag:

    soup.select("title")
    # [<title>The Dormouse's story</title>]

    soup.select("p nth-of-type(3)")
    # [<p class="story">...</p>]

通过tag标签逐层查找:

    soup.select("body a")
    # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
    #  <a class="sister" href="http://example.com/lacie"  id="link2">Lacie</a>,
    #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

    soup.select("html head title")
    # [<title>The Dormouse's story</title>]

找到某个tag标签下的直接子标签:

    soup.select("head > title")
    soup.select("p > a:nth-of-type(2)")
    soup.select("p > #link1")

找到兄弟节点标签:

    soup.select("#link1 ~ .sister")
    soup.select("#link1 + .sister")

通过CSS的类名查找:

    soup.select(".sister")
    soup.select("[class~=sister]")

通过tag的id查找:

    soup.select("#link1")
    soup.select("a#link2")

通过是否存在某个属性来查找:

    soup.select('a[href]')

通过属性的值来查找:

通过语言设置来查找:

# Modifying the tree

# Output

## Pretty-printing
将Beautiful Soup的文档树格式化后以Unicode编码输出,每个XML/HTML标签都独占一行

BeautifulSoup 对象和它的tag节点都可以调用 prettify() 方法

## Non-pretty printing

输出没有格式的字符串

    unicode()
    str()

You can also call encode() to get a bytestring, and decode() to get Unicode.

## get_text()
只获取内容文本
