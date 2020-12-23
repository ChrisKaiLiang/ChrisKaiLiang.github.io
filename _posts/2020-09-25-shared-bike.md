---
layout: post
title: "共享单车用户画像"
subtitle: "使用聚类方法进行用户画像"
date: 2020-09-25 00:30:00 -0100
background: '/img/posts/06.jpg'
permalink: '/shared-bike'
---


# 摩拜单车用户画像分析

[项目代码-GitHub](https://github.com/ChrisKaiLiang/Data-Analysis-and-Machine-Learning-Projects/blob/master/Shared_Bike/共享单车用户画像分析.ipynb)


**任务说明**  

摩拜单车，英文名mobike，是由胡玮炜创办的北京摩拜科技有限公司研发的互联网短途出行解决方案，是无桩借还车模式的智能硬件。人们通过智能手机就能快速租用和归还一辆摩拜单车，用可负担的价格来完成一次几公里的市内骑行。

由于一公里的出行是一个高频的需求场景，所以mobike单车累计了大量的用户基本信息以及骑行的数据，通过这些数据，能够帮助企业更好识别自己的客群画像和他们的骑行偏好。

**本周你的任务：**  
根据用户的基本信息以及骑行的数据，完成以下事项：
- 使用Python建立聚类分析模型
- 对于聚类分析模型得出的分群特征进行解读



## 1 - 数据概况分析

### 1.1 读取数据

<img src='img/shared_bike/data5.png' width='900'/>


### 1.2 数据概况

**数据字典**  

<img src='img/shared_bike/mobike_dict.png' alt='dict' width='900'/>


```python
>> mobike_raw.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 6427 entries, 0 to 6426
    Data columns (total 14 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   user_id            6427 non-null   int64  
     1   start_time         6427 non-null   object 
     2   end_time           6427 non-null   object 
     3   timeduration       6427 non-null   int64  
     4   bikeid             6427 non-null   int64  
     5   tripduration       6427 non-null   int64  
     6   from_station_id    6427 non-null   int64  
     7   from_station_name  6427 non-null   object 
     8   to_station_id      6427 non-null   int64  
     9   to_station_name    6427 non-null   object 
     10  usertype           6427 non-null   object 
     11  gender             5938 non-null   object 
     12  birthyear          5956 non-null   float64
     13  age                6427 non-null   object 
    dtypes: float64(1), int64(6), object(7)
    memory usage: 703.1+ KB



## 2 - 数据预处理

### 2.1 年龄

- 发现年龄缺失值，空值行数量较少，去掉
- 将年龄的数据类型进行转换
- 保留年龄小于80的数据


### 2.2 性别

- 仅有18个缺失值，删除


### 2.3 骑行时间

- 转化时间类型数据
- 创建计算字段
    - 是否为工作日
    - 是否是早晚高峰


### 2.5 骑行时长

- 检查骑行时长异常数据，存在 timeduration 计算错误的数据，同时 tripduration 也很长
- 重新计算骑行时长



## 3 - 探索性分析 (EDA)

### 3.1 年龄


```python
plt.figure(figsize=(15,5))

sns.distplot(mobike['age'], bins=15, color='orangered')
plt.title("用户年龄分布", fontsize=15)

plt.show()
```


<img src='img/shared_bike/output_40_0.png' alt='用户年龄分布' width='800'/>

大部分用户集中在 25-35 年龄段

### 3.2 性别


```python
plt.figure(figsize=(15,5))

sns.countplot(y='gender', data=mobike)
plt.title("用户类型分布", fontsize=15)

plt.show()
```


<img src='img/shared_bike/output_43_0.png' alt='用户类型分布' width='800'/>


男性用户较多，约占 78%

### 3.3 用户类型


```python
mobike['usertype'].value_counts()
```




    Subscriber    5704
    Customer       179
    Name: usertype, dtype: int64




```python
mobike['usertype'].value_counts(1)
```




    Subscriber    0.969573
    Customer      0.030427
    Name: usertype, dtype: float64




```python
plt.figure(figsize=(15,5))

sns.countplot(y='usertype', data=mobike)
plt.title("用户类型分布", fontsize=15)

plt.show()
```


<img src='img/shared_bike/output_48_0.png' alt='用户类型分布' width='800'/>

大部分为订阅用户，占 96.5%

### 3.4 骑行时间


```python
# 查看骑行时间年的分布
mobike['start_time'].dt.year.value_counts()
```




    2018    5883
    Name: start_time, dtype: int64




```python
# 查看骑行时间月份的分布
mobike['start_time'].dt.month.value_counts()
```




    10    3107
    11    1601
    12    1175
    Name: start_time, dtype: int64




```python
# 查看骑行时间按天分布
plt.figure(figsize=(15,5))

sns.countplot(mobike['start_time'].dt.day, color='orangered')
plt.title("骑行开始时间按天分布", fontsize=15)

plt.show()
```


<img src='img/shared_bike/output_53_0.png' alt='骑行开始时间按天分布' width='800'/>

在月末骑行的人略少于月初和月中的人


```python
# 查看骑行时间按周内天分布
plt.figure(figsize=(15,5))

sns.countplot(mobike['start_time'].dt.dayofweek, color='orangered')
plt.title("骑行开始时间按周内天分布", fontsize=15)

plt.show()
```


<img src='img/shared_bike/output_55_0.png' alt='骑行开始时间按周内天分布' width='800'/>

周末骑行的人少于周中的


```python
# 查看骑行开始时间按小时分布
plt.figure(figsize=(15,5))

sns.countplot(mobike['start_time'].dt.hour, color='orangered')
plt.title("骑行开始时间按小时分布", fontsize=15)

plt.show()
```


<img src='img/shared_bike/output_57_0.png' alt='骑行开始时间按小时分布' width='800'/>

早晚高峰的骑车数量明显增多

### 3.5 骑行时长


```python
plt.figure(figsize=(15,5))

sns.distplot(mobike['timeduration'], color='orangered')
plt.title("骑行时长分布 (分钟)", fontsize=15)

plt.show()
```


<img src='img/shared_bike/output_60_0.png' alt='骑行时长分布' width='800'/>

骑行时长在 5-15 分钟最多

### 3.6 骑行距离


```python
plt.figure(figsize=(15,5))

sns.distplot(mobike['tripduration'], color='orangered')
plt.title("骑行距离分布", fontsize=15)

plt.show()
```


<img src='img/shared_bike/output_63_0.png' alt='骑行距离分布' width='800'/>

骑行距离在 200-800 米最多

### 3.7 平均骑行速度


```python


```python
plt.figure(figsize=(15,5))

sns.distplot(mb['speed'], color='orangered')
plt.title("平均骑行速度分布 (米/分钟)", fontsize=15)

plt.show()
```


<img src='img/shared_bike/output_67_0.png' alt='平均骑行速度分布' width='800'/>

平均骑行速度集中在 60米/分钟 左右

## 4 - 聚类分析

### 4.1 - 数据准备
将类别变量使用 one-hot 编码方式进行编码  


### 4.2 - K-Means 聚类模型


```python
# 寻找最优 k

def KM_score(X, k: int) -> float:
    """返回轮廓系数 (Silhouette score)"""
    model=KMeans(n_clusters=k, random_state=42)
    model.fit(X)
    X_cluster = model.fit_predict(X)
    score = metrics.silhouette_score(X, X_cluster)
    
    return score

# 计算轮廓系数
score_dict = {}
for i in range(2, 10):
    score_dict[i] = KM_score(X, i)

# 绘制轮廓系数图像
plt.figure(figsize=(15,5))
plt.plot(score_dict.keys(), score_dict.values(), linestyle='--', marker='o')
plt.title("轮廓系数折线图", fontsize=15)
plt.show()
```


<img src='img/shared_bike/output_77_0.png' alt='轮廓系数折线图' width='800'/>


```python
# 为了观察分群效果，我们选择分成7组
model = KMeans(n_clusters=7, random_state=42)
model.fit(X)
```




    KMeans(n_clusters=7, random_state=42)




```python
mobike_cluster['cluster'] = model.labels_
```


```python
mobike_cluster['cluster'].value_counts()
```




    0    1399
    2    1167
    6    1114
    3     783
    1     703
    5     538
    4     179
    Name: cluster, dtype: int64



### 4.3 查看聚类效果


```python
>> mobike_cluster.groupby(['cluster'])['timeduration'].describe()
```



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
    <tr>
      <th>cluster</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1399.0</td>
      <td>7.975697</td>
      <td>4.156039</td>
      <td>1.0</td>
      <td>5.0</td>
      <td>7.0</td>
      <td>11.0</td>
      <td>19.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>703.0</td>
      <td>9.615932</td>
      <td>4.931771</td>
      <td>1.0</td>
      <td>6.0</td>
      <td>9.0</td>
      <td>13.0</td>
      <td>23.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1167.0</td>
      <td>9.262211</td>
      <td>4.824872</td>
      <td>1.0</td>
      <td>6.0</td>
      <td>8.0</td>
      <td>12.0</td>
      <td>23.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>783.0</td>
      <td>9.286079</td>
      <td>5.265814</td>
      <td>1.0</td>
      <td>5.0</td>
      <td>8.0</td>
      <td>13.0</td>
      <td>25.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>179.0</td>
      <td>20.016760</td>
      <td>13.295042</td>
      <td>4.0</td>
      <td>9.0</td>
      <td>16.0</td>
      <td>28.0</td>
      <td>60.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>538.0</td>
      <td>29.171004</td>
      <td>7.677662</td>
      <td>19.0</td>
      <td>24.0</td>
      <td>27.0</td>
      <td>33.0</td>
      <td>60.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1114.0</td>
      <td>9.270197</td>
      <td>4.610593</td>
      <td>1.0</td>
      <td>6.0</td>
      <td>8.0</td>
      <td>12.0</td>
      <td>21.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
>> mobike_cluster.groupby(['cluster'])['tripduration'].describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
    <tr>
      <th>cluster</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1399.0</td>
      <td>478.370979</td>
      <td>248.344145</td>
      <td>74.0</td>
      <td>289.00</td>
      <td>420.0</td>
      <td>635.00</td>
      <td>1191.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>703.0</td>
      <td>578.083926</td>
      <td>296.624495</td>
      <td>67.0</td>
      <td>348.00</td>
      <td>527.0</td>
      <td>762.50</td>
      <td>1377.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1167.0</td>
      <td>555.832905</td>
      <td>288.001391</td>
      <td>61.0</td>
      <td>334.50</td>
      <td>500.0</td>
      <td>734.00</td>
      <td>1394.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>783.0</td>
      <td>557.503193</td>
      <td>315.455506</td>
      <td>78.0</td>
      <td>307.00</td>
      <td>479.0</td>
      <td>749.50</td>
      <td>1477.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>179.0</td>
      <td>1201.486034</td>
      <td>798.199824</td>
      <td>236.0</td>
      <td>529.50</td>
      <td>980.0</td>
      <td>1667.50</td>
      <td>3597.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>538.0</td>
      <td>1748.819703</td>
      <td>461.001346</td>
      <td>1147.0</td>
      <td>1431.00</td>
      <td>1632.5</td>
      <td>1959.75</td>
      <td>3583.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1114.0</td>
      <td>555.160682</td>
      <td>276.301969</td>
      <td>80.0</td>
      <td>338.25</td>
      <td>495.0</td>
      <td>735.50</td>
      <td>1279.0</td>
    </tr>
  </tbody>
</table>
</div>



4，5 组骑行时间较长，骑行距离也远

## 5 - 聚类业务解读


```python
>> pd.DataFrame(model.cluster_centers_, columns=mobike_cluster.columns[:8])
```


<img src='img/shared_bike/knn_result.png' alt='dict' width='900'/>



**聚类分组分析**  
- 4， 5 组的骑行时间和距离较大
- 3 组主要在周末骑行
- 2 组主要是早高峰骑行的用户
- 6 组主要是晚高峰用户
- 1 组女性用户较多
- 4 组主要是非订阅用户

**总结**  
- 通过用户基本数据分析，我们的大部分用户为25-35的年轻人，在工作日的早晚高峰进行短距离骑行，我们可针对此类人气进行精准营销，进行推送和优惠券的发放来提高业务收入
- 通过聚类分析，我们可以得到各组用户的特征从而给他们打上标签，例如‘晚高峰用户’，之后可在晚高峰前给予信息推送，从而提高单车的使用率
