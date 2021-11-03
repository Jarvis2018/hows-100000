# "十万个"怎么做：如何实现word转html

## 是什么

实现将`word`文档转换为带有样式的`html`文档。

## 为什么

<p style="text-indent: 24px">
一般设计到登录注册的地方都会有用户协议或者隐私协议等链接。那这些协议的内容怎么来呢？可能大部分公司是产品给开发
一份`word`文档，然后开发根据内容和样式在手动用代码实现。
</p>
<p style="text-indent: 24px">
如果内容有更改，还得找到哪里修改了，然后再改代码。岂不是占用了划水时间[旺柴]。
</p>


## 怎么做

### 前提 

这里开发语言为`Python`为例。目前找到了以下几个包：

* pydocx（https://github.com/CenterForOpenScience/pydocx）
    
  `pydocx`的转换结果基本是用`hmlt`的标签来替换主要样式，比如标题用`h1`，加粗用`strong`标签。可以将图片转换
    `base64`。而且转换后的代码比较清爽。由于都是用默认标签替换的，所以导致样式不太美观，和`word`里的样式有出入。


* python-docx（https://github.com/python-openxml/python-docx）

    `python-docx`主要是对`Word`的增删改查操作。


* python-mammoth（https://github.com/mwilliamson/python-mammoth）
  
    `python-mammoth`和`pydocx`差不多，但是从转换的代码看，`python-mammoth`转换后的`html`嵌套层级要比`pydocx`少。而且`table`页没有了
    默认边框。代码更加简洁。

* pywin32（https://github.com/mhammond/pywin32）

    `pywin32`该包只能在`windows`下使用。可以操作`Word`另存为指定的文件。优点是保留了完整的样式。缺点就是产生很多无用代码。


由于我需要保留完整的样式，所以选了`pywin32`，之后在将转换后的代码利用正在替换。

### 上手

> 这里只简单列出关键代码

1.首先安装`pywin32`
```shell
 pip install pywin32
```

2.这里主要就是通代码将`Word`另存为`html`

> 打开`Word`，另存为指定的`html`(这里选筛选过的html，这样导出的html会删除一些`Word`自带的样式)

```
    try:
        pythoncom.CoInitialize()
        word = wc.Dispatch("Word.Application")
        word.Visible = 0  # 后台运行，不显示
        word.DisplayAlerts = 0  # 不警告
        # doc_file是Word文档的绝对路径
        doc = word.Documents.Open(doc_file)
        # html_file是存储的html文件绝对路径
        doc.SaveAs(html_file, 10)  # txt=4, html=10, docx=16， pdf=17
    finally:
        if ('doc' in dir()) and doc.Close:
            doc.Close()
        if ('word' in dir()) and word.Quit:
            word.Quit()
```

3. 使用`Flask`或者`Django`web框架搭建一套接口，大概功能就是：前端上传文档，返回转换后的`html`

## 总结

### 遇到的问题

* 如何在`windows`上部署`python`，参考文章：https://blog.csdn.net/bamboo_2001/article/details/108243592

* 报错提示：`pywintypes.com_error: (-2147221008, '尚未调用 CoInitialize。', None, None)`。参考文章：https://blog.csdn.net/zhouf00/article/details/93630823

* 类似的开源项目：https://github.com/sun2dan/stack/blob/master/python/word2html/README.md
