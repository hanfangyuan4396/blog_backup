---
title: python导包与读文件搜索目录
date: 2021-09-04 10:14:38
tags: python
---

有如下目录结构
```
> home
  > main
    - main.py
    - moudule_a.py
    - module_a.txt
    > module
      - module_b.py
```

各文件内容如下
```python
# main.py
from module_a import func_a
from module.module_b import func_b

func_b()
func_a()

```

```python
# module_a.py
def func_a():
    with open('module_a.txt') as f:
        print(f.read())

```

```python
# module_a.txt
module_a

```

```python
# module_b.py
def func_b():
    print('module_b')

```

运行命令
```shell
cd /home/main
pwd # /home/main
python main.py # 正常输出 moudle_b module_a

```

```shell
cd /home
pwd # /home
python main/main.py # 输出 moudle_b 无法输出module_a，报错No such file or directory: 'module_a.txt'

```

这是因为在import导入包的时候，会在当前import代码所在的文件的目录下搜索包，即main.py文件总会在main.py所处的main目录下搜索包

而在读取文件的时候，是在pwd，当前目录下搜索文件，即在home文件夹运行main/main.py时，在home中找不到module_a.txt，所以报错

如何让读取文件不受pwd(终端当前工作目录)影响，需要借助python的__file__变量以及os.path.dirname函数。每个python文件都会有一个__file__的文件变量，这个变量存储着文件所在的路径，os.path.dirname为路径提取函数，它可以从下往上一层一层地嵌套提取目录路径，每调用一次就会得到少一层目录的路径。

module_a.py改成如下代码
```python
import os

def func_a():
    path = os.path.dirname(__file__)
    with open(os.path.join(path, 'module_a.txt')) as f:
        print(f.read())

```

改完后再执行如下命令
```shell
cd /home
pwd # /home
python main/main.py # 正常输出 moudle_b module_a

```