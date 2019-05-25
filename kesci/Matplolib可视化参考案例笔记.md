>练习题Kesci链接: [link](https://www.kesci.com/home/project/5ce762fe0ee9cd002cd0fc9e)

@[toc] 
# Reading in the dataset
思路：
1. 创建特征名字的**字典**（从zip类型中用for语句创建字典的标签和内容）
```py
feature_dict = {i:label for i,label in zip(
            range(4),
              ('sepal length in cm', 
              'sepal width in cm', 
              'petal length in cm', 
              'petal width in cm', ))}
```
2. **读取**数据。
`df=pd.read_csv("../input/iris/iris.csv",header=0,sep=',')`
源文件第一行不是数据，因此传入参数`header`将第一行设置为列的标头，传入参数`sep`表示源文件是用`,`作为分隔符的。
3. 重新设置列名。
`df.columns = [l for i,l in sorted(feature_dict.items())] + ['class label']`
`sorted()`函数返回一个排序后的二维数组，再用for语句提取相应的列名。
4. **去除**缺失值所在的行。`df.dropna(how="all", inplace=True)`
<br/> 

# Pie chart
思路：
1.   **提取**感兴趣的数据。
```py
X = df[['sepal length in cm', 
              'sepal width in cm', 
              'petal length in cm', 
              'petal width in cm']].values 
y = df['class label'].values
```
2. ==将字符串转化为整型数据。==
```py
from sklearn.preprocessing import LabelEncoder
enc = LabelEncoder()
label_encoder = enc.fit(y)
y = label_encoder.transform(y)
```
利用`LabelEncoder`将`class label`中的字符串拟合成整型数据，以便于对列数据的分类，方便后面对列中不同类的数量进行统计。

3. 建立一个字典匹配上面的整型数据和字符串。
`label_dict = {0: 'Setosa', 1: 'Versicolor', 2:'Virginica'}`
4.  绘制扇形图。
```py
plt.pie(
    [X[y==i].shape[0] for i in range(3)],
    labels=[label_dict[i] for i in range(3)],
    shadow=True,
    colors=('yellowgreen', 'lightskyblue', 'gold'),
    startangle=90,      # rotate conter-clockwise by 90 degrees
    autopct='%1.1f%%',  # display fraction as percentage
    )
 ```
 上述的1,2,3步为了方便数据的导入和labels的设置。
<br/> 

# Bar plot
思路：
1. 因为Pie chart中的第二部已经把`class label`中的字符串拟合成整型数据，因此可以利用它作为分类的手段，用于计算分组后的结果。因为此时`['sepal length in cm', 'sepal width in cm', 'petal length in cm', 'petal width in cm']`的数值横向排列，因此用`mean(axis=0)`可计算各种`class label`的平均值。
```py
mean_vals = [X[y==i,:].mean(axis=0) for i in range(3)]
labels = [attr_dict[i] for i in range(4)]
```
2. 每种`class label`的平均值可以画一个柱状图，现在要把3种`class label`的柱状图和成一个柱状图。
```py
pos = np.arange(4)
width = 0.2 
    
# Plotting the bars
fig, ax = plt.subplots(figsize=(8,6))

plt.bar(pos, mean_vals[0], width,
                 alpha=0.5,
                 color='g',
                 label=labels[0])

plt.bar([p + width for p in pos], mean_vals[1], width,
                 alpha=0.5,
                 color='b',
                 label=labels[1])
    
plt.bar([p + width*2 for p in pos], mean_vals[2], width,
                 alpha=0.5,
                 color='r',
                 label=labels[2])
```
3. 设置轴标签、刻度、标题、上下限、图例、栅格等等
```py
# Setting axis labels and ticks
ax.set_ylabel('cm')
ax.set_title('Average values for the flower dimensions')
ax.set_xticks([p + 1.5 * width for p in pos])
ax.set_xticklabels(labels)

# Setting the x-axis and y-axis limits
plt.xlim(min(pos)-width, max(pos)+width*4)


# Adding the legend and showing the plot
plt.legend([label_dict[i] for i in range(3)], loc='upper right')

# adding horizontal grid lines 
ax.yaxis.grid(True)
```
<br/> 

# Box plot
思路：
1. 同样，提取各种`class label`对应的`petal width in cm`特征的数据所组成的array，并把3个array**合成一个array**作为`plt.boxplot`的数据输入。
`[X[y==i,3] for i in range(3)]`
2. 绘制箱型图。
```py
bplot = plt.boxplot([X[y==i,3] for i in range(3)],
        notch=True,          # notch shape 
        vert=True,           # vertical box aligmnent
        sym='ko',            # black circle for outliers
        patch_artist=True)   # fill with color
```
3. 设置箱型图的颜色、晶须(whiskers)、箱盖(caps)、轴刻度、栅格、标题等等。
```py
# choosing custom colors to fill the boxes
colors = ['pink', 'lightblue', 'lightgreen']
for patch, color in zip(bplot['boxes'], colors):
    patch.set_facecolor(color)

# modifying the whiskers: straight lines, black, wider
for whisker in bplot['whiskers']:
    whisker.set(color='black', linewidth=1.2, linestyle='-')    
    
# making the caps a little bit wider 
for cap in bplot['caps']:
    cap.set(linewidth=1.2)
    
# hiding axis ticks
plt.tick_params(axis="both", which="both", bottom="off", top="off",  
        labelbottom="on", left="off", right="off", labelleft="on")

# adding horizontal grid lines 
ax.yaxis.grid(True)

plt.xticks([y+1 for y in range(4)],
           [label_dict[i] for i in range(3)])
plt.ylim([0,3])

plt.title('Petal widths of the three different flower species')
plt.ylabel('cm')
```
<br/> 

