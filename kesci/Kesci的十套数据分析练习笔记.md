>练习题Kesci链接: [link](https://www.kesci.com/home/project/59e77a636d213335f38daec2)

@[toc] 
# 练习1-开始了解你的数据 
<br/> 

## 探索Chipotle快餐数据
<br/> 

### 步骤9 被下单数最多商品(item)是什么?
思路：
1. **对象**：DataFrame
2. **提取**感兴趣的列。
！注意：中括号内传入单个列名，如`chipo['item_name']`，则产生一个Series（不能传入多个列名）；
中括号内传入数组，如`chipo[['item_name','quantity']]`，则产生一个DataFrame。
3. **分组**，groupby括号内传入列名的数组（一个或多个），产生一个group对象。（`as_index=False`表示分组键不作为index，如果传入`group_keys=False`在后面的运算中会把分组键重新置为index——不清楚什么原因）
4. 对group对象**应用**函数。
答案的做法是—— `.agg({'quantity':sum})`
我的做法是（相同效果）—— `[['quantity']].sum()`
都是生成DataFrame对象
5. **排序** 使用sort_index和sort_values效果差不多，但是系统推荐后者。传入`inplace=True`实现原地排序
6. **显示**最大值
<br/> 

### 步骤10 在item_name这一列中，一共有多少种商品被下单？
统计不重复的对象的个数
答案：`chipo['item_name'].nunique()`
我：`len(chipo['item_name'].unique())`
<br/> 

### 步骤13 将item_price转换为浮点数
`chipo['item_price']`中每个对象的格式是string（‘$’+数字），如果直接用`.astype(float)`则会出错，因为str无法转换成为float。
解决办法：利用可调用函数处理每个对象，并将函数传入apply。
```python
dollarizer = lambda x: float(x[1:-1])#去除'$'符号并将数字传唤成为float
chipo['item_price'].apply(dollarizer)
```
<br/> 

### 步骤14 在该数据集对应的时期内，收入(revenue)是多少
`round()`返回括号内浮点数的四舍五入值。
<br/> 

### 步骤16 每一单(order)对应的平均总价是多少？
思路：
1. **对象**：DataFrame
2. **提取**感兴趣的列。`chipo[['order_id','sub_total']]`
3. **分组**。`.groupby(by=['order_id'])`
4. 对group对象**应用**函数。`.agg({'sub_total':'sum'})`
5. **提取**感兴趣的列。`['sub_total']`
6. 对列应用函数。`.mean()`
<br/> 

# 练习2-数据过滤与排序
<br/> 

## 探索2012欧洲杯数据
<br/> 

### 步骤5 有多少球队参与了2012欧洲杯？
`.shape(0)`DataFrame的shape函数，输出括号内（0代表0轴，1代表1轴...）的大小。
<br/> 

### 步骤8 对数据框discipline按照先Red Cards再Yellow Cards进行排序 
`.sort_values(by=['A','B'])`sort_value函数按照A，B的先后顺序对A，B先后排序。
<br/> 

### 步骤11 选取以字母G开头的球队数据
处理字符串对象用str函数
答案：`euro12[euro12.Team.str.startswith('G')]`
我：`euro12[euro12['Team'].apply(lambda x: x[0]=='G')]`
<br/> 

### 步骤12 选取前7列
`.iloc`按整数索引选取子行和子列
`loc`按轴标签（行名或列名）选取子行和子列
<br/> 

### 步骤14 找到英格兰(England)、意大利(Italy)和俄罗斯(Russia)的射正率(Shooting Accuracy)
`.isin()`函数可用于选取某一列中的几个对象所在的行作为行索引，函数返回的是bool类型的Series。
<br/> 

# 练习3-数据分组
<br/> 

## 探索酒类消费数据
<br/> 

### 步骤8 打印出每个大陆对spirit饮品消耗的平均值，最大值和最小值
思路：
1. **对象**：DataFrame
2. **分组**。`drinks.groupby('continent')`
3. **提取**感兴趣的列。`.spirit_servings`
4. **应用**函数。`agg(['mean', 'min', 'max'])`
<br/> 

# 练习4-Apply函数
<br/> 

## 探索1960 - 2014 美国犯罪数据
<br/> 

### 步骤5 将Year的数据类型转换为 ```datetime64```
`crime.Year = pd.to_datetime(crime.Year, format='%Y')`
转换为时间数据类型（年份）
<br/> 

### 步骤6 将列Year设置为数据框的索引
`crime.set_index('Year', drop = True)`
默认drop为True，如果drop=False则保留'Year'在原数据集中，不‘丢弃’。
<br/>

### 步骤7 删除名为Total的列
答案：`del crime['Total']`
我：`crime.drop(['Total'],axis=1)`
答案更加方便
#### 时间重采样
答案：`crime.resample('10AS').sum()`
该语句的作用是对时间的重采样（分组操作，跟groupby的作用类似）——每10年为一组，并对每组内的数据进行求和操作。
我（效果相同的代码）：`crime.groupby(pd.Grouper(freq='10AS')).sum()`
使用了groupby对时间实现分组操作
<br/>

### 步骤8 按照Year对数据框进行分组并求和 
*注意Population这一列，若直接对其求和，是不正确的*
思路：
1. 对时间进行重采样并对所有分组求和 `crimes = crime.resample('10AS').sum()`
2. 用resample去得到“Population”列的最大值 
`population = crime['Population'].resample('10AS').max()`
3. 更新 "Population" `crimes['Population'] = population`
<br/>

### 步骤9 何时是美国历史上生存最危险的年代？
目的：获取最大值的索引
代码实现：`crime.idxmax(0)`
<br/>

# 练习5-合并
>==笔记==：pandas对象中的数据可以通过一些方式进行合并：
>1. `pandas.merge`可根据一个或多个键将不同*DataFrame*中的行连接起来。SQL或其他关系型数据库的用户对此应该会比较熟悉，因为它实现的就是数据库的join操作。
>2. `pandas.concat`可以沿着一条轴将多个对象堆叠到一起。
>3. 实例方法`combine_first`可以将重复数据拼接在一起，用一个对象中的值填充另一个对象中的缺失值。
>
<br/>

# 练习6-统计
<br/>

## 探索风速数据
<br/>

### 步骤3 将数据作存储并且设置前三列为合适的索引
`data = pd.read_table(path6, sep = "\s+", parse_dates = [[0,1,2]])`
`parse_dates = [[0,1,2]]`的作用是：传入一个list of lists，合并0,1,2列的值作为一个日期列使用
<br/>

### 步骤4 2061年？我们真的有这一年的数据？创建一个函数并用它去修复这个bug
思路：
1. **定义函数**：这里我犯了一个错误，我们==不能去改变x.year的值然后返回x==，必须采取中间变量year，然后返回一个重新构造的datetime.date对象。
```python
def fix_century(x):
    year = x.year - 100 if x.year > 1989 else x.year
    return datetime.date(year, x.month, x.day)
```
2. **应用**函数：`data['Yr_Mo_Dy'] = data['Yr_Mo_Dy'].apply(fix_century)`
<br/>

### 步骤5 将日期设为索引，注意数据类型，应该是```datetime64[ns]```
思路：
1. 将`object`类型转换成```datetime64[ns]```类型
`data["Yr_Mo_Dy"] = pd.to_datetime(data["Yr_Mo_Dy"])`
2. 设置日期为索引 `data = data.set_index('Yr_Mo_Dy')`
<br/>

### 步骤6 对应每一个location，一共有多少数据值缺失
目的：统计每一列的缺失值数
代码实现：`data.isnull().sum()`
<br/>

### 步骤9 创建一个名为```loc_stats```的数据框去计算并存储每个location的风速最小值，最大值，平均值和标准差
答案代码实现：
```python
loc_stats = pd.DataFrame()
loc_stats['min'] = data.min() # min
loc_stats['max'] = data.max() # max 
loc_stats['mean'] = data.mean() # mean
loc_stats['std'] = data.std() # standard deviations
```
我的方法：`data.agg({'min','max','mean','std'})`
这种方法生成的index和columns刚好与答案相反了，而且agg函数不能对行(axis=1)传入多个函数，~~`data.agg({'min','max','mean','std'},axis=1)`~~
<br/>

### 步骤11 对于每一个location，计算一月份的平均风速
*注意，1961年的1月和1962年的1月应该区别对待*
答案思路：
1. **创建**新列，复制index的数据到新列中。`data['date'] = data.index`
2. **创建**3个新列，分别加入日期的年、月、日信息。
```py
data['month'] = data['date'].apply(lambda date: date.month)
data['year'] = data['date'].apply(lambda date: date.year)
data['day'] = data['date'].apply(lambda date: date.day)
```
3. 使用查询函数`.quary()`**提取**月份等于1所在的**行**。 `january_winds = data.query('month == 1')`
4. **选取**目标**列**求平均。`january_winds.loc[:,'RPT':"MAL"].mean()`

我的方法： `data[data.index.month==1].mean()`
我的方法是通过索引提取月份等于1所在的行。
<br/>

### 步骤12  对于数据记录按照年为频率取样
答案方法：依然是使用查询函数`.quary()`**提取**月和日都等于1所在的**行**。
`data.query('month == 1 and day == 1')`
我的方法：使用时间重采样函数`.resample()`，传入`'AS'`代表按年重采样，使用`.first()`代表数值用第一个值代替，也就是按1月1日的值填充。
`data.resample('AS').first()`
<br/>

# 练习7-可视化
<br/>

## 探索泰坦尼克灾难数据
<br/>

# 练习8-创建数据框
<br/>

## 探索Pokemon数据
<br/>

### 步骤6 查看每个列的数据类型
查看DataFrame的每个列的数据类型：`.dtypes`（记得后面有s）
查看Series的数据类型：`dtype` （后面没有s）
我的方法（错误结果）：~~`pokemon.apply(lambda x: x.dtype)`~~，暂时不知道原因。
<br/>

# 练习9-时间序列
<br/>

## 探索Apple公司股价数据
<br/>

### 步骤10 数据集中最早的日期和最晚的日期相差多少天？
`apple.index.max() - apple.index.min()`输出`timedelta`类型
转化为天数的int型输出需要：`(apple.index.max() - apple.index.min()).days`
<br/>

# 练习10-删除数据
<br/>

## 探索Iris纸鸢花数据
<br/>

### 步骤4 创建数据框的列名称
答案方法：`iris = pd.read_csv(path10,names = ['sepal_length','sepal_width', 'petal_length', 'petal_width', 'class'])`
使用`pd.read_csv`设置列名需要传入参数`names=`而不是`columns=`。
我的方法：`iris.columns=['sepal_length','sepal_width', 'petal_length', 'petal_width', 'class']`
直接使用`.columns`设置列名。
<br/>

### 步骤6 将列```petal_length```的第10到19行设置为缺失值
答案：`iris.iloc[10:20,2:3] = np.nan`
最好使用loc或iloc**提取**感兴趣的行和列。
我的方法（系统报Warning）：~~`iris['petal_length'][10:20]=np.nan`~~
这种方法倾向于从DataFrame中复制相应的行和列，然后作修改。所以最好是用答案原地修改的方法。
<br/>

### 步骤11 重新设置索引
代码实现：`iris = iris.reset_index(drop = True)`
因为`reset_index`后，老的index默认会成为新的DataFrame中的一列。传入`drop=True`则可以丢弃老的index，直接生成新的索引。
