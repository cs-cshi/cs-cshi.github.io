---
title: pandas
date: 2021-05-02 19:00
tags: 
      - python
categories: 
- [编程语言, python]
---
pandas 学习

<!--more-->

# 1 | Pandas 介绍
- 以 Numpy 为基础，借力 Numpy 模块在计算方面性能高的优势
- 封装了Matplotlib、Numpy的画图和计算
- 独特的数据结构，便捷的数据处理能力
- 读取文件方便

# 2 | Pandas 数据结构
## 2.1 Series
Series是一个类似于一维数组的数据结构，它能够保存任何类型的数据，比如整数、字符串、浮点数等，主要由一组数据和与之相关的索引两部分构成。
![Series](Series.png)

### 2.1.1 Series的创建
```python
# 导入pandas
import pandas as pd

pd.Series(data=None, index=None, dtype=None)
```
- 参数
  - data：传入的数据，可以是 ndarray、list 等
  - index：索引，必须是唯一的，且与数据的长度相等。如果没有传入索引参数，则默认会自动创建一个从 0-N 的整数索引。
  - dtype：数据的类型
  
- 通过已有数据创建
  - 指定内容，默认索引
  ```python
  pd.Series(np.arange(10))

  # 结果
  0    0
  1    1
  2    2
  3    3
  4    4
  5    5
  6    6
  7    7
  8    8
  9    9
  dtype: int64
  ```
  - 指定索引
  ```python
  pd.Series([6.7,5.6,3,10,2], index=[1,2,3,4,5])

  # 结果
  1     6.7
  2     5.6
  3     3.0
  4    10.0
  5     2.0
  dtype: float64
  ```
  - 通过字典数据创建
  ```python
  pd.Series({'red':100, 'blue':200, 'green': 500, 'yellow':1000})

  # 运行结果
  blue       200
  green      500
  red        100
  yellow    1000
  dtype: int64
  ```

### 2.1.2 Series的属性
**Series 中提供了两个属性: index 和 values**
- index
  ```python
  color_count.index

  # 结果
  Index(['blue', 'green', 'red', 'yellow'], dtype='object')
  ```
- values
  ```python
  color_count.values

  # 结果
  array([ 200,  500,  100, 1000])
  ```
  也可以使用索引来获取数据：
  ```python
  color_count[2]

  # 结果
  100
  ```

## 2.2 DataFrame
DataFrame是一个类似于二维数组或表格(如excel)的对象，既有行索引，又有列索引
- 行索引，表明不同行，横向索引，叫index，0轴，axis=0
- 列索引，表名不同列，纵向索引，叫columns，1轴，axis=1
  ![df](df.png)

### 2.2.1 DataFrame的创建
```python
# 导入pandas
import pandas as pd

pd.DataFrame(data=None, index=None, columns=None)
```
- 参数：
  - index：行标签。如果没有传入索引参数，则默认会自动创建一个从 0-N 的整数索引。
  - columns：列标签。如果没有传入索引参数，则默认会自动创建一个从 0-N 的整数索引。
- 通过已有数据创建
  ```python
  pd.DataFrame(np.random.randn(2,3))
  ```
  ![dataframe创建举例](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/dataframe创建举例.png)
- 创建学生成绩表（举例）
```python
# numpy
# 生成10名同学，5门功课的数据
score = np.random.randint(40, 100, (10, 5))

# 结果
array([[92, 55, 78, 50, 50],
       [71, 76, 50, 48, 96],
       [45, 84, 78, 51, 68],
       [81, 91, 56, 54, 76],
       [86, 66, 77, 67, 95],
       [46, 86, 56, 61, 99],
       [46, 95, 44, 46, 56],
       [80, 50, 45, 65, 57],
       [41, 93, 90, 41, 97],
       [65, 83, 57, 57, 40]])

```
```python
# 使用Pandas中的数据结构
score_df = pd.DataFrame(score)
```
![score1](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/score1.png)
增加行、列索引
```python
# 构造行索引序列
subjects = ["语文", "数学", "英语", "政治", "体育"]

# 构造列索引序列
stu = ['同学' + str(i) for i in range(score_df.shape[0])]

# 添加行索引
data = pd.DataFrame(score, columns=subjects, index=stu)
```
![score2](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/score2.png)

### 2.2.2 DataFrame 的属性
- shape
  ```python
  data.shape

  # 结果
  (10, 5)
  ```
- index
  DataFrame 的行索引列表
  ```python
  data.index

  # 结果
  Index(['同学0', '同学1', '同学2', '同学3', '同学4', '同学5', '同学6', '同学7', '同学8', '同学9'], dtype='object')
  ```
- columns
  DataFrame 的列索引列表
  ```python
  data.columns

  # 结果
  Index(['语文', '数学', '英语', '政治', '体育'], dtype='object')
  ```
- values
  直接获取其中 array 的值
  ```python
  data.values

  array([[92, 55, 78, 50, 50],
         [71, 76, 50, 48, 96],
         [45, 84, 78, 51, 68],
         [81, 91, 56, 54, 76],
         [86, 66, 77, 67, 95],
         [46, 86, 56, 61, 99],
         [46, 95, 44, 46, 56],
         [80, 50, 45, 65, 57],
         [41, 93, 90, 41, 97],
         [65, 83, 57, 57, 40]])
  ```
- T
  转置
  ```python
  data.T
  ```
- head(n)
  显示前 n 行，如果不填参数，默认 5 行
  ```python
  data.head(5)
  ```
- tail(n)
  显示后 n 行，如果不填参数，默认 5 行
  ```python
  data.tail()
  ```

### 2.2.3 DataFramle 索引设置
- 修改行列索引值
  ```python
  stu = ["学生_" + str(i) for i in range(score_df.shape[0])]

  # 必须整体全部修改
  data.index = stu

  # 错误修改方式
  data.index[3] = '学生_3'
  ```
  ![score修改索引](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/score修改索引.png)

- 重设索引
  - reset_index(drop=False)
    - 设置新的下标索引
    - drop：默认为False，不删除原来索引，如果为True,删除原来的索引值
  ```python
  # 重置索引,drop=False
  data.reset_index()

  # 重置索引,drop=True
  .data.reset_index(drop=True)
  ```
  ![重设索引1](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/重设索引1.png)

- 以某列值设置为新的索引
  - set_index(keys, drop=True)
    - keys : 列索引名成或者列索引名称的列表
    - drop : boolean, default True.当做新的索引，删除原来的列
  ```python
  df = pd.DataFrame({'month': [1, 4, 7, 10],
                    'year': [2012, 2014, 2013, 2014],
                    'sale':[55, 40, 84, 31]})

     month  sale  year
  0  1      55    2012
  1  4      40    2014
  2  7      84    2013
  3  10     31    2014
  ```
  以月份设置新的索引
  ```python
  df.set_index('month')

  # 结果
         sale  year
  month
  1      55    2012
  4      40    2014
  7      84    2013
  10     31    2014

  # df.index,注意 month 为 name 了
  Int64Index([1, 4, 7, 10], dtype='int64', name='month')

  # df.columns
  Index(['year', 'sale'], dtype='object')
  ```
  设置多个索引，以年和月份
  ```python
  df = df.set_index(['year', 'month'])
  
  # 结果
              sale
  year  month
  2012  1     55
  2014  4     40
  2013  7     84
  2014  10    31

  # df.index
  MultiIndex([(2012,  1),
            (2014,  4),
            (2013,  7),
            (2014, 10)],
           names=['year', 'month'])

  # df.columns
  Index(['sale'], dtype='object')
  ```
  > 注意：DataFrame 已经变成了一个具有MultiIndex的DataFrame

## 2.3 MultiIndex
MultiIndex是三维的数据结构;

多级索引（也称层次化索引）是pandas的重要功能，可以在Series、DataFrame对象上拥有2个以及2个以上的索引。

### 2.3.1 multiIndex的特性
```python
df = pd.DataFrame({'month': [1, 4, 7, 10],
                    'year': [2012, 2014, 2013, 2014],
                    'sale':[55, 40, 84, 31]})
df = df.set_index(['year', 'month'])
print(df.index)

# 结果
MultiIndex([(2012,  1),
          (2014,  4),
          (2013,  7),
          (2014, 10)],
         names=['year', 'month'])

df.index.levels
# 结果
[[2012, 2013, 2014], [1, 4, 7, 10]]

df.index.names
# 结果
['year', 'month']
```
多级或分层索引对象。
- index属性
  - names:levels的名称
  - levels：每个level的元组值

### 2.3.2 multiIndex的创建
```python
arrays = [[1, 1, 2, 2], ['red', 'blue', 'red', 'blue']]
pd.MultiIndex.from_arrays(arrays, names=('number', 'color'))

# 结果
MultiIndex(levels=[[1, 2], ['blue', 'red']],
           codes=[[0, 0, 1, 1], [1, 0, 1, 0]],
           names=['number', 'color'])
```

## 2.4 Panel( 从 0.20.0 开始弃用)
### 2.4.1 panel 的创建
- class pandas.Panel(data=None, items=None, major_axis=None, minor_axis=None)
  - 作用：存储3维数组的Panel结构
  - 参数：
    - data : ndarray或者dataframe
    - items : 索引或类似数组的对象，axis=0
    - major_axis : 索引或类似数组的对象，axis=1
    - minor_axis : 索引或类似数组的对象，axis=2
```python
p = pd.Panel(data=np.arange(24).reshape(4,3,2),
                 items=list('ABCD'),
                 major_axis=pd.date_range('20130101', periods=3),
                 minor_axis=['first', 'second'])

# 结果
<class 'pandas.core.panel.Panel'>
Dimensions: 4 (items) x 3 (major_axis) x 2 (minor_axis)
Items axis: A to D
Major_axis axis: 2013-01-01 00:00:00 to 2013-01-03 00:00:00
Minor_axis axis: first to second
```

### 2.4.2 查看panel数据
```python
p[:,:,"first"]
p["B",:,:]
```

# 3 | 基本数据操作
![stockday](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/stockday.png)
## 3.1 索引操作
1. 直接使用行列索引（先列后行）
获取'2018-02-27'这天的'close'的结果
```python
# 直接使用行列索引名字的方式（先列后行）
data['open']['2018-02-27']
23.53

# 不支持的操作
# 错误
data['2018-02-27']['open']
# 错误
data[:1, :2]
```
2. 结合loc或者iloc使用索引
获取从'2018-02-27':'2018-02-22'，'open'的结果
```python
# 使用loc:只能指定行列索引的名字
data.loc['2018-02-27':'2018-02-22', 'open']

2018-02-27    23.53
2018-02-26    22.80
2018-02-23    22.88
Name: open, dtype: float64

# 使用iloc可以通过索引的下标去获取
# 获取前3天数据,前5列的结果
data.iloc[:3, :5]

            open    high    close    low
2018-02-27    23.53    25.88    24.16    23.53
2018-02-26    22.80    23.78    23.53    22.80
2018-02-23    22.88    23.37    22.82    22.71
```
3. 使用ix组合索引
> Warning:Starting in 0.20.0, the .ix indexer is deprecated, in favor of the more strict .iloc and .loc indexers.
获取行第1天到第4天，['open', 'close', 'high', 'low']这个四个指标的结果
```python
# 使用ix进行下表和名称组合做引
data.ix[0:4, ['open', 'close', 'high', 'low']]

# 推荐使用loc和iloc来获取的方式
data.loc[data.index[0:4], ['open', 'close', 'high', 'low']]
data.iloc[0:4, data.columns.get_indexer(['open', 'close', 'high', 'low'])]

            open    close    high    low
2018-02-27    23.53    24.16    25.88    23.53
2018-02-26    22.80    23.53    23.78    22.80
2018-02-23    22.88    22.82    23.37    22.71
2018-02-22    22.25    22.28    22.76    22.02
```

## 3.2 赋值操作
对DataFrame当中的close列进行重新赋值为1
```python
# 直接修改原来的值
data['close'] = 1
# 或者
data.close = 1
```

## 3.3 排序
排序有两种形式，一种对于索引进行排序，一种对于内容进行排序
### 3.3.1 DataFrame 排序
- 使用df.sort_values(by=, ascending=)
  - 单个键或者多个键进行排序,
  - 参数：
    - by：指定排序参考的键
    - ascending:默认升序
      - ascending=False:降序
      - ascending=True:升序
```python
# 按照开盘价大小进行排序 , 使用ascending指定按照大小排序
data.sort_values(by="open", ascending=True).head()

# 按照多个键进行排序
data.sort_values(by=['open', 'high'])
```
- 使用df.sort_index给索引进行排序
  ```python
  # 对索引进行排序, 这个股票的日期索引原来是从大到小，现在重新排序，从小到大
  data.sort_index()
  ```
### 3.3.2 Series 排序
- 使用series.sort_values(ascending=True)进行排序，series排序时，只有一列，不需要参数
  ```python
  data['p_change'].sort_values(ascending=True).head()

  2015-09-01   -10.03
  2015-09-14   -10.02
  2016-01-11   -10.02
  2015-07-15   -10.02
  2015-08-26   -10.01
  Name: p_change, dtype: float64
  ```
- 使用series.sort_index()进行排序
  ```python
  # 对索引进行排序
  data['p_change'].sort_index().head()

  2015-03-02    2.62
  2015-03-03    1.44
  2015-03-04    1.57
  2015-03-05    2.02
  2015-03-06    8.51
  Name: p_change, dtype: float64
  ```

### 3.4 增加
> 官方文档：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.append.html
DataFrame.append(other, ignore_index=False, verify_integrity=False, sort=False)[source]
功能：向 dataframe 对象中添加新的行，如果添加的列名不在 dataframe 对象中，将会被当作新的列进行添加
- other：DataFrame、series、dict、list 等数据结构
- ignore_index：默认值为 False，如果为 True 则不使用 index 标签
- verify_integraty：默认值为 False，如果为 True 当创建相同的 index 时会抛出 ValueError 的异常
- sort：boolean，默认是 None，pandas 0.23.0 的版本才有

```python
output_csv_field_names = ['host', 'request method', 'request uri', 'request version', 'request full uri', 'user agent', 'referer']
output_df = pd.DataFrame(columns=output_csv_field_names)
output_df = output_df.append(
    {
     'host': http_pkt_1.host,
     'request method': http_pkt_1.request_method,
     'request uri': http_pkt_1.request_uri,
     'request version': http_pkt_1.request_version,
     'request full uri': http_pkt_1.request_full_uri,
     'user agent': http_pkt_1.user_agent,
     'referer': http_pkt_1.referer
    }, ignore_index=True
)
```

# 4 | DataFrame 运算
## 4.1 算数运算
- add(n)
- sub(n)
```python
data['open'].add(1)

2018-02-27    24.53
2018-02-26    23.80
2018-02-23    23.88
2018-02-22    23.25
2018-02-14    22.49
```

## 4.2 逻辑运算
1. 逻辑运算符
```python
data["open"] > 23

2018-02-27     True
2018-02-26    False
2018-02-23    False
2018-02-22    False
2018-02-14    False

# 逻辑判断的结果可以作为筛选的依据
data[data["open"] > 23].head()

# 完成多个逻辑
data[(data["open"] > 23) & (data["open"] < 24)].head()
```
2. 逻辑运算函数
- query(expr)
  - expr:查询字符串
```python
# 完成多个逻辑
data[(data["open"] > 23) & (data["open"] < 24)].head()

data.query("open<24 & open>23").head()
```
- isin(values)
```python
# 可以指定值进行一个判断，从而进行筛选操作
data[data["open"].isin([23.53, 23.85])]
```

## 4.3 统计运算
1. describe
  综合分析: 能够直接得出很多统计结果,count, mean, std, min, max 等
  ```python
  # 计算平均值、标准差、最大值、最小值
  data.describe()
  ```
2. 统计函数
| count | Number of non-NA observations |
| --- | --- |
| sum	| Sum of values |
| mean	| Mean of values |
| median	| Arithmetic median of values 中位数 |
| min | Minimum |
| max	| Maximum |
| mode	| Mode |
| abs	| Absolute Value |
| prod	| Product of values |
| std	| Bessel-corrected sample standard deviation 标准差 |
| var	| Unbiased variance 方差 |
| idxmax	| compute the index labels with the maximum 最大值的索引 |
| idxmin	| compute the index labels with the minimum 最小值的索引 |
> **对于单个函数去进行统计的时候，坐标轴还是按照默认列“columns” (axis=0, default)，如果要对行“index” 需要指定(axis=1)**

3. 累计统计函数
| 函数	| 作用 |
| --- | --- |
| cumsum	| 计算前 n 个数的和 |
| cummax	| 计算前 n 个数的最大值 |
| cummin	| 计算前 n 个数的最小值 |
| cumprod	| 计算前 n 个数的积 |

## 4.4 自定义运算
- apply(func, axis=0)
  - func:自定义函数
  - axis=0:默认是列，axis=1为行进行运算

```python
# 定义一个对列，最大值-最小值的函数
data[['open', 'close']].apply(lambda x: x.max() - x.min(), axis=0)

open     22.74
close    22.85
dtype: float64
```

# 5 | 画图
1. pandas.DataFrame.plot
> https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.plot.html

2. pandas.Series.plot
> https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.plot.html

# 6 | 文件读取与存储
![读取存储](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/读取存储.png)
## 6.1 CSV
- pandas.read_csv(filepath_or_buffer, sep =',', usecols )
  - filepath_or_buffer:文件路径
  - sep :分隔符，默认用","隔开
  - usecols:指定读取的列名，列表形式
```python
# 读取文件,并且指定只获取'open', 'close'指标
data = pd.read_csv("./data/stock_day.csv", usecols=['open', 'close'])

            open    close
2018-02-27    23.53    24.16
2018-02-26    22.80    23.53
2018-02-23    22.88    22.82
2018-02-22    22.25    22.28
2018-02-14    21.49    21.92
```
- DataFrame.to_csv(path_or_buf=None, sep=', ’, columns=None, header=True, index=True, mode='w', encoding=None)
  - path_or_buf :文件路径
  - sep :分隔符，默认用","隔开
  - columns :选择需要的列索引
  - header :boolean or list of string, default True,是否写进列索引值
  - index:是否写进行索引
  - mode:'w'：重写, 'a' 追加
```python
# 选取10行数据保存,便于观察数据
data[:10].to_csv("./data/test.csv", columns=['open'])

# 结果
     Unnamed: 0    open
0    2018-02-27    23.53
1    2018-02-26    22.80
2    2018-02-23    22.88
3    2018-02-22    22.25
4    2018-02-14    21.49
5    2018-02-13    21.40
6    2018-02-12    20.70
7    2018-02-09    21.20
8    2018-02-08    21.79
9    2018-02-07    22.69

# index:存储不会讲索引值变成一列数据
data[:10].to_csv("./data/test.csv", columns=['open'], index=False)
```

## 6.2 HDF5
- HDF5在存储的时候支持压缩，使用的方式是blosc，这个是速度最快的也是pandas默认支持的
- 使用压缩可以提磁盘利用率，节省空间
- HDF5还是跨平台的，可以轻松迁移到hadoop 上面

HDF5文件的读取和存储需要指定一个键，值为要存储的DataFrame
- pandas.read_hdf(path_or_buf，key =None，** kwargs)
  - path_or_buffer:文件路径
  - key:读取的键
```python
# 读 HDF5 文件需要 tables 模块
day_close = pd.read_hdf("./data/day_close.h5")
```
- DataFrame.to_hdf(path_or_buf, key, *\kwargs*)
```python
day_close.to_hdf("./data/test.h5", key="day_close")

# 再次读取的时候, 需要指定键的名字
new_close = pd.read_hdf("./data/test.h5", key="day_close")
```

## 6.3 JSON
- pandas.read_json(path_or_buf=None, orient=None, typ='frame', lines=False)
  - 将JSON格式准换成默认的Pandas DataFrame格式
  - orient : string,Indication of expected JSON string format.
    - 'split' : dict like {index -> [index], columns -> [columns], data -> [values]}
      - split 将索引总结到索引，列名到列名，数据到数据。将三部分都分开了
    - 'records' : list like [{column -> value}, ... , {column -> value}]
      - records 以columns：values的形式输出
    - 'index' : dict like {index -> {column -> value}}
      - index 以index：{columns：values}...的形式输出
    - 'columns' : dict like {column -> {index -> value}},默认该格式
      - colums 以columns:{index:values}的形式输出
    - 'values' : just the values array
      - values 直接输出值
  - lines : boolean, default False
    - 按照每行读取json对象
  - typ : default ‘frame’， 指定转换成的对象类型series或者dataframe

- DataFrame.to_json(path_or_buf=None, orient=None, lines=False)
  - 将Pandas 对象存储为json格式
  - path_or_buf=None：文件地址
  - orient:存储的json形式，{‘split’,’records’,’index’,’columns’,’values’}
  - lines:一个对象存储为一行

# 7 | 缺失值处理
- 获取缺失值的标记方式(NaN或者其他标记方式)
- 如果缺失值的标记方式是NaN
  - 判断数据中是否包含NaN：
    - pd.isnull(df),
    - pd.notnull(df)
  - 存在缺失值Nan:
    1. 删除存在缺失值的:dropna(axis='rows')
       - 注：不会修改原数据，需要接受返回值
    2. 替换缺失值:fillna(value, inplace=True)
       - value:替换成的值
       - inplace:True:会修改原数据，False:不替换修改原数据，生成新的对象
- 如果缺失值没有使用NaN标记，比如使用"？"
  - 先替换‘?’为np.nan，然后继续处理

```python
# 替换所有缺失值
for i in movie.columns:
    if np.all(pd.notnull(movie[i])) == False:
        print(i)
        movie[i].fillna(movie[i].mean(), inplace=True)
```

若在读取数据时，报 SSL error 错误，解决办法:
```python
# URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed (_ssl.c:833)>

# 全局取消证书验证
import ssl
ssl._create_default_https_context = ssl._create_unverified_context
```

# 8 | 数据离散化
- 数据离散化
  - 可以用来减少给定连续属性值的个数
  - 在连续属性的值域上，将值域划分为若干个离散的区间，最后用不同的符号或整数值代表落在每个子区间中的属性值。

- 数据分组
  - pd.qcut(data, q)：
    - 对数据进行分组将数据分组，一般会与value_counts搭配使用，统计每组的个数
    - series.value_counts()：统计分组次数
  ```python
  # 自行分组
  qcut = pd.qcut(p_change, 10)
  # 计算分到每个组数据个数
  qcut.value_counts()
  ```
  - pd.cut(data, bins)
    - 自定义分组区间
  ```python
  # 自己指定分组区间
  bins = [-100, -7, -5, -3, 0, 3, 5, 7, 100]
  p_counts = pd.cut(p_change, bins)
  ```
- one-hot 热编码
![one_hot编码](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/one_hot编码.png)

  - pandas.get_dummies(data, prefix=None)
    - data:array-like, Series, or DataFrame
    - prefix:分组名字
```python
# 得出one-hot编码矩阵
dummies = pd.get_dummies(p_counts, prefix="rise")
```

# 9 | 合并
- pd.concat([data1, data2], axis=1)
  - 按照行或列进行合并,axis=0为列索引，axis=1为行索引
- pd.merge(left, right, how='inner', on=None)
  - 可以指定按照两组数据的共同键值对合并或者左右各自合并
  - left: DataFrame
  - right: 另一个DataFrame
  - on: 指定的共同键
  - how:按照什么方式连接

| Merge method	| SQL Join Name	| Description |
| --- | --- | --- |
| left	| LEFT OUTER JOIN	| Use keys from left frame only |
| right	| RIGHT OUTER JOIN	| Use keys from right frame only |
| outer	| FULL OUTER JOIN	| Use union of keys from both frames |
| inner	| INNER JOIN	| Use intersection of keys from both frames |

```python
left = pd.DataFrame({'key1': ['K0', 'K0', 'K1', 'K2'],
                        'key2': ['K0', 'K1', 'K0', 'K1'],
                        'A': ['A0', 'A1', 'A2', 'A3'],
                        'B': ['B0', 'B1', 'B2', 'B3']})

right = pd.DataFrame({'key1': ['K0', 'K1', 'K1', 'K2'],
                        'key2': ['K0', 'K0', 'K0', 'K0'],
                        'C': ['C0', 'C1', 'C2', 'C3'],
                        'D': ['D0', 'D1', 'D2', 'D3']})

# 默认内连接
result = pd.merge(left, right, on=['key1', 'key2'])
```
![内连接](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/内连接.png)

```python
# 左连接
result = pd.merge(left, right, how='left', on=['key1', 'key2'])
```
![左连接](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/左连接.png)

```python
# 左连接
result = pd.merge(left, right, how='right', on=['key1', 'key2'])
```
![右连接](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/右连接.png)


```python
# 左连接
result = pd.merge(left, right, how='outer', on=['key1', 'key2'])
```
![外连接](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/外链接.png)

# 10 | 交叉表与透视表

# 11 | 分组与聚合
分组与聚合原理
![分组聚合原理](https://cdn.jsdelivr.net/gh/cs-cshi/image-host/pandas/分组聚合原理.png)

- DataFrame.groupby(key, as_index=False)
  - key:分组的列数据，可以多个

```python
col =pd.DataFrame({'color': ['white','red','green','red','green'], 'object': ['pen','pencil','pencil','ashtray','pen'],'price1':[5.56,4.20,1.30,0.56,2.75],'price2':[4.75,4.12,1.60,0.75,3.15]})

color    object    price1    price2
0    white    pen    5.56    4.75
1    red    pencil    4.20    4.12
2    green    pencil    1.30    1.60
3    red    ashtray    0.56    0.75
4    green    pen    2.75    3.15
```

进行分组，对颜色分组，price进行聚合
```python
# 分组，求平均值
col.groupby(['color'])['price1'].mean()
col['price1'].groupby(col['color']).mean()

color
green    2.025
red      2.380
white    5.560
Name: price1, dtype: float64

# 分组，数据的结构不变
col.groupby(['color'], as_index=False)['price1'].mean()

color    price1
0    green    2.025
1    red    2.380
2    white    5.560
```
