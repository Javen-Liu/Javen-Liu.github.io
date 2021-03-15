---
title: 用PY查找固定代码所在的文件
tags: 
    - 脚本工具
categories:
    - 脚本工具
keywords: "脚本工具"
date: 2020-8-19 16:46
top_img: 
cover: https://gitee.com/Javen-Liu/blogimage/raw/master/img/20200807215836_1.jpg
---

# 缘由

今天给我们项目组的某一个项目集成友盟+的统计。由于该项目之前集成过firebase，然后因为一些原因，firebase被弃用了，相关sdk都删掉了，原来的统计事件都给注释掉了（还好我当时机制，没有删掉，而是给注释上了，否则这次相当于重新找统计的点，更麻烦）。

由于C#文件巨多，一个一个找那怕是要累死我。突然我一想，之前自己学过一点爬虫，写了几个小demo，尤其是正则表达式让我印象深刻。所以我就在想，可不可以用正则，来找出有之前注释掉的代码。

# 具体逻辑

在写这段代码之前，我思考了一下，可能后续会有文件夹，所以在这段代码里我用了递归，如果检测到当前路径是文件夹，则会对当前路径再次调用该方法。

如果是.cs文件，则读取代码内容，让后通过正则表达式匹配，如果匹配上了，则打印当前文件名。

# 代码

```python
import os
import re


def find(path, pattern):
    files = os.listdir(path)
    for file in files:
        filepath = path+'\\'+file
        if os.path.isdir(filepath):
            find(filepath, pattern)
        elif file.endswith(".cs"):
            content = open(filepath, mode='r', encoding='UTF-8').read()
            items = re.findall(pattern, content)
            if len(items):
                print(file)


if __name__ == '__main__':
    # 此处是需要寻找的目标代码
    pattern = re.compile('XXXXXXXX')
    
    # 此处path是需要寻找的文件夹
    find(path=r'XXXXXXXXXXXX', pattern=pattern)
```

# 总结

有的时候，有些较为机械且重复的活，其实是可以借助工具来做，人来做费时又费力，效率不高。

之前在训练测试脚本的时候，也用到了python来写文件转换，比起认为来做，确实方便了不少。

平时在工作学习中，还是要善于用这种思维来考虑事情，对于重复度高的事情，可以借助脚本，增加效率。