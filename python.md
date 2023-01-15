# python 学习小记

## 字符串前加 r

python 字符串前面加上'r'的作用. 在打开文件的时候 open (r'c:....') 'r'是防止字符转义

## pandas 学习汇总

接触了下 pandas，颇有些心得和自己的积累，记录下留作后用

官网[pandas](https://pandas.pydata.org/)

首先介绍下 pandas，这是一个快速、强大、灵活且易于使用的开源数据分析和操作工具，构建在 Python 编程语言之上。

我目前使用局限在操作 excel，读取一个 excel 文件

```py
import pandas as pd

df = pd.read_excel(excel_file['file_path'],header=None)
```

其中，第一个参数便是文件的路径，header=None 表示第一行不是列名，默认会按照 1，2，3 排布。

```py
dir = r'/home/gdb/gdb/python/index/'
output = r'/home/gdb/gdb/python/output/'

Dfs = []
for root, dirs, files in os.walk(dir):
    for file in files:
        file_path = os.path.join(root,file)
        df = pd.read_excel(file_path,header=None)
        df.insert(loc=1,column='test-3',value='test-value')
        df.insert(loc=1,column='test-2',value='test-value')
        df.insert(loc=1,column='test-1',value='test-value')
        Dfs.append(df)
        print(file+' 完成')
df_all = pd.concat(Dfs)
df_all.to_excel(output+'index.xlsx')
```

这是一段在数据中插入新列，df.insert(loc=1,column='test-3',value='test-value')，其中 loc 是插入的列行，记住，pandas 都是按照 0 开始的，列号也是，插入的原列会向右移动，column 是列名，value 是对应的值，这里我因为需要输入固定值所以只有一个字符串，看官网，应该可以输入其他类型数据。

df_all = pd.concat(Dfs)，这段代码是拼接函数，有行拼接和列拼接，默认是行拼接，拼接方法默认是外拼接

```py
>>> df1 = pd.DataFrame([['a', 1], ['b', 2]],
...                    columns=['letter', 'number'])
>>> df1
  letter  number
0      a       1
1      b       2
>>> df2 = pd.DataFrame([['c', 3], ['d', 4]],
...                    columns=['letter', 'number'])
>>> df2
  letter  number
0      c       3
1      d       4
>>> pd.concat([df1, df2])
  letter  number
0      a       1
1      b       2
0      c       3
1      d       4
```

这是官网的教程，我之前使用 pandas 默认列名：header=None，默认 1，2，3 的列名，用 concat 拼接可以保存所有的数据，只是相同列名下可能出现不同数据。

df_all.to_excel(output+'index.xlsx')，这是生成对应的 excel 数据，数据量大后，用 to_csv()更好，更快。

参考网址：[Python - pandas DataFrame 数据的合并与拼接（merge、join、concat）](https://blog.csdn.net/sc179/article/details/108169436)

```py
data = {
    'user':['zszxz','zszxz','rose']
    ,
    'price':[100,200,300],
    'hobby':['reading','reading','king']
}

frame = pd.DataFrame(data)
new = frame[frame.duplicated(subset=['user'],keep=False)]
print(frame)
print(new)

# 结果
    user  price    hobby
0  zszxz    100  reading
1  zszxz    200  reading
2   rose    300     king
    user  price    hobby
0  zszxz    100  reading
1  zszxz    200  reading
```

frame.duplicated，这个找出重复值的函数，两个参数 subset 和 keep，subset 如果不指定列的话默认读取所有的数据，可以设置多个列，keep 标记重复值，三个选项，first 是除了第一次出现的重复值，其他标记为 true，last 是除了最后一次出现的重复值，其他标记为 true，false 是所有的重复都标记为 true。这个函数返回值是每个行的重复情况（Boolean 值）的 Series（pandas 的一维数据类型）

我在使用时是 new = frame[frame.duplicated(subset=['user'],keep=False)]，返回的是一个包含所有重复的数据的 DataFrame。原理不知

参考网址：[【Python】pandas 重复数据处理大全（附代码）](https://blog.csdn.net/fengdu78/article/details/123267484)

说一个警告

```py
df = pd.read_csv('/home/gdb/gdb/python/index/test.csv',low_memory=False)
```

之前用默认列名，也即是 header=None，结果在读取 csv 时，因为 pandas 是一块一块读取，是靠猜测这块数据中 csv 字段的数据类型，而我的同列不一定是相同数据，结果警告，其实可以略过，但是还是找了下解决方法，一般按照警告的推荐方式，也就是在后面加上 low_memory=False，将整个数据读取，不在分块，按照网上的说法这个方式非常不好，一旦数据量超大，就会内存溢出，不过我的没有出现这个情况。第二个方法就是将警告的列指定数据类型。

参考网址：[DtypeWarning: Columns (1,5,7,16,......) have mixed types. Specify dtype option on import or set low\_...](https://blog.csdn.net/weixin_30326745/article/details/97060519)

## os

python3 os 提供非常多的方法来处理文件和目录

```py
dir = r'/home/gdb/gdb/python/index/'
output = r'/home/gdb/gdb/python/output/'

Dfs = []
for root, dirs, files in os.walk(dir):
    for file in files:
        file_path = os.path.join(root,file)
        df = pd.read_excel(file_path,header=None)
        df.insert(loc=1,column='test-3',value='test-value')
        df.insert(loc=1,column='test-2',value='test-value')
        df.insert(loc=1,column='test-1',value='test-value')
        Dfs.append(df)
        print(file+' 完成')
df_all = pd.concat(Dfs)
df_all.to_excel(output+'index.xlsx')
```

这里我用的是 os.walk

- os.walk() 方法可以创建一个生成器，用以生成所要查找的目录及其子目录下的所有文件。
- os.walk() 方法用于通过在目录树中游走输出在目录中的文件名，向上或者向下。
- os.walk() 方法是一个简单易用的文件、目录遍历器，可以帮助我们高效的处理文件、目录方面的事情。

参考网址：[Python3 os.walk() 方法](https://www.runoob.com/python3/python3-os-walk.html)

我只说下我的用法，root、dirs 和 files 获得，遍历 files 获取文件名，再用 os.path.join()，拼接完整的目录

参考网址：[Python3 os.path() 模块](https://www.runoob.com/python3/python3-os-path.html)
