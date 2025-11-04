
## 1. Pandas 基础概念

### 1.1 Pandas 简介

```python
import pandas as pd
import numpy as np

"""
Pandas 是 Python 的数据分析核心库，提供：
- 快速、灵活、表达力强的数据结构
- 数据清洗、转换、分析功能
- 时间序列处理能力
- 与 NumPy、Matplotlib 等库完美集成

核心数据结构：
1. Series: 一维带标签数组
2. DataFrame: 二维表格型数据结构
3. Index: 索引对象
"""
```

### 1.2 Series 基础

```python
# ========== 创建 Series ==========
# 从列表创建
s1 = pd.Series([1, 3, 5, np.nan, 6, 8])
print("从列表创建:")
print(s1)

# 从字典创建（自动使用键作为索引）
s2 = pd.Series({'a': 1, 'b': 2, 'c': 3})
print("\n从字典创建:")
print(s2)

# 指定索引
s3 = pd.Series([10, 20, 30], index=['x', 'y', 'z'])
print("\n指定索引:")
print(s3)

# ========== Series 基本操作 ==========
print(f"Series 值: {s3.values}")
print(f"Series 索引: {s3.index}")
print(f"Series 数据类型: {s3.dtype}")
print(f"Series 形状: {s3.shape}")
print(f"Series 大小: {s3.size}")

# 索引访问
print(f"通过标签访问: s3['x'] = {s3['x']}")
print(f"通过位置访问: s3[0] = {s3[0]}")

# 切片操作
print(f"标签切片: {s3['x':'y']}")  # 包含末端
print(f"位置切片: {s3[0:2]}")     # 不包含末端

# ========== Series 运算 ==========
s4 = pd.Series([1, 2, 3, 4])
s5 = pd.Series([10, 20, 30, 40])

print(f"加法: {s4 + s5}")
print(f"乘法: {s4 * 2}")
print(f"布尔运算: {s4 > 2}")

# 向量化运算
print(f"平方: {s4 ** 2}")
print(f"对数: {np.log(s4)}")
```

### 1.3 DataFrame 基础

```python
# ========== 创建 DataFrame ==========
# 从字典创建
data = {
    '姓名': ['张三', '李四', '王五', '赵六'],
    '年龄': [25, 30, 35, 28],
    '城市': ['北京', '上海', '广州', '深圳'],
    '薪资': [15000, 20000, 18000, 22000]
}

df = pd.DataFrame(data)
print("从字典创建 DataFrame:")
print(df)

# 从列表创建
data_list = [
    ['张三', 25, '北京', 15000],
    ['李四', 30, '上海', 20000],
    ['王五', 35, '广州', 18000],
    ['赵六', 28, '深圳', 22000]
]

df2 = pd.DataFrame(data_list, columns=['姓名', '年龄', '城市', '薪资'])
print("\n从列表创建 DataFrame:")
print(df2)

# 从 NumPy 数组创建
arr = np.random.randn(4, 3)
df3 = pd.DataFrame(arr, columns=['A', 'B', 'C'])
print("\n从 NumPy 数组创建:")
print(df3)

# ========== DataFrame 基本属性 ==========
print(f"DataFrame 形状: {df.shape}")
print(f"DataFrame 列名: {df.columns}")
print(f"DataFrame 索引: {df.index}")
print(f"DataFrame 数据类型:\n{df.dtypes}")
print(f"DataFrame 信息:")
df.info()

# ========== 查看数据 ==========
print("前2行:")
print(df.head(2))

print("\n后2行:")
print(df.tail(2))

print("\n基本统计信息:")
print(df.describe())

print("\n唯一值:")
print(df['城市'].unique())

print("\n值计数:")
print(df['城市'].value_counts())
```

## 2. 数据读取与导出

### 2.1 读取各种格式数据

```python
# ========== 读取 CSV 文件 ==========
"""
# 创建示例 CSV 文件
with open('sample_data.csv', 'w', encoding='utf-8') as f:
    f.write('''姓名,年龄,城市,薪资,入职日期
张三,25,北京,15000,2020-01-15
李四,30,上海,20000,2019-03-20
王五,35,广州,18000,2018-07-10
赵六,28,深圳,22000,2021-05-30
钱七,32,北京,19000,2019-11-05
''')
"""

# 读取 CSV（常用参数）
df_csv = pd.read_csv('sample_data.csv', encoding='utf-8')
print("读取 CSV 文件:")
print(df_csv)

# 高级读取参数
df_csv_adv = pd.read_csv(
    'sample_data.csv',
    encoding='utf-8',
    parse_dates=['入职日期'],  # 解析日期列
    na_values=['', 'NULL'],   # 指定缺失值
    thousands=',',           # 千位分隔符
    dtype={'年龄': 'int32'}   # 指定数据类型
)

# ========== 读取 Excel 文件 ==========
"""
需要安装: pip install openpyxl xlrd
"""

# 创建示例 Excel 文件
with pd.ExcelWriter('sample_data.xlsx') as writer:
    df.to_excel(writer, sheet_name='员工信息', index=False)

# 读取 Excel
df_excel = pd.read_excel(
    'sample_data.xlsx',
    sheet_name='员工信息',
    engine='openpyxl'
)
print("\n读取 Excel 文件:")
print(df_excel)

# ========== 读取其他格式 ==========
# 从 JSON 读取
json_data = '''
[
    {"姓名": "张三", "年龄": 25, "城市": "北京"},
    {"姓名": "李四", "年龄": 30, "城市": "上海"}
]
'''

df_json = pd.read_json(json_data)
print("\n读取 JSON 数据:")
print(df_json)

# 从字典列表读取
dict_list = [
    {'姓名': '张三', '年龄': 25, '城市': '北京'},
    {'姓名': '李四', '年龄': 30, '城市': '上海'}
]

df_dict = pd.DataFrame(dict_list)
print("\n从字典列表创建:")
print(df_dict)
```

### 2.2 数据导出

```python
# ========== 导出到 CSV ==========
df.to_csv('output_data.csv', 
          index=False,           # 不保存索引
          encoding='utf-8-sig',  # 支持中文
          sep=',')              # 分隔符

# ========== 导出到 Excel ==========
with pd.ExcelWriter('output_data.xlsx') as writer:
    df.to_excel(writer, 
                sheet_name='数据',
                index=False)
    
    # 可以导出多个 sheet
    df.describe().to_excel(writer, 
                          sheet_name='统计信息',
                          index=True)

# ========== 导出到其他格式 ==========
# 导出为 JSON
df.to_json('output_data.json', 
           orient='records',  # 记录格式
           force_ascii=False) # 支持中文

# 导出为 HTML 表格
html_table = df.to_html(index=False)
print("HTML 表格:")
print(html_table)

# ========== 数据库操作 ==========
"""
# 需要安装: pip install sqlalchemy
import sqlalchemy

# 创建数据库连接
engine = sqlalchemy.create_engine('sqlite:///sample.db')

# 从数据库读取
df_sql = pd.read_sql('SELECT * FROM table_name', engine)

# 写入数据库
df.to_sql('table_name', engine, if_exists='replace', index=False)
"""
```

## 3. 数据选择与索引

### 3.1 基本选择方法

```python
# ========== 创建示例数据 ==========
data = {
    '姓名': ['张三', '李四', '王五', '赵六', '钱七'],
    '年龄': [25, 30, 35, 28, 32],
    '部门': ['技术部', '销售部', '技术部', '人事部', '销售部'],
    '城市': ['北京', '上海', '广州', '深圳', '北京'],
    '薪资': [15000, 20000, 18000, 22000, 19000],
    '奖金': [3000, 5000, 4000, 4500, 3500]
}

df = pd.DataFrame(data)
print("原始数据:")
print(df)

# ========== 列选择 ==========
# 选择单列（返回 Series）
names = df['姓名']
print(f"\n选择姓名列 (Series):\n{names}")

# 选择多列（返回 DataFrame）
subset = df[['姓名', '年龄', '薪资']]
print(f"\n选择多列 (DataFrame):\n{subset}")

# 使用点表示法（不推荐，容易出错）
ages = df.年龄
print(f"\n使用点表示法选择年龄列:\n{ages}")

# ========== 行选择 ==========
# 通过位置选择
print(f"\n前2行:\n{df.iloc[:2]}")          # 前2行
print(f"\n第2行:\n{df.iloc[1]}")           # 第2行（Series）
print(f"\n第1,3行:\n{df.iloc[[0, 2]]}")    # 第1,3行

# 通过标签选择
df_indexed = df.set_index('姓名')
print(f"\n设置姓名索引后:\n{df_indexed}")
print(f"\n选择张三:\n{df_indexed.loc['张三']}")

# ========== 布尔索引 ==========
# 简单条件
beijing_employees = df[df['城市'] == '北京']
print(f"\n北京员工:\n{beijing_employees}")

# 多条件
high_salary_tech = df[(df['部门'] == '技术部') & (df['薪资'] > 16000)]
print(f"\n技术部高薪员工:\n{high_salary_tech}")

# 复杂条件
complex_condition = df[(df['年龄'] > 25) & (df['年龄'] < 35) | (df['城市'] == '北京')]
print(f"\n复杂条件筛选:\n{complex_condition}")

# 使用 isin
cities_filter = df[df['城市'].isin(['北京', '上海'])]
print(f"\n北京或上海员工:\n{cities_filter}")
```

### 3.2 高级索引技巧

```python
# ========== loc 和 iloc 详细用法 ==========
print("使用 loc 选择行和列:")
# 选择行和特定列
selected = df.loc[df['城市'] == '北京', ['姓名', '薪资']]
print(selected)

print("\n使用 iloc 选择行和列:")
# 选择特定位置
positions = df.iloc[1:3, 0:3]  # 第1-2行，第0-2列
print(positions)

# ========== 条件赋值 ==========
# 根据条件修改数据
df.loc[df['城市'] == '北京', '地区'] = '华北'
df.loc[df['城市'] == '上海', '地区'] = '华东'
df.loc[df['城市'].isin(['广州', '深圳']), '地区'] = '华南'

print("\n添加地区列后:")
print(df)

# ========== query 方法 ==========
# 使用字符串表达式查询
young_high_salary = df.query('年龄 < 30 and 薪资 > 16000')
print(f"\n年轻高薪员工 (query):\n{young_high_salary}")

# 使用变量查询
min_age = 30
max_salary = 20000
result = df.query('年龄 >= @min_age and 薪资 <= @max_salary')
print(f"\n使用变量查询:\n{result}")

# ========== 多重索引 ==========
# 设置多重索引
df_multi = df.set_index(['部门', '城市'])
print(f"\n多重索引数据:\n{df_multi}")

# 多重索引选择
print(f"\n选择技术部:\n{df_multi.loc['技术部']}")
print(f"\n选择技术部北京员工:\n{df_multi.loc[('技术部', '北京')]}")

# ========== 索引重置 ==========
# 重置索引
df_reset = df_multi.reset_index()
print(f"\n重置索引后:\n{df_reset}")
```

## 4. 数据清洗与预处理

### 4.1 处理缺失值

```python
# ========== 创建包含缺失值的数据 ==========
data_with_na = {
    '姓名': ['张三', '李四', '王五', '赵六', '钱七', None],
    '年龄': [25, 30, None, 28, 32, 35],
    '部门': ['技术部', '销售部', '技术部', None, '销售部', '人事部'],
    '城市': ['北京', '上海', '广州', '深圳', None, '北京'],
    '薪资': [15000, 20000, 18000, None, 19000, 21000],
    '奖金': [3000, 5000, None, 4500, 3500, 4000]
}

df_na = pd.DataFrame(data_with_na)
print("包含缺失值的数据:")
print(df_na)

# ========== 检测缺失值 ==========
print(f"\n缺失值统计:")
print(df_na.isnull().sum())

print(f"\n缺失值位置:")
print(df_na.isnull())

print(f"\n数据信息:")
df_na.info()

# ========== 处理缺失值 ==========
# 删除包含缺失值的行
df_drop_na = df_na.dropna()
print(f"\n删除缺失值后:\n{df_drop_na}")

# 删除包含缺失值的列
df_drop_na_cols = df_na.dropna(axis=1)
print(f"\n删除缺失列后:\n{df_drop_na_cols}")

# 指定阈值删除
df_threshold = df_na.dropna(thresh=4)  # 至少4个非空值
print(f"\n阈值删除后:\n{df_threshold}")

# ========== 填充缺失值 ==========
# 固定值填充
df_fill_constant = df_na.fillna('未知')
print(f"\n固定值填充:\n{df_fill_constant}")

# 向前填充
df_ffill = df_na.fillna(method='ffill')
print(f"\n向前填充:\n{df_ffill}")

# 向后填充
df_bfill = df_na.fillna(method='bfill')
print(f"\n向后填充:\n{df_bfill}")

# 不同列不同填充方式
df_fill_dict = df_na.fillna({
    '年龄': df_na['年龄'].mean(),
    '部门': '未知部门',
    '城市': '未知城市',
    '薪资': df_na['薪资'].median(),
    '奖金': 0
})
print(f"\n字典方式填充:\n{df_fill_dict}")

# ========== 插值处理 ==========
# 创建时间序列数据
dates = pd.date_range('2023-01-01', periods=6, freq='D')
ts_data = pd.Series([1, np.nan, np.nan, 4, np.nan, 6], index=dates)

print(f"\n时间序列数据:\n{ts_data}")

# 线性插值
ts_linear = ts_data.interpolate(method='linear')
print(f"\n线性插值:\n{ts_linear}")

# 时间插值
ts_time = ts_data.interpolate(method='time')
print(f"\n时间插值:\n{ts_time}")
```

### 4.2 数据类型转换

```python
# ========== 数据类型检测与转换 ==========
# 创建混合类型数据
mixed_data = {
    '字符串数字': ['1', '2', '3', '4'],
    '数字字符串': [100, 200, 300, 400],
    '布尔字符串': ['True', 'False', 'True', 'False'],
    '日期字符串': ['2023-01-01', '2023-01-02', '2023-01-03', '2023-01-04']
}

df_mixed = pd.DataFrame(mixed_data)
print("混合类型数据:")
print(df_mixed.dtypes)

# 类型转换
df_converted = df_mixed.copy()
df_converted['字符串数字'] = pd.to_numeric(df_converted['字符串数字'])
df_converted['数字字符串'] = df_converted['数字字符串'].astype(str)
df_converted['布尔字符串'] = df_converted['布尔字符串'].map({'True': True, 'False': False})
df_converted['日期字符串'] = pd.to_datetime(df_converted['日期字符串'])

print(f"\n转换后数据类型:")
print(df_converted.dtypes)

# ========== 分类数据 ==========
# 创建分类数据
df_cat = df.copy()
df_cat['部门'] = df_cat['部门'].astype('category')
df_cat['城市'] = df_cat['城市'].astype('category')

print(f"\n分类数据类型:")
print(df_cat.dtypes)
print(f"\n部门分类: {df_cat['部门'].cat.categories}")
print(f"\n城市分类: {df_cat['城市'].cat.categories}")

# 分类数据操作
df_cat['部门编码'] = df_cat['部门'].cat.codes
print(f"\n添加分类编码:\n{df_cat[['部门', '部门编码']]}")

# ========== 数据去重 ==========
# 创建重复数据
duplicate_data = {
    '姓名': ['张三', '李四', '张三', '王五', '李四'],
    '年龄': [25, 30, 25, 35, 30],
    '城市': ['北京', '上海', '北京', '广州', '上海']
}

df_dup = pd.DataFrame(duplicate_data)
print(f"\n重复数据:\n{df_dup}")

# 检测重复行
print(f"\n重复行:\n{df_dup.duplicated()}")

# 删除重复行
df_no_dup = df_dup.drop_duplicates()
print(f"\n去重后:\n{df_no_dup}")

# 基于特定列去重
df_no_dup_subset = df_dup.drop_duplicates(subset=['姓名'])
print(f"\n基于姓名去重:\n{df_no_dup_subset}")
```

### 4.3 字符串处理

```python
# ========== 字符串操作 ==========
# 创建字符串数据
string_data = {
    '姓名': ['张三', '李四', '王五', '赵六'],
    '邮箱': ['zhangsan@company.com', 'lisi@company.com', 'wangwu@company.com', 'zhaoliu@company.com'],
    '电话': ['138-0011-0011', '139-0022-0022', '137-0033-0033', '136-0044-0044']
}

df_str = pd.DataFrame(string_data)
print("字符串数据:")
print(df_str)

# 字符串方法
df_str['姓名大写'] = df_str['姓名'].str.upper()
df_str['用户名'] = df_str['邮箱'].str.split('@').str[0]
df_str['电话清理'] = df_str['电话'].str.replace('-', '')
df_str['姓名长度'] = df_str['姓名'].str.len()

print(f"\n字符串处理结果:\n{df_str}")

# ========== 正则表达式 ==========
# 提取数字
df_str['区号'] = df_str['电话'].str.extract(r'(\d{3})-\d{4}-\d{4}')
df_str['中间号'] = df_str['电话'].str.extract(r'\d{3}-(\d{4})-\d{4}')

print(f"\n正则提取结果:\n{df_str[['电话', '区号', '中间号']]}")

# 字符串包含检测
contains_138 = df_str['电话'].str.contains('138')
print(f"\n包含138的电话:\n{df_str[contains_138]}")

# ========== 字符串拆分与连接 ==========
# 拆分字符串
split_result = df_str['邮箱'].str.split('@', expand=True)
split_result.columns = ['用户名', '域名']
print(f"\n邮箱拆分:\n{split_result}")

# 连接字符串
df_str['姓名邮箱'] = df_str['姓名'] + ' <' + df_str['邮箱'] + '>'
print(f"\n姓名邮箱组合:\n{df_str[['姓名', '邮箱', '姓名邮箱']]}")
```

## 5. 数据转换与操作

### 5.1 数据排序

```python
# ========== 基本排序 ==========
print("原始数据:")
print(df)

# 单列排序
df_sorted_age = df.sort_values('年龄')
print(f"\n按年龄排序:\n{df_sorted_age}")

# 多列排序
df_sorted_multi = df.sort_values(['部门', '薪资'], ascending=[True, False])
print(f"\n按部门升序、薪资降序排序:\n{df_sorted_multi}")

# ========== 索引排序 ==========
# 按索引排序
df_index_sorted = df.set_index('姓名').sort_index()
print(f"\n按姓名索引排序:\n{df_index_sorted}")

# 重置索引排序
df_reset_sorted = df_index_sorted.reset_index().sort_index(ascending=False)
print(f"\n重置索引并降序排序:\n{df_reset_sorted}")

# ========== 排名 ==========
df['薪资排名'] = df['薪资'].rank(ascending=False)
df['年龄排名'] = df['年龄'].rank(method='dense')  # 密集排名

print(f"\n添加排名列:\n{df}")

# ========== 自定义排序 ==========
# 自定义排序顺序
department_order = ['人事部', '技术部', '销售部']
df['部门'] = pd.Categorical(df['部门'], categories=department_order, ordered=True)
df_custom_sorted = df.sort_values('部门')

print(f"\n自定义部门顺序排序:\n{df_custom_sorted}")
```

### 5.2 数据分组与聚合

```python
# ========== 基本分组操作 ==========
print("分组统计 - 各部门平均薪资:")
grouped = df.groupby('部门')['薪资'].mean()
print(grouped)

print("\n多列分组:")
multi_grouped = df.groupby(['部门', '城市']).agg({
    '薪资': ['mean', 'max', 'min', 'count'],
    '年龄': 'mean'
})
print(multi_grouped)

# ========== 常用聚合函数 ==========
# 多种聚合操作
agg_result = df.groupby('部门').agg({
    '薪资': ['mean', 'sum', 'std', 'count'],
    '年龄': ['mean', 'max', 'min'],
    '奖金': 'sum'
}).round(2)

print(f"\n详细聚合统计:\n{agg_result}")

# ========== 转换操作 ==========
# 分组转换（不改变形状）
df['部门平均薪资'] = df.groupby('部门')['薪资'].transform('mean')
df['薪资部门占比'] = df['薪资'] / df.groupby('部门')['薪资'].transform('sum')

print(f"\n添加转换列:\n{df}")

# ========== 过滤操作 ==========
# 分组过滤
def filter_func(x):
    return x['薪资'].mean() > 18000

filtered = df.groupby('部门').filter(filter_func)
print(f"\n平均薪资大于18000的部门:\n{filtered}")

# ========== 应用自定义函数 ==========
# 应用自定义函数
def salary_stats(group):
    return pd.Series({
        '平均薪资': group['薪资'].mean(),
        '最高薪资': group['薪资'].max(),
        '人数': len(group),
        '薪资范围': group['薪资'].max() - group['薪资'].min()
    })

custom_agg = df.groupby('部门').apply(salary_stats)
print(f"\n自定义聚合函数:\n{custom_agg}")
```

### 5.3 数据合并与连接

```python
# ========== 创建示例数据 ==========
# 员工基本信息
employees = pd.DataFrame({
    '员工ID': [1, 2, 3, 4, 5],
    '姓名': ['张三', '李四', '王五', '赵六', '钱七'],
    '部门ID': [101, 102, 101, 103, 102]
})

# 部门信息
departments = pd.DataFrame({
    '部门ID': [101, 102, 103, 104],
    '部门名称': ['技术部', '销售部', '人事部', '财务部'],
    '经理': ['技术总监', '销售总监', '人事总监', '财务总监']
})

# 薪资信息
salaries = pd.DataFrame({
    '员工ID': [1, 2, 3, 4, 6],  # 注意ID6不存在于employees中
    '薪资': [15000, 20000, 18000, 22000, 25000],
    '奖金': [3000, 5000, 4000, 4500, 6000]
})

print("员工表:")
print(employees)
print("\n部门表:")
print(departments)
print("\n薪资表:")
print(salaries)

# ========== merge 合并 ==========
# 内连接
inner_merge = pd.merge(employees, salaries, on='员工ID', how='inner')
print(f"\n内连接结果:\n{inner_merge}")

# 左连接
left_merge = pd.merge(employees, salaries, on='员工ID', how='left')
print(f"\n左连接结果:\n{left_merge}")

# 右连接
right_merge = pd.merge(employees, salaries, on='员工ID', how='right')
print(f"\n右连接结果:\n{right_merge}")

# 外连接
outer_merge = pd.merge(employees, salaries, on='员工ID', how='outer')
print(f"\n外连接结果:\n{outer_merge}")

# 多键合并
multi_merge = pd.merge(
    pd.merge(employees, departments, on='部门ID'),
    salaries,
    on='员工ID'
)
print(f"\n多表连接结果:\n{multi_merge}")

# ========== concat 连接 ==========
# 纵向连接（相同列）
df1 = df[df['城市'] == '北京']
df2 = df[df['城市'] == '上海']
vertical_concat = pd.concat([df1, df2], ignore_index=True)
print(f"\n纵向连接:\n{vertical_concat}")

# 横向连接（相同索引）
horizontal_concat = pd.concat([df[['姓名', '年龄']], df[['城市', '薪资']]], axis=1)
print(f"\n横向连接:\n{horizontal_concat}")

# ========== join 连接 ==========
# 设置索引后连接
employees_indexed = employees.set_index('员工ID')
salaries_indexed = salaries.set_index('员工ID')

joined = employees_indexed.join(salaries_indexed, how='left')
print(f"\n索引连接:\n{joined}")
```

## 6. 高级数据分析技巧

### 6.1 数据透视表

```python
# ========== 基本数据透视表 ==========
# 创建销售数据
sales_data = {
    '日期': pd.date_range('2023-01-01', periods=100, freq='D'),
    '销售员': np.random.choice(['张三', '李四', '王五'], 100),
    '产品': np.random.choice(['手机', '电脑', '平板'], 100),
    '地区': np.random.choice(['北京', '上海', '广州', '深圳'], 100),
    '销售额': np.random.randint(1000, 10000, 100),
    '数量': np.random.randint(1, 10, 100)
}

sales_df = pd.DataFrame(sales_data)
sales_df['月份'] = sales_df['日期'].dt.month

print("销售数据样本:")
print(sales_df.head())

# 基本透视表
pivot_basic = sales_df.pivot_table(
    values='销售额',
    index='销售员',
    columns='产品',
    aggfunc='sum'
)
print(f"\n基本透视表:\n{pivot_basic}")

# ========== 复杂透视表 ==========
pivot_complex = sales_df.pivot_table(
    values=['销售额', '数量'],
    index=['销售员', '地区'],
    columns=['产品', '月份'],
    aggfunc={'销售额': ['sum', 'mean'], '数量': 'sum'},
    fill_value=0,
    margins=True  # 添加总计
)
print(f"\n复杂透视表:\n{pivot_complex}")

# ========== 交叉表 ==========
# 使用交叉表分析频率
cross_tab = pd.crosstab(
    index=sales_df['销售员'],
    columns=[sales_df['产品'], sales_df['地区']],
    margins=True
)
print(f"\n交叉表:\n{cross_tab}")
```

### 6.2 时间序列处理

```python
# ========== 时间序列创建 ==========
# 创建时间序列
date_rng = pd.date_range('2023-01-01', '2023-12-31', freq='D')
ts_data = pd.DataFrame({
    '日期': date_rng,
    '销售额': np.random.randint(1000, 5000, len(date_rng)),
    '访问量': np.random.randint(100, 1000, len(date_rng))
})

ts_data = ts_data.set_index('日期')
print("时间序列数据:")
print(ts_data.head())

# ========== 时间序列重采样 ==========
# 按周重采样
weekly = ts_data.resample('W').mean()
print(f"\n周平均:\n{weekly.head()}")

# 按月重采样，多种聚合
monthly = ts_data.resample('M').agg({
    '销售额': ['sum', 'mean', 'std'],
    '访问量': 'sum'
})
print(f"\n月统计:\n{monthly}")

# ========== 时间序列窗口计算 ==========
# 移动平均
ts_data['7天移动平均'] = ts_data['销售额'].rolling(window=7).mean()
ts_data['30天移动平均'] = ts_data['销售额'].rolling(window=30).mean()

# 扩展窗口
ts_data['累计销售额'] = ts_data['销售额'].expanding().sum()

print(f"\n添加移动平均:\n{ts_data.tail(10)}")

# ========== 时间序列差分 ==========
# 计算日环比
ts_data['销售额日环比'] = ts_data['销售额'].pct_change() * 100

# 计算月同比（需要相同月份数据）
ts_data['月份'] = ts_data.index.month
monthly_growth = ts_data.groupby('月份')['销售额'].pct_change() * 100

print(f"\n添加增长率:\n{ts_data[['销售额', '销售额日环比']].tail()}")
```

### 6.3 性能优化技巧

```python
# ========== 数据类型优化 ==========
# 查看内存使用
print("原始数据内存使用:")
print(df.info(memory_usage='deep'))

# 优化数据类型
def optimize_dtypes(df):
    result = df.copy()
    
    # 整数列优化
    int_cols = result.select_dtypes(include=['int']).columns
    for col in int_cols:
        result[col] = pd.to_numeric(result[col], downcast='integer')
    
    # 浮点列优化
    float_cols = result.select_dtypes(include=['float']).columns
    for col in float_cols:
        result[col] = pd.to_numeric(result[col], downcast='float')
    
    # 对象列转为分类
    obj_cols = result.select_dtypes(include=['object']).columns
    for col in obj_cols:
        if result[col].nunique() / len(result[col]) < 0.5:  # 唯一值比例小于50%
            result[col] = result[col].astype('category')
    
    return result

df_optimized = optimize_dtypes(df)
print("\n优化后内存使用:")
print(df_optimized.info(memory_usage='deep'))

# ========== 使用查询优化 ==========
# 创建大型数据集
large_df = pd.DataFrame({
    'A': np.random.randn(1000000),
    'B': np.random.randn(1000000),
    'C': np.random.choice(['X', 'Y', 'Z'], 1000000),
    'D': np.random.randint(0, 100, 1000000)
})

# 传统方式 vs query方式
import time

# 传统布尔索引
start_time = time.time()
traditional = large_df[(large_df['A'] > 0) & (large_df['D'] < 50)]
traditional_time = time.time() - start_time

# query方法
start_time = time.time()
query_result = large_df.query('A > 0 and D < 50')
query_time = time.time() - start_time

print(f"\n性能比较:")
print(f"传统方式: {traditional_time:.4f}秒")
print(f"Query方式: {query_time:.4f}秒")

# ========== 使用 NumPy 加速 ==========
# Pandas操作
start_time = time.time()
pandas_result = large_df['A'] + large_df['B']
pandas_time = time.time() - start_time

# NumPy操作
start_time = time.time()
numpy_result = large_df['A'].values + large_df['B'].values
numpy_time = time.time() - start_time

print(f"\nNumPy加速比较:")
print(f"Pandas方式: {pandas_time:.4f}秒")
print(f"NumPy方式: {numpy_time:.4f}秒")
```

## 7. 易错点与解决方案

### 7.1 常见陷阱

```python
# ========== SettingWithCopyWarning ==========
print("=== SettingWithCopyWarning 问题 ===")

# 错误示例
df_chained = df[df['年龄'] > 25]
try:
    df_chained['新列'] = 100  # 这会触发警告
    print("链式赋值完成")
except Exception as e:
    print(f"错误: {e}")

# 正确做法1: 使用 copy()
df_safe = df[df['年龄'] > 25].copy()
df_safe['新列'] = 100
print("使用copy()安全赋值")

# 正确做法2: 使用 loc[]
df.loc[df['年龄'] > 25, '新列'] = 100
print("使用loc[]安全赋值")

# ========== 整数NA问题 ==========
print("\n=== 整数NA问题 ===")

# 错误示例
try:
    int_df = pd.DataFrame({'A': [1, 2, 3]})
    int_df.loc[0, 'A'] = np.nan  # 这会报错
except Exception as e:
    print(f"错误: {e}")

# 解决方案: 使用可空整数类型
int_df = pd.DataFrame({'A': [1, 2, 3]})
int_df['A'] = int_df['A'].astype('Int64')  # 大写的I
int_df.loc[0, 'A'] = pd.NA
print("使用可空整数类型:")
print(int_df)

# ========== 日期处理问题 ==========
print("\n=== 日期处理问题 ===")

# 混合日期格式
mixed_dates = ['2023-01-01', '01/02/2023', '2023.03.03']
try:
    bad_dates = pd.to_datetime(mixed_dates)
except Exception as e:
    print(f"错误: {e}")

# 解决方案: 指定格式或使用 errors 参数
good_dates = pd.to_datetime(mixed_dates, errors='coerce')
print("处理混合日期格式:")
print(good_dates)

# ========== 分组聚合问题 ==========
print("\n=== 分组聚合问题 ===")

# 分组后列选择
grouped = df.groupby('部门')
try:
    # 错误: 直接选择不存在的列
    result = grouped['不存在的列'].mean()
except KeyError as e:
    print(f"错误: {e}")

# 正确做法: 先检查列是否存在
if '薪资' in df.columns:
    result = grouped['薪资'].mean()
    print("分组聚合成功")
```

### 7.2 内存优化技巧

```python
# ========== 大数据集处理技巧 ==========
print("=== 大数据集处理技巧 ===")

# 1. 分块读取
def process_large_csv(file_path, chunk_size=10000):
    """分块处理大CSV文件"""
    chunks = []
    for chunk in pd.read_csv(file_path, chunksize=chunk_size):
        # 处理每个分块
        processed_chunk = chunk[chunk['年龄'] > 25]  # 示例过滤
        chunks.append(processed_chunk)
    
    return pd.concat(chunks, ignore_index=True)

print("分块读取函数定义完成")

# 2. 使用合适的数据类型
def reduce_memory_usage(df):
    """减少内存使用"""
    start_mem = df.memory_usage(deep=True).sum() / 1024**2
    print(f"初始内存使用: {start_mem:.2f} MB")
    
    for col in df.columns:
        col_type = df[col].dtype
        
        if col_type == object:
            # 对象类型转为分类
            if df[col].nunique() / len(df[col]) < 0.5:
                df[col] = df[col].astype('category')
        elif col_type.name.startswith('int'):
            # 整数类型下转换
            c_min = df[col].min()
            c_max = df[col].max()
            if c_min > np.iinfo(np.int8).min and c_max < np.iinfo(np.int8).max:
                df[col] = df[col].astype(np.int8)
            elif c_min > np.iinfo(np.int16).min and c_max < np.iinfo(np.int16).max:
                df[col] = df[col].astype(np.int16)
            elif c_min > np.iinfo(np.int32).min and c_max < np.iinfo(np.int32).max:
                df[col] = df[col].astype(np.int32)
            else:
                df[col] = df[col].astype(np.int64)
        elif col_type.name.startswith('float'):
            # 浮点类型下转换
            c_min = df[col].min()
            c_max = df[col].max()
            if c_min > np.finfo(np.float32).min and c_max < np.finfo(np.float32).max:
                df[col] = df[col].astype(np.float32)
            else:
                df[col] = df[col].astype(np.float64)
    
    end_mem = df.memory_usage(deep=True).sum() / 1024**2
    print(f"优化后内存使用: {end_mem:.2f} MB")
    print(f"减少: {100 * (start_mem - end_mem) / start_mem:.1f}%")
    
    return df

# 测试内存优化
large_example = pd.DataFrame({
    'int_col': np.random.randint(0, 100, 100000),
    'float_col': np.random.randn(100000),
    'category_col': np.random.choice(['A', 'B', 'C', 'D'], 100000),
    'bool_col': np.random.choice([True, False], 100000)
})

optimized_df = reduce_memory_usage(large_example)
```

## 8. 实用技巧与最佳实践

### 8.1 数据验证与测试

```python
# ========== 数据质量检查 ==========
def data_quality_report(df):
    """生成数据质量报告"""
    report = {
        '总行数': len(df),
        '总列数': len(df.columns),
        '缺失值统计': df.isnull().sum().to_dict(),
        '重复行数': df.duplicated().sum(),
        '数据类型分布': df.dtypes.value_counts().to_dict(),
        '内存使用(MB)': df.memory_usage(deep=True).sum() / 1024**2
    }
    
    # 数值列统计
    numeric_cols = df.select_dtypes(include=[np.number]).columns
    if len(numeric_cols) > 0:
        report['数值列统计'] = df[numeric_cols].describe().to_dict()
    
    # 分类列统计
    categorical_cols = df.select_dtypes(include=['object', 'category']).columns
    if len(categorical_cols) > 0:
        categorical_stats = {}
        for col in categorical_cols:
            categorical_stats[col] = {
                '唯一值数量': df[col].nunique(),
                '最常见值': df[col].mode().iloc[0] if not df[col].mode().empty else None,
                '最常见值频次': df[col].value_counts().iloc[0] if not df[col].value_counts().empty else 0
            }
        report['分类列统计'] = categorical_stats
    
    return report

# 生成数据质量报告
quality_report = data_quality_report(df)
print("数据质量报告:")
for key, value in quality_report.items():
    print(f"{key}: {value}")

# ========== 数据验证 ==========
def validate_data(df, rules):
    """数据验证函数"""
    violations = []
    
    for rule_name, rule in rules.items():
        try:
            if not rule(df):
                violations.append(rule_name)
        except Exception as e:
            violations.append(f"{rule_name} (错误: {e})")
    
    return violations

# 定义验证规则
validation_rules = {
    '年龄非负': lambda df: (df['年龄'] >= 0).all(),
    '薪资合理范围': lambda df: ((df['薪资'] >= 0) & (df['薪资'] <= 1000000)).all(),
    '城市非空': lambda df: df['城市'].notnull().all(),
    '部门有效性': lambda df: df['部门'].isin(['技术部', '销售部', '人事部']).all()
}

# 执行验证
violations = validate_data(df, validation_rules)
if violations:
    print(f"\n数据验证失败: {violations}")
else:
    print("\n数据验证通过")
```

### 8.2 高效数据处理模式

```python
# ========== 管道操作模式 ==========
def data_processing_pipeline(df):
    """数据处理管道"""
    from functools import reduce
    
    # 定义处理步骤
    processing_steps = [
        # 步骤1: 数据类型转换
        lambda x: x.astype({
            '姓名': 'category',
            '部门': 'category',
            '城市': 'category'
        }),
        
        # 步骤2: 处理缺失值
        lambda x: x.fillna({
            '年龄': x['年龄'].median(),
            '薪资': x['薪资'].median(),
            '奖金': 0
        }),
        
        # 步骤3: 数据增强
        lambda x: x.assign(
            总薪资=x['薪资'] + x['奖金'],
            年龄分组=pd.cut(x['年龄'], bins=[0, 25, 30, 35, 100], 
                          labels=['25以下', '26-30', '31-35', '35以上'])
        ),
        
        # 步骤4: 异常值处理
        lambda x: x[(x['薪资'] >= x['薪资'].quantile(0.01)) & 
                    (x['薪资'] <= x['薪资'].quantile(0.99))]
    ]
    
    # 应用所有处理步骤
    result = reduce(lambda data, func: func(data), processing_steps, df.copy())
    return result

# 使用管道处理数据
processed_df = data_processing_pipeline(df)
print("管道处理结果:")
print(processed_df)

# ========== 配置化数据处理 ==========
class DataProcessor:
    """配置化数据处理器"""
    
    def __init__(self, config):
        self.config = config
    
    def process(self, df):
        result = df.copy()
        
        # 类型转换
        if 'dtype_conversions' in self.config:
            result = result.astype(self.config['dtype_conversions'])
        
        # 缺失值处理
        if 'fillna_values' in self.config:
            result = result.fillna(self.config['fillna_values'])
        
        # 数据过滤
        if 'filters' in self.config:
            for filter_condition in self.config['filters']:
                result = result.query(filter_condition)
        
        # 列重命名
        if 'rename_columns' in self.config:
            result = result.rename(columns=self.config['rename_columns'])
        
        return result

# 配置示例
config = {
    'dtype_conversions': {
        '部门': 'category',
        '城市': 'category'
    },
    'fillna_values': {
        '奖金': 0
    },
    'filters': [
        '年龄 >= 25',
        '薪资 > 15000'
    ],
    'rename_columns': {
        '薪资': '月薪',
        '奖金': '绩效奖金'
    }
}

# 使用配置化处理器
processor = DataProcessor(config)
config_processed = processor.process(df)
print("\n配置化处理结果:")
print(config_processed)
```

## 9. 实战案例

### 9.1 电商数据分析

```python
# ========== 电商数据模拟与分析 ==========
# 生成模拟电商数据
np.random.seed(42)
n_customers = 1000

ecommerce_data = pd.DataFrame({
    '订单ID': range(1, n_customers + 1),
    '客户ID': np.random.randint(1000, 2000, n_customers),
    '订单日期': pd.date_range('2023-01-01', periods=n_customers, freq='H'),
    '产品类别': np.random.choice(['电子产品', '服装', '家居', '食品', '图书'], n_customers),
    '销售额': np.random.exponential(100, n_customers),
    '数量': np.random.randint(1, 5, n_customers),
    '支付方式': np.random.choice(['信用卡', '支付宝', '微信支付', '银行卡'], n_customers),
    '城市': np.random.choice(['北京', '上海', '广州', '深圳', '杭州', '成都'], n_customers)
})

# 添加一些缺失值
missing_indices = np.random.choice(ecommerce_data.index, size=50, replace=False)
ecommerce_data.loc[missing_indices, '销售额'] = np.nan

print("电商数据样本:")
print(ecommerce_data.head())

# ========== 销售分析 ==========
# 1. 总体销售统计
print("\n=== 总体销售分析 ===")
total_sales = ecommerce_data['销售额'].sum()
avg_order_value = ecommerce_data['销售额'].mean()
orders_per_day = ecommerce_data.groupby(ecommerce_data['订单日期'].dt.date).size()

print(f"总销售额: ¥{total_sales:,.2f}")
print(f"平均订单价值: ¥{avg_order_value:.2f}")
print(f"日均订单数: {orders_per_day.mean():.1f}")

# 2. 按类别分析
print("\n=== 产品类别分析 ===")
category_analysis = ecommerce_data.groupby('产品类别').agg({
    '销售额': ['sum', 'mean', 'count'],
    '数量': 'sum'
}).round(2)

category_analysis.columns = ['总销售额', '平均销售额', '订单数', '总数量']
print(category_analysis)

# 3. 时间序列分析
print("\n=== 时间序列分析 ===")
ecommerce_data['小时'] = ecommerce_data['订单日期'].dt.hour
ecommerce_data['星期'] = ecommerce_data['订单日期'].dt.day_name()

hourly_sales = ecommerce_data.groupby('小时')['销售额'].mean()
weekly_sales = ecommerce_data.groupby('星期')['销售额'].sum()

print("小时平均销售额:")
print(hourly_sales)
print("\n每周销售额:")
print(weekly_sales)

# 4. 客户行为分析
print("\n=== 客户行为分析 ===")
customer_behavior = ecommerce_data.groupby('客户ID').agg({
    '订单ID': 'count',
    '销售额': ['sum', 'mean'],
    '产品类别': lambda x: x.mode()[0] if not x.mode().empty else '未知'
}).round(2)

customer_behavior.columns = ['订单数', '总消费', '平均订单价值', '最常购买类别']
print("客户行为统计:")
print(customer_behavior.describe())

# 5. 高级分析：RFM模型
print("\n=== RFM 分析 ===")
# 计算RFM值
max_date = ecommerce_data['订单日期'].max()

rfm_data = ecommerce_data.groupby('客户ID').agg({
    '订单日期': lambda x: (max_date - x.max()).days,  # Recency
    '订单ID': 'count',                               # Frequency  
    '销售额': 'sum'                                  # Monetary
}).reset_index()

rfm_data.columns = ['客户ID', 'Recency', 'Frequency', 'Monetary']

# RFM评分
rfm_data['R_Score'] = pd.qcut(rfm_data['Recency'], 4, labels=[4, 3, 2, 1])
rfm_data['F_Score'] = pd.qcut(rfm_data['Frequency'], 4, labels=[1, 2, 3, 4])
rfm_data['M_Score'] = pd.qcut(rfm_data['Monetary'], 4, labels=[1, 2, 3, 4])

rfm_data['RFM_Score'] = (rfm_data['R_Score'].astype(str) + 
                        rfm_data['F_Score'].astype(str) + 
                        rfm_data['M_Score'].astype(str))

print("RFM分析结果:")
print(rfm_data.head())

# RFM客户分群
def rfm_segment(row):
    if row['R_Score'] >= 3 and row['F_Score'] >= 3 and row['M_Score'] >= 3:
        return '高价值客户'
    elif row['R_Score'] >= 3 and row['F_Score'] >= 2:
        return '潜力客户'
    elif row['R_Score'] >= 2:
        return '一般客户'
    else:
        return '流失客户'

rfm_data['客户分群'] = rfm_data.apply(rfm_segment, axis=1)
segment_summary = rfm_data['客户分群'].value_counts()
print("\n客户分群统计:")
print(segment_summary)
```

### 9.2 数据可视化集成

```python
# ========== 与Matplotlib集成 ==========
import matplotlib.pyplot as plt

# 设置中文字体
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 销售分析可视化
fig, axes = plt.subplots(2, 2, figsize=(15, 10))

# 1. 产品类别销售分布
category_sales = ecommerce_data.groupby('产品类别')['销售额'].sum()
category_sales.plot(kind='bar', ax=axes[0,0], title='各产品类别销售额', color='skyblue')
axes[0,0].set_ylabel('销售额')

# 2. 时间趋势
daily_sales = ecommerce_data.groupby(ecommerce_data['订单日期'].dt.date)['销售额'].sum()
daily_sales.plot(ax=axes[0,1], title='日销售额趋势', color='orange')
axes[0,1].set_ylabel('日销售额')

# 3. 支付方式分布
payment_dist = ecommerce_data['支付方式'].value_counts()
payment_dist.plot(kind='pie', ax=axes[1,0], title='支付方式分布', autopct='%1.1f%%')

# 4. 城市销售对比
city_sales = ecommerce_data.groupby('城市')['销售额'].sum()
city_sales.plot(kind='bar', ax=axes[1,1], title='各城市销售额', color='lightgreen')
axes[1,1].set_ylabel('销售额')

plt.tight_layout()
plt.show()

# ========== 与Seaborn集成 ==========
import seaborn as sns

# 创建分析数据
analysis_data = ecommerce_data.copy()
analysis_data['订单周'] = analysis_data['订单日期'].dt.isocalendar().week

# 多维度分析可视化
fig, axes = plt.subplots(2, 2, figsize=(15, 10))

# 1. 销售额分布
sns.histplot(data=analysis_data, x='销售额', ax=axes[0,0], kde=True)
axes[0,0].set_title('销售额分布')

# 2. 类别与销售额关系
sns.boxplot(data=analysis_data, x='产品类别', y='销售额', ax=axes[0,1])
axes[0,1].set_title('各产品类别销售额分布')
axes[0,1].tick_params(axis='x', rotation=45)

# 3. 时间趋势热力图
pivot_data = analysis_data.pivot_table(
    values='销售额', 
    index='小时', 
    columns='星期', 
    aggfunc='mean'
)
sns.heatmap(pivot_data, ax=axes[1,0], cmap='YlOrRd')
axes[1,0].set_title('销售热力图（小时×星期）')

# 4. 城市与支付方式交叉分析
cross_data = analysis_data.groupby(['城市', '支付方式']).size().unstack()
cross_data.plot(kind='bar', ax=axes[1,1], stacked=True)
axes[1,1].set_title('各城市支付方式分布')
axes[1,1].legend(bbox_to_anchor=(1.05, 1), loc='upper left')

plt.tight_layout()
plt.show()
```

## 10. 总结

### 10.1 Pandas 核心要点总结

```python
"""
Pandas 学习要点总结：

1. 核心数据结构
   - Series: 一维带标签数组
   - DataFrame: 二维表格数据结构
   - Index: 索引对象

2. 数据读取与导出
   - 支持多种格式：CSV、Excel、JSON、SQL等
   - 注意编码和数据类型指定

3. 数据选择与索引
   - loc: 基于标签的选择
   - iloc: 基于位置的选择
   - 布尔索引：条件筛选
   - query方法：字符串表达式查询

4. 数据清洗
   - 处理缺失值：dropna(), fillna(), interpolate()
   - 数据类型转换：astype(), to_numeric(), to_datetime()
   - 数据去重：drop_duplicates()
   - 字符串处理：str方法

5. 数据转换
   - 排序：sort_values(), sort_index()
   - 分组聚合：groupby(), agg(), transform()
   - 数据合并：merge(), concat(), join()

6. 高级功能
   - 数据透视表：pivot_table()
   - 时间序列：resample(), rolling()
   - 分类数据：category类型

7. 性能优化
   - 合适的数据类型
   - 向量化操作
   - 避免链式赋值
   - 使用query方法

8. 易错点
   - SettingWithCopyWarning
   - 整数NA问题
   - 日期格式处理
   - 内存使用优化

9. 最佳实践
   - 数据质量验证
   - 管道式处理
   - 配置化操作
   - 适当的文档和测试
"""

# ========== 常用操作速查表 ==========
cheat_sheet = {
    '数据查看': [
        "df.head()", "df.tail()", "df.info()", "df.describe()",
        "df.shape", "df.columns", "df.dtypes"
    ],
    '数据选择': [
        "df[col]", "df[[col1, col2]]", "df.loc[]", "df.iloc[]",
        "df.query()", "df.boolean_indexing"
    ],
    '数据清洗': [
        "df.dropna()", "df.fillna()", "df.astype()", 
        "pd.to_datetime()", "df.drop_duplicates()"
    ],
    '数据转换': [
        "df.sort_values()", "df.groupby()", "df.agg()",
        "pd.merge()", "pd.concat()", "df.pivot_table()"
    ],
    '字符串处理': [
        "df.str.upper()", "df.str.contains()", "df.str.split()",
        "df.str.replace()", "df.str.extract()"
    ],
    '时间序列': [
        "pd.to_datetime()", "df.resample()", "df.rolling()",
        "df.dt.year", "df.dt.month", "df.dt.day"
    ]
}

print("Pandas 常用操作速查表:")
for category, operations in cheat_sheet.items():
    print(f"\n{category}:")
    for op in operations:
        print(f"  - {op}")
```

### 10.2 学习资源推荐

```python
"""
进一步学习资源：

官方文档：
- Pandas官方文档：https://pandas.pydata.org/docs/
- 用户指南：https://pandas.pydata.org/docs/user_guide/index.html
- API参考：https://pandas.pydata.org/docs/reference/index.html

实践项目：
1. 数据分析项目：泰坦尼克号生存预测
2. 时间序列分析：股票价格预测
3. 文本数据分析：新闻分类
4. 网络数据采集与分析

相关库：
- NumPy: 数值计算基础
- Matplotlib/Seaborn: 数据可视化
- Scikit-learn: 机器学习
- Dask: 大数据处理

记住：实践是最好的学习方法！
多写代码，多解决实际问题，才能真正掌握Pandas。
"""
```

这份超详细的Pandas笔记涵盖了从基础到高级的各个方面，包括：
- 核心概念和数据结构
- 数据读取导出
- 数据选择索引
- 数据清洗预处理
- 数据转换操作
- 高级分析技巧
- 性能优化
- 易错点解析
- 实战案例
- 最佳实践

通过系统学习和实践这些内容，你将能够熟练使用Pandas进行各种数据分析任务。