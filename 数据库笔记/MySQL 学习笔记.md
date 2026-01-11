
## 一、`MySQL` 基础认知

### 1.1 什么是`MySQL`
`MySQL`是一款**开源的关系型数据库管理系统（`RDBMS`）**，基于`SQL`（结构化查询语言）实现数据的存储、管理、查询和维护，具有轻量、高效、跨平台、易扩展的特点，是`Web`开发（电商、后台系统、数据分析）的主流数据库。

### 1.2 核心概念
| 概念         | 解释                                                                 |
|--------------|----------------------------------------------------------------------|
| 数据库（`DB`） | 存储数据的容器，一个`MySQL`实例可包含多个数据库（如`test_db`、`user_db`）|
| 表（`Table`）  | 数据库的基本存储单元，由行（记录）和列（字段）组成（如`user`表：`id`、`name`、`age`） |
| 字段（`Column`）| 表的列，定义数据类型（`INT`/`VARCHAR`/`DATE`）和约束（主键/非空/唯一）|
| 记录（`Row`）  | 表的行，是一条具体的数据（如`id=1`， `name="张三"`， `age=20`）|
| 主键（`PK`）   | 唯一标识表中记录的字段，非空且唯一（如`id`字段，通常自增）|
| 外键（`FK`）   | 关联两个表的字段，保证数据完整性（如订单表`user_id`关联用户表`id`）|
| 索引（`Index`） | 提升查询效率的数据结构（类似书籍目录，主流：主键索引、普通索引）|
| 事务（`Transaction`） | 一组原子性的操作，要么全部成功，要么全部失败（`ACID`特性）|

### 1.3 版本选择
- **社区版（`MySQL` `Community` `Server`）**：免费开源，适合个人/企业开发、生产环境；
- **企业版（`MySQL` `Enterprise` `Edition`）**：收费，含官方技术支持、监控工具，适合大型企业；
- **推荐版本**：`MySQL` `8.0`（主流，支持窗口函数、`JSON`、更好的性能），兼容`MySQL` `5.7`（经典稳定版）。

## 二、环境安装与基础配置

### 2.1 安装方式（主流）
| 系统       | 安装方式                          | 核心命令/说明                                                                 |
|------------|-----------------------------------|------------------------------------------------------------------------------|
| `Linux`（`CentOS`） | `YUM` 源安装                        | `sudo yum install -y mysql-community-server`                                 |
| `Linux`（`Ubuntu`） | `APT` 源安装                        | `sudo apt update && sudo apt install -y mysql-server`                        |
| `Windows`    | 安装包（`.msi`）/ 免安装版（`.zip`）  | 下载地址：https://dev.mysql.com/downloads/mysql/，按向导下一步安装            |
| 通用       | `Docker` 快速部署（推荐）           | `docker run -d --name mysql80 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:8.0` |

### 2.2 服务管理（系统层面）
#### 2.2.1 `Linux`（`Systemd`）
```bash
# 启动服务
sudo systemctl start mysqld
# 停止服务
sudo systemctl stop mysqld
# 重启服务
sudo systemctl restart mysqld
# 查看状态
sudo systemctl status mysqld
# 设置开机自启
sudo systemctl enable mysqld
# 查看初始密码（CentOS安装后）
sudo grep 'temporary password' /var/log/mysqld.log
```
bZ-Y?f/7SWPV
#### 2.2.2 `Windows`（管理员`CMD`/`PowerShell`）
```bash
# 启动服务（服务名通常为MySQL80/MySQL57）
net start MySQL80
# 停止服务
net stop MySQL80
# 查看已安装的MySQL服务
sc query | findstr "MySQL"
```

### 2.3 首次登录与密码重置
#### 2.3.1 登录命令
```bash
# 本地登录（推荐，密码不明文显示）
mysql -u root -p
# 远程登录（指定IP/端口）
mysql -u root -p -h 192.168.1.100 -P 3306
# 退出MySQL
exit; 或 quit;
```

#### 2.3.2 重置`root`密码（忘记密码时）
##### `Linux` 步骤：
1. 停止服务：`sudo systemctl stop mysqld`
2. 跳过权限验证启动：`sudo mysqld_safe --skip-grant-tables --skip-networking &`
3. 免密登录：`mysql -u root`
4. 修改密码并刷新权限：
```sql
-- MySQL 8.0+
ALTER USER 'root'@'localhost' IDENTIFIED BY 'NewRoot@123456';
-- MySQL 5.7
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('NewRoot@123456');
-- 刷新权限
FLUSH PRIVILEGES;
```
5. 重启服务：`sudo systemctl restart mysqld`

### 2.4 配置文件（永久修改参数）
#### 2.4.1 配置文件路径
- `Linux`：`/etc/my.cnf` 或 `/etc/mysql/my.cnf`
- `Windows`：`my.ini`（安装目录/`C:\ProgramData\MySQL\MySQL Server 8.0\my.ini`）

#### 2.4.2 核心配置项（示例）
```ini
[mysqld]
# 基础配置
port = 3306                    # 端口
datadir = /var/lib/mysql       # 数据存储目录（Linux）
socket = /var/lib/mysql/mysql.sock # 套接字文件（Linux）
# 字符集（避免中文乱码）
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
# 性能配置
max_connections = 1000         # 最大连接数
lower_case_table_names = 1     # 表名不区分大小写（Linux默认区分）
skip-name-resolve              # 跳过DNS解析，提升连接速度
# 远程连接（注释bind-address，或设为0.0.0.0）
# bind-address = 127.0.0.1

[mysql]
default-character-set = utf8mb4 # 客户端字符集
```
修改配置后需**重启`MySQL`服务**生效。

## 三、数据库与表的核心操作（`DDL`/`DML`）

### 3.1 数据库操作
#### 3.1.1 创建数据库
```sql
-- 推荐写法：指定字符集，避免重复创建报错
CREATE DATABASE IF NOT EXISTS test_db 
DEFAULT CHARACTER SET utf8mb4 
COLLATE utf8mb4_unicode_ci;
```

#### 3.1.2 查看/选择/删除数据库
```sql
-- 查看所有数据库
SHOW DATABASES;
-- 查看数据库创建语句
SHOW CREATE DATABASE test_db;
-- 选择要操作的数据库（必须执行）
USE test_db;
-- 删除数据库（谨慎！不可逆）
DROP DATABASE IF EXISTS test_db;
```

### 3.2 表操作（`DDL`）
#### 3.2.1 创建表
```sql
-- 创建用户表（含主键、非空、默认值、注释、自增）
CREATE TABLE IF NOT EXISTS tb_user (
    id INT UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '用户ID（主键）',
    username VARCHAR(50) NOT NULL UNIQUE COMMENT '用户名（唯一）',
    password VARCHAR(100) NOT NULL COMMENT '密码（加密存储）',
    age TINYINT UNSIGNED DEFAULT 0 COMMENT '年龄（0-255）',
    gender ENUM('男','女','未知') DEFAULT '未知' COMMENT '性别',
    create_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    update_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
    PRIMARY KEY (id) COMMENT '主键索引'
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='用户信息表';
```
- `ENGINE=InnoDB`：主流存储引擎，支持事务、外键、行锁；
- `CURRENT_TIMESTAMP ON UPDATE`：更新记录时自动刷新时间。

#### 3.2.2 查看/修改/删除表
```sql
-- 查看当前数据库所有表
SHOW TABLES;
-- 查看表结构（核心）
DESC tb_user;
-- 查看表完整创建语句
SHOW CREATE TABLE tb_user;

-- 修改表名
ALTER TABLE tb_user RENAME TO user_info;
-- 添加字段
ALTER TABLE tb_user ADD COLUMN email VARCHAR(100) DEFAULT '' COMMENT '邮箱' AFTER age;
-- 修改字段（类型/默认值）
ALTER TABLE tb_user MODIFY COLUMN age INT UNSIGNED DEFAULT 0 COMMENT '年龄';
-- 删除字段
ALTER TABLE tb_user DROP COLUMN email;

-- 删除表（谨慎）
DROP TABLE IF EXISTS tb_user;
-- 清空表数据（保留结构，自增重置）
TRUNCATE TABLE tb_user;
```

### 3.3 数据操作（`DML`）
#### 3.3.1 插入数据（`INSERT`）
```sql
-- 单条插入
INSERT INTO tb_user (username, password, age, gender)
VALUES ('zhangsan', '123456', 20, '男');

-- 多条插入（效率更高）
INSERT INTO tb_user (username, password, age, gender)
VALUES 
('lisi', '654321', 22, '女'),
('wangwu', '987654', 25, '男');

-- 插入查询结果
INSERT INTO tb_user (username, age)
SELECT name, age FROM temp_user WHERE status = 1;
```

#### 3.3.2 查询数据（`SELECT`，核心）
```sql
-- 基础查询
SELECT id, username, age FROM tb_user;
-- 去重查询
SELECT DISTINCT age FROM tb_user;
-- 条件查询
SELECT * FROM tb_user WHERE age > 20 AND gender = '男';
-- 模糊查询
SELECT * FROM tb_user WHERE username LIKE '张%'; -- 以张开头
-- 范围查询
SELECT * FROM tb_user WHERE age BETWEEN 20 AND 30;
SELECT * FROM tb_user WHERE id IN (1, 3, 5);
-- 排序（ASC升序，DESC降序）
SELECT * FROM tb_user ORDER BY age DESC, id ASC;
-- 分页查询（LIMIT 偏移量, 条数）
SELECT * FROM tb_user LIMIT 5, 10; -- 第6条开始，查10条（第2页，每页10条）
-- 聚合查询
SELECT 
    COUNT(*) AS total, -- 总记录数
    MAX(age) AS max_age, -- 最大年龄
    AVG(age) AS avg_age  -- 平均年龄
FROM tb_user;
-- 分组查询+筛选
SELECT gender, COUNT(*) AS count 
FROM tb_user 
GROUP BY gender 
HAVING count > 1;
-- 多表关联查询（左连接）
SELECT u.username, o.order_no 
FROM tb_user u
LEFT JOIN tb_order o ON u.id = o.user_id;
```

#### 3.3.3 更新数据（`UPDATE`）
```sql
-- 单条更新（必须加WHERE，否则更新全表）
UPDATE tb_user 
SET age = 21, update_time = NOW() 
WHERE id = 1;
```

#### 3.3.4 删除数据（`DELETE`）
```sql
-- 单条删除（必须加WHERE，否则删除全表）
DELETE FROM tb_user WHERE id = 2;
```

## 四、用户与权限管理

### 4.1 用户管理
```sql
-- 创建本地用户（仅本机登录）
CREATE USER 'test_user'@'localhost' IDENTIFIED BY 'Test@123456';
-- 创建远程用户（允许任意IP登录）
CREATE USER 'test_user'@'%' IDENTIFIED BY 'Test@123456';
-- 修改密码（8.0+）
ALTER USER 'test_user'@'%' IDENTIFIED BY 'NewTest@654321';
-- 删除用户
DROP USER 'test_user'@'%';
```

### 4.2 权限管理
```sql
-- 授予权限（最小权限原则）
-- 授予test_user对test_db所有表的查询/插入权限
GRANT SELECT, INSERT ON test_db.* TO 'test_user'@'%';
-- 授予所有权限（谨慎）
GRANT ALL PRIVILEGES ON test_db.* TO 'test_user'@'%';
-- 刷新权限（授权后必须执行）
FLUSH PRIVILEGES;

-- 查看用户权限
SHOW GRANTS FOR 'test_user'@'%';

-- 撤销权限
REVOKE INSERT ON test_db.* FROM 'test_user'@'%';
```

## 五、`Python` 操作 `MySQL` 实战

### 5.1 环境准备
```bash
# 安装pymysql（主流库）
pip install pymysql
```

### 5.2 核心操作封装
```python
import pymysql
from pymysql import Error

# 1. 获取数据库连接
def get_mysql_conn():
    """封装连接函数，便于复用"""
    try:
        conn = pymysql.connect(
            host='127.0.0.1',
            port=3306,
            user='test_user',
            password='Test@123456',
            database='test_db',
            charset='utf8mb4'
        )
        print("连接成功")
        return conn
    except Error as e:
        print(f"连接失败：{e}")
        return None

# 2. 关闭连接
def close_conn(conn, cursor=None):
    """关闭游标和连接，避免资源泄漏"""
    if cursor:
        cursor.close()
    if conn and conn.open:
        conn.close()
        print("连接已关闭")

# 3. 查询数据（防SQL注入）
def query_data():
    conn = get_mysql_conn()
    if not conn:
        return
    try:
        # 字典格式返回结果
        cursor = conn.cursor(pymysql.cursors.DictCursor)
        # 参数化查询（%s是占位符，参数用元组传递）
        sql = "SELECT id, username, age FROM tb_user WHERE age > %s"
        cursor.execute(sql, (20,))
        # 获取所有结果
        results = cursor.fetchall()
        for row in results:
            print(f"ID：{row['id']}，用户名：{row['username']}")
    except Error as e:
        print(f"查询失败：{e}")
    finally:
        close_conn(conn, cursor)

# 4. 新增/修改/删除数据（事务控制）
def modify_data():
    conn = get_mysql_conn()
    if not conn:
        return
    try:
        cursor = conn.cursor()
        # 新增数据
        insert_sql = "INSERT INTO tb_user (username, password, age) VALUES (%s, %s, %s)"
        cursor.execute(insert_sql, ("zhaoliu", "111222", 28))
        # 提交事务（关键！否则数据不生效）
        conn.commit()
        print(f"新增成功，影响行数：{cursor.rowcount}")
    except Error as e:
        # 出错回滚
        conn.rollback()
        print(f"操作失败，已回滚：{e}")
    finally:
        close_conn(conn, cursor)

# 5. 批量插入（提升效率）
def batch_insert():
    conn = get_mysql_conn()
    if not conn:
        return
    try:
        cursor = conn.cursor()
        # 批量数据
        batch_data = [
            (f"user_{i}", f"pwd_{i}", 20 + i % 10)
            for i in range(100)
        ]
        sql = "INSERT INTO tb_user (username, password, age) VALUES (%s, %s, %s)"
        cursor.executemany(sql, batch_data)
        conn.commit()
        print(f"批量插入{cursor.rowcount}条数据")
    except Error as e:
        conn.rollback()
        print(f"批量插入失败：{e}")
    finally:
        close_conn(conn, cursor)

# 执行示例
if __name__ == "__main__":
    query_data()
    modify_data()
    batch_insert()
```

### 5.3 关键注意事项
1. **防`SQL`注入**：全程使用参数化查询（`%s`占位符），禁止字符串拼接`SQL`；
2. **事务控制**：增删改操作必须`commit()`，出错时`rollback()`；
3. **资源释放**：所有操作完成后关闭游标和连接；
4. **字符集**：连接时指定`charset='utf8mb4'`，避免中文乱码。

### 5.4 常见问题解决方案
| 问题                | 原因                          | 解决方案                                                                 |
|---------------------|-------------------------------|--------------------------------------------------------------------------|
| `Connection refused`  | 服务未启动/端口错误/防火墙拦截 | 1. 检查服务状态；2. 确认端口；3. 放行3306端口                            |
| `Access denied`       | 账号密码错误/权限不足         | 1. 核对账号密码；2. 授权：`GRANT ALL ON test_db.* TO 'test_user'@'%'`    |
| 中文乱码            | 字符集不匹配                  | 数据库/表/连接均设置为`utf8mb4`                                             |
| 数据修改不生效      | 未提交事务                    | 执行`conn.commit()`                                                       |

## 六、核心总结

### 6.1 `MySQL`基础核心
1. 数据库/表创建时务必指定`utf8mb4`字符集，避免中文乱码；
2. `SQL`查询优先使用`WHERE`过滤数据，`LIMIT`控制返回行数，提升效率；
3. 增删改操作必须加`WHERE`条件，防止误操作全表数据；
4. 用户权限遵循“最小权限原则”，避免过度授权。

### 6.2 `Python`操作`MySQL`核心
1. 核心流程：**建立连接→获取游标→参数化执行`SQL`→处理结果→提交/回滚→关闭连接**；
2. 防`SQL`注入是重中之重，必须使用`%s`占位符传递参数；
3. 批量操作（`executemany`）比单条循环执行效率提升显著；
4. 所有操作需捕获异常，确保连接/游标最终关闭。