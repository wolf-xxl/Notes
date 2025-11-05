# Python日志模块超详细笔记

## 1. 日志基础概念

### 1.1 什么是日志及其重要性
```python
"""
日志是记录程序运行状态、事件和错误信息的重要工具
在软件开发中，日志用于：
1. 调试程序：通过日志追踪程序执行流程和变量状态
2. 监控运行：实时了解程序运行状态和性能指标
3. 故障排查：当程序出现问题时，通过日志分析原因
4. 安全审计：记录用户操作和系统事件用于安全分析
5. 数据分析：收集用户行为和数据变化用于业务分析
"""

import logging

# 不使用日志的代码（难以调试）
def calculate_price(quantity, price):
    result = quantity * price
    # 如果结果不对，不知道是哪个值有问题
    return result

# 使用日志的代码（易于调试）
def calculate_price_with_log(quantity, price):
    logging.debug(f"开始计算价格: quantity={quantity}, price={price}")
    result = quantity * price
    logging.debug(f"计算结果: {result}")
    return result
```

### 1.2 Python logging模块的优势
```python
"""
Python的logging模块相比print语句的优势：
1. 分级管理：可以设置不同的日志级别（DEBUG, INFO, WARNING, ERROR, CRITICAL）
2. 多输出目的地：可以同时输出到控制台、文件、网络等
3. 灵活配置：可以通过代码或配置文件进行详细配置
4. 线程安全：在多线程环境中安全使用
5. 性能优化：可以控制日志输出频率和格式
"""

import logging

# 使用print的缺点
def process_data(data):
    print(f"开始处理数据: {data}")  # 总是输出，无法关闭
    # ...处理逻辑
    print("处理完成")  # 生产环境不需要这些输出

# 使用logging的优点
def process_data_with_log(data):
    logging.debug(f"开始处理数据: {data}")  # 可以根据级别控制输出
    # ...处理逻辑
    logging.info("处理完成")  # 生产环境可以只保留重要信息
```

## 2. 日志级别详解

### 2.1 六个标准日志级别及其使用场景
```python
import logging

# 查看各级别的数值
print(f"DEBUG级别数值: {logging.DEBUG}")      # 输出: DEBUG级别数值: 10
print(f"INFO级别数值: {logging.INFO}")       # 输出: INFO级别数值: 20  
print(f"WARNING级别数值: {logging.WARNING}") # 输出: WARNING级别数值: 30
print(f"ERROR级别数值: {logging.ERROR}")     # 输出: ERROR级别数值: 40
print(f"CRITICAL级别数值: {logging.CRITICAL}") # 输出: CRITICAL级别数值: 50

# 演示不同级别的使用场景
def demonstrate_log_levels():
    """
    各级别的典型使用场景：
    DEBUG: 详细的调试信息，开发阶段使用
    INFO: 程序正常运行的重要信息
    WARNING: 警告信息，程序仍能正常运行
    ERROR: 错误信息，程序某个功能失败
    CRITICAL: 严重错误，程序可能无法继续运行
    """
    
    # 设置日志级别为DEBUG，显示所有日志
    logging.basicConfig(level=logging.DEBUG, format='%(levelname)s: %(message)s')
    
    logging.debug("这是DEBUG信息 - 变量状态: x=10, y=20")  # 输出: DEBUG: 这是DEBUG信息 - 变量状态: x=10, y=20
    logging.info("这是INFO信息 - 用户登录成功")           # 输出: INFO: 这是INFO信息 - 用户登录成功
    logging.warning("这是WARNING信息 - 磁盘空间不足")     # 输出: WARNING: 这是WARNING信息 - 磁盘空间不足
    logging.error("这是ERROR信息 - 数据库连接失败")       # 输出: ERROR: 这是ERROR信息 - 数据库连接失败
    logging.critical("这是CRITICAL信息 - 系统崩溃")      # 输出: CRITICAL: 这是CRITICAL信息 - 系统崩溃

demonstrate_log_levels()

print("\n" + "="*50 + "\n")

# 演示日志级别过滤
def demonstrate_level_filtering():
    """演示如何通过设置级别来过滤日志"""
    
    # 设置级别为WARNING，只显示WARNING及以上的日志
    logging.basicConfig(level=logging.WARNING, format='%(levelname)s: %(message)s')
    
    print("设置日志级别为WARNING后的输出:")
    logging.debug("这条DEBUG不会显示")      # 无输出（级别低于WARNING）
    logging.info("这条INFO不会显示")        # 无输出（级别低于WARNING）
    logging.warning("这条WARNING会显示")    # 输出: WARNING: 这条WARNING会显示
    logging.error("这条ERROR会显示")        # 输出: ERROR: 这条ERROR会显示

demonstrate_level_filtering()
```

### 2.2 自定义日志级别
```python
import logging

# 创建自定义日志级别
CUSTOM_LEVEL_NUM = 25  # 介于INFO(20)和WARNING(30)之间

def add_custom_level():
    """添加自定义日志级别"""
    
    # 方法1：使用logging.addLevelName
    logging.addLevelName(CUSTOM_LEVEL_NUM, 'VERBOSE')
    
    # 方法2：创建自定义Logger类
    class CustomLogger(logging.getLoggerClass()):
        def verbose(self, msg, *args, **kwargs):
            if self.isEnabledFor(CUSTOM_LEVEL_NUM):
                self._log(CUSTOM_LEVEL_NUM, msg, args, **kwargs)
    
    # 设置自定义Logger类
    logging.setLoggerClass(CustomLogger)
    
    # 创建使用自定义级别的logger
    logger = logging.getLogger('custom_logger')
    logging.basicConfig(level=CUSTOM_LEVEL_NUM, format='%(levelname)s: %(message)s')
    
    # 使用自定义级别
    logger.log(CUSTOM_LEVEL_NUM, "这是自定义VERBOSE级别信息")  # 输出: VERBOSE: 这是自定义VERBOSE级别信息

add_custom_level()
```

## 3. 基础配置和使用

### 3.1 basicConfig方法详解
```python
import logging
import sys

def basic_config_demo():
    """
    logging.basicConfig()方法参数详解：
    - level: 设置日志级别
    - format: 设置日志格式
    - datefmt: 设置日期格式
    - filename: 输出到文件
    - filemode: 文件打开模式 ('w'覆盖, 'a'追加)
    - stream: 输出流（如sys.stdout, sys.stderr）
    - handlers: 直接指定处理器列表
    - encoding: 文件编码（Python 3.9+）
    - force: 强制重新配置（Python 3.8+）
    """
    
    # 完整的basicConfig配置
    logging.basicConfig(
        level=logging.DEBUG,  # 设置日志级别为DEBUG
        format='%(asctime)s - %(name)s - %(levelname)s - [%(filename)s:%(lineno)d] - %(message)s',  # 详细的格式
        datefmt='%Y-%m-%d %H:%M:%S',  # 日期时间格式
        filename='app.log',  # 日志输出到文件
        filemode='a',  # 追加模式（默认就是'a'）
        encoding='utf-8',  # 文件编码
        force=True  # 强制重新配置（如果之前已经配置过）
    )
    
    # 创建logger实例
    logger = logging.getLogger('my_app')
    
    # 记录不同级别的日志
    logger.debug("调试信息")  # 文件内容: 2024-01-01 10:00:00 - my_app - DEBUG - [example.py:25] - 调试信息
    logger.info("普通信息")   # 文件内容: 2024-01-01 10:00:00 - my_app - INFO - [example.py:26] - 普通信息
    logger.warning("警告信息") # 文件内容: 2024-01-01 10:00:00 - my_app - WARNING - [example.py:27] - 警告信息

basic_config_demo()

print("\n" + "="*50 + "\n")

def basic_config_common_mistakes():
    """演示basicConfig的常见错误用法"""
    
    print("常见错误1: 多次调用basicConfig")
    try:
        logging.basicConfig(level=logging.INFO)
        logging.basicConfig(level=logging.DEBUG)  # 这次调用不会生效！
        logger = logging.getLogger()
        logger.info("第一次basicConfig生效")  # 输出: INFO:root:第一次basicConfig生效
    except Exception as e:
        print(f"错误: {e}")
    
    print("\n常见错误2: basicConfig在getLogger之后")
    logger = logging.getLogger('late_config')
    logger.info("这条日志使用默认配置")  # 输出: INFO:late_config:这条日志使用默认配置
    
    # 此时调用basicConfig可能不会影响已创建的logger
    logging.basicConfig(level=logging.WARNING, format='%(message)s')
    logger.info("这条可能还会显示")  # 具体行为取决于Python版本和之前的配置

basic_config_common_mistakes()
```

### 3.2 同时输出到控制台和文件
```python
import logging
import sys

def multi_destination_logging():
    """配置日志同时输出到控制台和文件"""
    
    # 创建logger
    logger = logging.getLogger('multi_output')
    logger.setLevel(logging.DEBUG)  # 设置logger的级别
    
    # 避免重复添加handler（重要！）
    if logger.handlers:
        logger.handlers.clear()
    
    # 创建formatter（定义日志格式）
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)-8s - %(filename)s:%(lineno)d - %(message)s',
        datefmt='%H:%M:%S'
    )
    
    # 1. 创建文件处理器
    file_handler = logging.FileHandler('application.log', encoding='utf-8')
    file_handler.setLevel(logging.DEBUG)  # 文件记录所有级别的日志
    file_handler.setFormatter(formatter)
    
    # 2. 创建控制台处理器
    console_handler = logging.StreamHandler(sys.stdout)
    console_handler.setLevel(logging.INFO)  # 控制台只显示INFO及以上级别
    console_handler.setFormatter(formatter)
    
    # 3. 创建错误文件处理器（只记录ERROR和CRITICAL）
    error_handler = logging.FileHandler('errors.log', encoding='utf-8')
    error_handler.setLevel(logging.ERROR)  # 只记录错误日志
    error_handler.setFormatter(formatter)
    
    # 将所有处理器添加到logger
    logger.addHandler(file_handler)
    logger.addHandler(console_handler)
    logger.addHandler(error_handler)
    
    # 防止日志传播到根logger（避免重复输出）
    logger.propagate = False
    
    # 测试日志输出
    logger.debug("调试信息 - 只在文件中显示")  # 控制台: 无输出, 文件: 有输出
    logger.info("普通信息 - 在控制台和文件中都显示")  # 控制台: 有输出, 文件: 有输出
    logger.error("错误信息 - 在所有地方都显示")  # 控制台: 有输出, 文件: 有输出, 错误文件: 有输出
    
    return logger

# 使用多输出日志
logger = multi_destination_logging()
print("多目的地日志配置完成，请查看application.log和errors.log文件")
```

## 4. 日志记录器(Logger)详解

### 4.1 Logger的创建和命名
```python
import logging

def logger_creation_demo():
    """演示Logger的创建和命名最佳实践"""
    
    # 不推荐的创建方式
    bad_logger1 = logging.getLogger()  # 根logger，不推荐在应用代码中使用
    bad_logger2 = logging.getLogger('')  # 同样是根logger
    
    # 推荐的创建方式
    # 方式1: 使用模块名（最常用）
    module_logger = logging.getLogger(__name__)  # 如果当前模块是example，logger名称就是'example'
    
    # 方式2: 使用应用名称
    app_logger = logging.getLogger('my_application')
    
    # 方式3: 使用分层名称
    db_logger = logging.getLogger('my_application.database')
    api_logger = logging.getLogger('my_application.api')
    
    print(f"模块logger名称: {module_logger.name}")  # 输出: 模块logger名称: __main__
    print(f"应用logger名称: {app_logger.name}")     # 输出: 应用logger名称: my_application
    print(f"数据库logger名称: {db_logger.name}")    # 输出: 数据库logger名称: my_application.database
    
    # 演示logger层次结构
    print(f"\ndb_logger的父logger: {db_logger.parent}")  # 输出: db_logger的父logger: <Logger my_application (WARNING)>
    print(f"app_logger的父logger: {app_logger.parent}")  # 输出: app_logger的父logger: <RootLogger root (WARNING)>
    
    return module_logger, app_logger, db_logger

module_logger, app_logger, db_logger = logger_creation_demo()

print("\n" + "="*50 + "\n")

def logger_hierarchy_demo():
    """演示Logger的层次结构和继承"""
    
    # 配置父logger
    parent_logger = logging.getLogger('parent')
    parent_logger.setLevel(logging.INFO)
    
    # 创建控制台handler并添加到父logger
    console_handler = logging.StreamHandler()
    console_handler.setFormatter(logging.Formatter('%(name)s - %(message)s'))
    parent_logger.addHandler(console_handler)
    
    # 创建子logger
    child_logger = logging.getLogger('parent.child')
    
    print("子logger继承演示:")
    # 子logger没有单独配置，会使用父logger的配置
    child_logger.info("这条消息通过父logger的handler输出")  # 输出: parent.child - 这条消息通过父logger的handler输出
    
    # 设置子logger的级别
    child_logger.setLevel(logging.DEBUG)
    child_logger.debug("这条DEBUG消息不会显示，因为父logger级别是INFO")  # 无输出
    
    # 关闭传播
    child_logger.propagate = False
    child_logger.info("这条消息不会传播到父logger")  # 无输出（因为没有自己的handler）

logger_hierarchy_demo()
```

### 4.2 Logger的方法和属性
```python
import logging

def logger_methods_demo():
    """演示Logger的主要方法和属性"""
    
    # 创建并配置logger
    logger = logging.getLogger('methods_demo')
    logger.setLevel(logging.DEBUG)
    
    # 添加控制台handler
    handler = logging.StreamHandler()
    formatter = logging.Formatter('%(levelname)s: %(message)s')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    print("Logger方法和属性演示:")
    
    # 1. 基本日志记录方法
    logger.debug("debug消息")
    logger.info("info消息") 
    logger.warning("warning消息")
    logger.error("error消息")
    logger.critical("critical消息")
    
    # 2. 检查级别是否启用
    print(f"\nDEBUG级别是否启用: {logger.isEnabledFor(logging.DEBUG)}")  # 输出: DEBUG级别是否启用: True
    print(f"日志级别: {logger.level} ({logging.getLevelName(logger.level)})")  # 输出: 日志级别: 10 (DEBUG)
    
    # 3. 异常日志记录
    try:
        # 模拟一个异常
        result = 10 / 0
    except Exception as e:
        # 方式1: 使用exc_info参数
        logger.error("发生除零错误", exc_info=True)  # 会显示完整的堆栈跟踪
        
        # 方式2: 使用exception方法（相当于error+exc_info=True）
        logger.exception("这是使用exception方法记录的异常")  # 同样会显示完整堆栈跟踪
    
    # 4. 使用log方法指定级别
    logger.log(logging.INFO, "使用log方法记录INFO消息")  # 输出: INFO: 使用log方法记录INFO消息
    
    # 5. 处理器的管理
    print(f"\nLogger的处理器数量: {len(logger.handlers)}")  # 输出: Logger的处理器数量: 1
    for i, h in enumerate(logger.handlers):
        print(f"处理器 {i}: {type(h).__name__}")

logger_methods_demo()
```

## 5. 处理器(Handler)详解

### 5.1 常用Handler类型及使用
```python
import logging
import logging.handlers
import sys
import os

def handlers_demo():
    """演示各种常用的Handler类型"""
    
    # 创建测试用的logger
    logger = logging.getLogger('handlers_demo')
    logger.setLevel(logging.DEBUG)
    
    # 1. StreamHandler - 输出到流（控制台）
    stream_handler = logging.StreamHandler(sys.stdout)  # 输出到标准输出
    stream_handler.setLevel(logging.INFO)
    stream_handler.setFormatter(logging.Formatter('STREAM: %(message)s'))
    
    # 2. FileHandler - 输出到文件
    file_handler = logging.FileHandler('basic.log', encoding='utf-8')
    file_handler.setLevel(logging.DEBUG)
    file_handler.setFormatter(logging.Formatter('FILE: %(asctime)s - %(message)s'))
    
    # 3. RotatingFileHandler - 按文件大小轮转
    rotating_handler = logging.handlers.RotatingFileHandler(
        filename='rotating.log',      # 日志文件名
        maxBytes=1024,               # 每个日志文件最大1KB（测试用，实际应该更大）
        backupCount=3,               # 保留3个备份文件
        encoding='utf-8'
    )
    rotating_handler.setLevel(logging.DEBUG)
    rotating_handler.setFormatter(logging.Formatter('ROTATING: %(message)s'))
    
    # 4. TimedRotatingFileHandler - 按时间轮转
    timed_handler = logging.handlers.TimedRotatingFileHandler(
        filename='timed.log',        # 日志文件名
        when='S',                    # 按秒轮转（测试用，实际用'h'或'd'）
        interval=5,                  # 每5秒轮转一次
        backupCount=3,               # 保留3个备份文件
        encoding='utf-8'
    )
    timed_handler.setLevel(logging.DEBUG)
    timed_handler.setFormatter(logging.Formatter('TIMED: %(asctime)s - %(message)s'))
    
    # 5. NullHandler - 不输出任何内容（用于库代码）
    null_handler = logging.NullHandler()
    
    # 添加handler到logger（除了NullHandler）
    logger.addHandler(stream_handler)
    logger.addHandler(file_handler)
    logger.addHandler(rotating_handler)
    logger.addHandler(timed_handler)
    
    # 测试日志输出
    print("开始测试各种Handler...")
    for i in range(5):
        logger.info(f"测试消息 {i}")
    
    logger.debug("这条DEBUG消息只在文件中显示")
    logger.error("这条ERROR消息在所有handler中显示")
    
    # 显示生成的日志文件
    print(f"\n生成的日志文件:")
    for file in ['basic.log', 'rotating.log', 'timed.log']:
        if os.path.exists(file):
            print(f"  {file}: {os.path.getsize(file)} 字节")
    
    return logger

handlers_demo()

print("\n" + "="*50 + "\n")

def advanced_handlers_demo():
    """演示高级Handler的使用"""
    
    logger = logging.getLogger('advanced_handlers')
    
    # 6. SMTPHandler - 通过邮件发送错误日志
    try:
        smtp_handler = logging.handlers.SMTPHandler(
            mailhost=('smtp.example.com', 587),  # 邮件服务器和端口
            fromaddr='app@example.com',          # 发件人
            toaddrs=['admin@example.com'],       # 收件人列表
            subject='应用程序错误报告',           # 邮件主题
            credentials=('username', 'password'), # 登录凭据
            secure=()  # 使用TLS
        )
        smtp_handler.setLevel(logging.ERROR)  # 只发送ERROR及以上级别的日志
        # logger.addHandler(smtp_handler)  # 注释掉避免实际发送邮件
        print("SMTPHandler配置完成（未实际添加）")
    except Exception as e:
        print(f"SMTPHandler配置失败: {e}")
    
    # 7. HTTPHandler - 发送日志到HTTP服务器
    try:
        http_handler = logging.handlers.HTTPHandler(
            host='localhost:8000',  # 日志服务器地址
            url='/log',             # 日志接收URL
            method='POST'           # 请求方法
        )
        # logger.addHandler(http_handler)  # 注释掉避免实际发送请求
        print("HTTPHandler配置完成（未实际添加）")
    except Exception as e:
        print(f"HTTPHandler配置失败: {e}")
    
    # 8. MemoryHandler - 在内存中缓冲日志
    memory_handler = logging.handlers.MemoryHandler(
        capacity=100,  # 缓冲容量
        target=logging.StreamHandler(),  # 目标handler
        flushLevel=logging.ERROR  # 遇到ERROR级别时刷新缓冲
    )
    memory_handler.setFormatter(logging.Formatter('MEMORY: %(message)s'))
    logger.addHandler(memory_handler)
    
    # 测试MemoryHandler
    logger.info("这条消息在内存缓冲中")  # 不会立即输出
    logger.error("这条错误消息会触发刷新")  # 会输出所有缓冲的消息
    
    return logger

advanced_handlers_demo()
```

### 5.2 自定义Handler
```python
import logging
import requests
import json

class SlackHandler(logging.Handler):
    """自定义Handler，将日志发送到Slack"""
    
    def __init__(self, webhook_url, channel=None, username=None):
        """
        初始化Slack Handler
        
        Args:
            webhook_url: Slack Webhook URL
            channel: 发送到的Slack频道（可选）
            username: 发送者名称（可选）
        """
        super().__init__()
        self.webhook_url = webhook_url
        self.channel = channel
        self.username = username
    
    def emit(self, record):
        """发送日志记录到Slack"""
        try:
            # 格式化日志消息
            log_entry = self.format(record)
            
            # 构建Slack消息负载
            payload = {
                'text': log_entry,
                'attachments': [
                    {
                        'color': self._get_color(record.levelno),
                        'fields': [
                            {
                                'title': 'Level',
                                'value': record.levelname,
                                'short': True
                            },
                            {
                                'title': 'Logger',
                                'value': record.name,
                                'short': True
                            }
                        ]
                    }
                ]
            }
            
            # 添加可选字段
            if self.channel:
                payload['channel'] = self.channel
            if self.username:
                payload['username'] = self.username
            
            # 发送请求到Slack
            response = requests.post(
                self.webhook_url,
                data=json.dumps(payload),
                headers={'Content-Type': 'application/json'}
            )
            
            # 检查响应
            if response.status_code != 200:
                print(f"Slack发送失败: {response.status_code} - {response.text}")
                
        except Exception as e:
            # 避免在Handler中抛出异常导致循环
            print(f"SlackHandler错误: {e}")
    
    def _get_color(self, level):
        """根据日志级别返回颜色"""
        colors = {
            logging.DEBUG: '#36a64f',    # 绿色
            logging.INFO: '#2eb886',     # 浅绿色
            logging.WARNING: '#ffff00',  # 黄色
            logging.ERROR: '#ff0000',    # 红色
            logging.CRITICAL: '#8b0000'  # 深红色
        }
        return colors.get(level, '#cccccc')

class DatabaseHandler(logging.Handler):
    """自定义Handler，将日志保存到数据库"""
    
    def __init__(self, db_connection, table_name='logs'):
        """
        初始化数据库Handler
        
        Args:
            db_connection: 数据库连接对象
            table_name: 日志表名
        """
        super().__init__()
        self.db_connection = db_connection
        self.table_name = table_name
    
    def emit(self, record):
        """保存日志记录到数据库"""
        try:
            # 获取格式化的日志信息
            log_entry = self.format(record)
            
            # 构建SQL插入语句（这里使用SQLite示例）
            sql = f"""
            INSERT INTO {self.table_name} 
            (timestamp, level, logger, message, filename, lineno, funcName)
            VALUES (?, ?, ?, ?, ?, ?, ?)
            """
            
            # 执行插入
            cursor = self.db_connection.cursor()
            cursor.execute(sql, (
                record.created,
                record.levelname,
                record.name,
                log_entry,
                record.filename,
                record.lineno,
                record.funcName
            ))
            self.db_connection.commit()
            
        except Exception as e:
            print(f"DatabaseHandler错误: {e}")

def custom_handlers_demo():
    """演示自定义Handler的使用"""
    
    logger = logging.getLogger('custom_handlers')
    logger.setLevel(logging.INFO)
    
    # 使用SlackHandler（需要实际的webhook URL）
    try:
        # slack_handler = SlackHandler(
        #     webhook_url='https://hooks.slack.com/services/xxx',
        #     channel='#alerts',
        #     username='LoggerBot'
        # )
        # slack_handler.setLevel(logging.ERROR)  # 只发送错误日志
        # logger.addHandler(slack_handler)
        print("SlackHandler配置完成（需要真实webhook URL才能使用）")
    except Exception as e:
        print(f"SlackHandler配置失败: {e}")
    
    # 使用DatabaseHandler（需要实际的数据库连接）
    try:
        # import sqlite3
        # conn = sqlite3.connect('app.db')
        # db_handler = DatabaseHandler(conn)
        # logger.addHandler(db_handler)
        print("DatabaseHandler配置完成（需要真实数据库连接才能使用）")
    except Exception as e:
        print(f"DatabaseHandler配置失败: {e}")
    
    # 添加一个简单的控制台handler用于演示
    console_handler = logging.StreamHandler()
    console_handler.setFormatter(logging.Formatter('CUSTOM: %(levelname)s - %(message)s'))
    logger.addHandler(console_handler)
    
    # 测试日志
    logger.info("这条消息会显示在控制台")
    logger.error("这条错误消息会触发所有handler")
    
    return logger

custom_handlers_demo()
```

## 6. 格式化(Formatter)详解

### 6.1 标准格式化属性
```python
import logging

def formatter_attributes_demo():
    """演示所有可用的格式化属性"""
    
    # 创建logger
    logger = logging.getLogger('formatter_demo')
    logger.setLevel(logging.DEBUG)
    
    # 创建handler
    handler = logging.StreamHandler()
    
    # 使用包含所有常用属性的格式化字符串
    detailed_format = """
=== 日志详细信息 ===
时间: %(asctime)s
级别: %(levelname)s
Logger名称: %(name)s
文件: %(filename)s
行号: %(lineno)d
函数: %(funcName)s
模块: %(module)s
进程ID: %(process)d
进程名称: %(processName)s
线程ID: %(thread)d
线程名称: %(threadName)s
消息: %(message)s
=================
"""
    
    formatter = logging.Formatter(detailed_format, datefmt='%Y-%m-%d %H:%M:%S')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    # 定义一个测试函数来产生日志
    def test_function():
        logger.info("这是在test_function中产生的日志消息")
    
    # 记录日志
    test_function()

formatter_attributes_demo()

print("\n" + "="*50 + "\n")

def practical_formatter_demo():
    """演示实际项目中常用的格式化方式"""
    
    logger = logging.getLogger('practical_format')
    logger.setLevel(logging.DEBUG)
    
    handler = logging.StreamHandler()
    
    # 实际项目中的常用格式
    practical_format = '%(asctime)s | %(levelname)-8s | %(name)-15s | %(filename)s:%(lineno)d | %(funcName)s() - %(message)s'
    
    formatter = logging.Formatter(practical_format, datefmt='%H:%M:%S')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    # 测试各种级别的日志
    logger.debug("调试信息")
    logger.info("普通信息")
    logger.warning("警告信息")
    logger.error("错误信息")
    
    # 输出示例:
    # 10:00:00 | DEBUG    | practical_format | example.py:25 | <module>() - 调试信息
    # 10:00:00 | INFO     | practical_format | example.py:26 | <module>() - 普通信息
    # 10:00:00 | WARNING  | practical_format | example.py:27 | <module>() - 警告信息
    # 10:00:00 | ERROR    | practical_format | example.py:28 | <module>() - 错误信息

practical_formatter_demo()
```

### 6.2 自定义Formatter
```python
import logging
import time

class ColorFormatter(logging.Formatter):
    """带颜色的控制台日志格式化器"""
    
    # 颜色代码
    COLORS = {
        'DEBUG': '\033[36m',      # 青色
        'INFO': '\033[32m',       # 绿色
        'WARNING': '\033[33m',    # 黄色
        'ERROR': '\033[31m',      # 红色
        'CRITICAL': '\033[41m',   # 红色背景
        'RESET': '\033[0m'        # 重置颜色
    }
    
    def format(self, record):
        """格式化日志记录并添加颜色"""
        # 调用父类方法格式化日志
        log_message = super().format(record)
        
        # 添加颜色
        if record.levelname in self.COLORS:
            color = self.COLORS[record.levelname]
            reset = self.COLORS['RESET']
            log_message = f"{color}{log_message}{reset}"
        
        return log_message

class JsonFormatter(logging.Formatter):
    """JSON格式的日志格式化器"""
    
    def format(self, record):
        """将日志记录格式化为JSON"""
        import json
        
        log_data = {
            'timestamp': self.formatTime(record),
            'level': record.levelname,
            'logger': record.name,
            'message': record.getMessage(),
            'module': record.module,
            'function': record.funcName,
            'line': record.lineno,
            'thread': record.threadName,
            'process': record.processName
        }
        
        # 如果有异常信息，添加到JSON
        if record.exc_info:
            log_data['exception'] = self.formatException(record.exc_info)
        
        # 添加额外字段
        if hasattr(record, 'user_id'):
            log_data['user_id'] = record.user_id
        if hasattr(record, 'request_id'):
            log_data['request_id'] = record.request_id
        
        return json.dumps(log_data, ensure_ascii=False)

class PerformanceFormatter(logging.Formatter):
    """性能监控专用的格式化器"""
    
    def __init__(self):
        self.start_time = time.time()
        super().__init__()
    
    def format(self, record):
        """添加时间戳和运行时间信息"""
        current_time = time.time()
        running_time = current_time - self.start_time
        
        # 添加性能相关字段
        record.running_time = f"{running_time:.3f}s"
        record.current_time = time.strftime('%H:%M:%S')
        
        format_str = '%(current_time)s [%(running_time)s] %(levelname)s - %(name)s: %(message)s'
        formatter = logging.Formatter(format_str)
        return formatter.format(record)

def custom_formatters_demo():
    """演示自定义Formatter的使用"""
    
    # 测试ColorFormatter
    print("1. 彩色日志演示:")
    color_logger = logging.getLogger('color_demo')
    color_handler = logging.StreamHandler()
    color_handler.setFormatter(ColorFormatter('%(levelname)s - %(message)s'))
    color_logger.addHandler(color_handler)
    color_logger.setLevel(logging.DEBUG)
    
    color_logger.debug("彩色调试信息")
    color_logger.info("彩色普通信息")
    color_logger.warning("彩色警告信息")
    color_logger.error("彩色错误信息")
    
    print("\n2. JSON格式日志演示:")
    json_logger = logging.getLogger('json_demo')
    json_handler = logging.StreamHandler()
    json_handler.setFormatter(JsonFormatter())
    json_logger.addHandler(json_handler)
    json_logger.setLevel(logging.INFO)
    
    # 添加额外字段
    import sys
    sys.path.append('.')  # 确保可以导入当前目录的模块
    extra_info = {'user_id': '12345', 'request_id': 'req-67890'}
    json_logger.info("用户操作日志", extra=extra_info)
    
    print("\n3. 性能监控日志演示:")
    perf_logger = logging.getLogger('performance_demo')
    perf_handler = logging.StreamHandler()
    perf_handler.setFormatter(PerformanceFormatter())
    perf_logger.addHandler(perf_handler)
    perf_logger.setLevel(logging.INFO)
    
    perf_logger.info("程序启动")
    time.sleep(0.1)  # 模拟一些处理时间
    perf_logger.info("处理完成第一阶段")
    time.sleep(0.2)
    perf_logger.info("处理完成第二阶段")

custom_formatters_demo()
```

## 7. 过滤器(Filter)详解

### 7.1 使用过滤器控制日志输出
```python
import logging
import re

class LevelRangeFilter(logging.Filter):
    """只允许特定级别范围内的日志通过"""
    
    def __init__(self, min_level, max_level):
        """
        初始化级别范围过滤器
        
        Args:
            min_level: 最小级别（包括）
            max_level: 最大级别（包括）
        """
        super().__init__()
        self.min_level = min_level
        self.max_level = max_level
    
    def filter(self, record):
        """过滤日志记录"""
        return self.min_level <= record.levelno <= self.max_level

class KeywordFilter(logging.Filter):
    """基于关键词过滤日志"""
    
    def __init__(self, include_keywords=None, exclude_keywords=None):
        """
        初始化关键词过滤器
        
        Args:
            include_keywords: 包含的关键词列表（只要包含任意一个就通过）
            exclude_keywords: 排除的关键词列表（包含任意一个就过滤掉）
        """
        super().__init__()
        self.include_keywords = include_keywords or []
        self.exclude_keywords = exclude_keywords or []
    
    def filter(self, record):
        """基于关键词过滤日志记录"""
        message = record.getMessage()
        
        # 如果设置了包含关键词，检查是否包含任意一个
        if self.include_keywords:
            if not any(keyword in message for keyword in self.include_keywords):
                return False
        
        # 如果设置了排除关键词，检查是否包含任意一个
        if self.exclude_keywords:
            if any(keyword in message for keyword in self.exclude_keywords):
                return False
        
        return True

class RegexFilter(logging.Filter):
    """使用正则表达式过滤日志"""
    
    def __init__(self, pattern, include=True):
        """
        初始化正则表达式过滤器
        
        Args:
            pattern: 正则表达式模式
            include: True表示匹配时包含，False表示匹配时排除
        """
        super().__init__()
        self.pattern = re.compile(pattern)
        self.include = include
    
    def filter(self, record):
        """使用正则表达式过滤日志记录"""
        message = record.getMessage()
        matches = bool(self.pattern.search(message))
        
        if self.include:
            return matches  # 包含匹配的消息
        else:
            return not matches  # 排除匹配的消息

class RateLimitFilter(logging.Filter):
    """限制相同消息的日志频率"""
    
    def __init__(self, max_per_minute=60):
        """
        初始化频率限制过滤器
        
        Args:
            max_per_minute: 每分钟最大允许次数
        """
        super().__init__()
        self.max_per_minute = max_per_minute
        self.message_count = {}
        self.last_cleanup = time.time()
    
    def filter(self, record):
        """限制日志频率"""
        current_time = time.time()
        message = record.getMessage()
        
        # 每分钟清理一次计数器
        if current_time - self.last_cleanup > 60:
            self.message_count.clear()
            self.last_cleanup = current_time
        
        # 检查消息频率
        count = self.message_count.get(message, 0)
        if count >= self.max_per_minute:
            return False
        
        # 更新计数器
        self.message_count[message] = count + 1
        return True

def filters_demo():
    """演示各种过滤器的使用"""
    
    # 创建基础logger
    logger = logging.getLogger('filters_demo')
    logger.setLevel(logging.DEBUG)
    
    # 1. 级别范围过滤器演示
    print("1. 级别范围过滤器:")
    level_logger = logging.getLogger('level_filter')
    level_handler = logging.StreamHandler()
    level_handler.setFormatter(logging.Formatter('LEVEL: %(levelname)s - %(message)s'))
    
    # 只允许INFO和WARNING级别的日志
    level_filter = LevelRangeFilter(logging.INFO, logging.WARNING)
    level_handler.addFilter(level_filter)
    level_logger.addHandler(level_handler)
    
    level_logger.debug("这条DEBUG不会显示")    # 无输出
    level_logger.info("这条INFO会显示")        # 输出: LEVEL: INFO - 这条INFO会显示
    level_logger.warning("这条WARNING会显示")  # 输出: LEVEL: WARNING - 这条WARNING会显示
    level_logger.error("这条ERROR不会显示")     # 无输出
    
    # 2. 关键词过滤器演示
    print("\n2. 关键词过滤器:")
    keyword_logger = logging.getLogger('keyword_filter')
    keyword_handler = logging.StreamHandler()
    keyword_handler.setFormatter(logging.Formatter('KEYWORD: %(message)s'))
    
    # 只包含"用户"或"订单"关键词，排除"密码"关键词
    keyword_filter = KeywordFilter(
        include_keywords=['用户', '订单'],
        exclude_keywords=['密码', 'token']
    )
    keyword_handler.addFilter(keyword_filter)
    keyword_logger.addHandler(keyword_handler)
    
    keyword_logger.info("用户登录成功")           # 输出: KEYWORD: 用户登录成功
    keyword_logger.info("订单创建完成")           # 输出: KEYWORD: 订单创建完成
    keyword_logger.info("密码重置请求")           # 无输出（包含排除关键词）
    keyword_logger.info("系统启动完成")           # 无输出（不包含包含关键词）
    
    # 3. 正则表达式过滤器演示
    print("\n3. 正则表达式过滤器:")
    regex_logger = logging.getLogger('regex_filter')
    regex_handler = logging.StreamHandler()
    regex_handler.setFormatter(logging.Formatter('REGEX: %(message)s'))
    
    # 只包含包含IP地址的日志
    ip_pattern = r'\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}'
    regex_filter = RegexFilter(ip_pattern, include=True)
    regex_handler.addFilter(regex_filter)
    regex_logger.addHandler(regex_handler)
    
    regex_logger.info("用户从192.168.1.1登录")  # 输出: REGEX: 用户从192.168.1.1登录
    regex_logger.info("系统正常运行")            # 无输出（不匹配IP地址模式）
    
    # 4. 频率限制过滤器演示
    print("\n4. 频率限制过滤器:")
    rate_logger = logging.getLogger('rate_filter')
    rate_handler = logging.StreamHandler()
    rate_handler.setFormatter(logging.Formatter('RATE: %(message)s'))
    
    # 限制相同消息每分钟最多输出2次
    rate_filter = RateLimitFilter(max_per_minute=2)
    rate_handler.addFilter(rate_filter)
    rate_logger.addHandler(rate_handler)
    
    for i in range(5):
        rate_logger.info("重复的消息")  # 只有前2次会输出
    
    rate_logger.info("不同的消息")      # 会输出（因为是不同的消息）

filters_demo()
```

## 8. 配置文件方式配置日志

### 8.1 使用字典配置（推荐方式）
```python
import logging.config

def dict_config_demo():
    """使用字典配置日志系统"""
    
    # 详细的日志配置字典
    LOGGING_CONFIG = {
        # 配置版本，必须为1
        'version': 1,
        
        # 是否禁用现有的logger，False表示保留
        'disable_existing_loggers': False,
        
        # 定义格式化器
        'formatters': {
            # 简单格式
            'simple': {
                'format': '%(asctime)s - %(name)s - %(levelname)s - %(message)s',
                'datefmt': '%H:%M:%S'
            },
            # 详细格式
            'detailed': {
                'format': '%(asctime)s | %(levelname)-8s | %(name)-15s | %(filename)s:%(lineno)d | %(funcName)s() - %(message)s',
                'datefmt': '%Y-%m-%d %H:%M:%S'
            },
            # JSON格式
            'json': {
                '()': '__main__.JsonFormatter'  # 使用自定义的JSON格式化器
            }
        },
        
        # 定义过滤器
        'filters': {
            'info_only': {
                '()': '__main__.LevelRangeFilter',  # 使用自定义过滤器
                'min_level': logging.INFO,
                'max_level': logging.INFO
            }
        },
        
        # 定义处理器
        'handlers': {
            # 控制台处理器
            'console': {
                'class': 'logging.StreamHandler',      # 处理器类
                'level': 'INFO',                      # 处理器级别
                'formatter': 'simple',                # 使用的格式化器
                'stream': 'ext://sys.stdout'          # 输出流
            },
            
            # 文件处理器
            'file': {
                'class': 'logging.FileHandler',       # 处理器类
                'level': 'DEBUG',                     # 处理器级别
                'formatter': 'detailed',              # 使用的格式化器
                'filename': 'app.log',                # 日志文件名
                'mode': 'a',                          # 文件模式
                'encoding': 'utf-8'                   # 文件编码
            },
            
            # 轮转文件处理器
            'rotating_file': {
                'class': 'logging.handlers.RotatingFileHandler',
                'level': 'INFO',
                'formatter': 'detailed',
                'filename': 'rotating_app.log',
                'maxBytes': 10485760,  # 10MB
                'backupCount': 5,
                'encoding': 'utf-8'
            },
            
            # 错误文件处理器（只记录错误）
            'error_file': {
                'class': 'logging.FileHandler',
                'level': 'ERROR',
                'formatter': 'detailed',
                'filename': 'errors.log',
                'mode': 'a',
                'encoding': 'utf-8'
            },
            
            # 仅INFO级别处理器
            'info_file': {
                'class': 'logging.FileHandler',
                'level': 'INFO',
                'formatter': 'simple',
                'filename': 'info.log',
                'filters': ['info_only'],  # 使用过滤器
                'encoding': 'utf-8'
            }
        },
        
        # 定义logger
        'loggers': {
            # 根logger配置
            '': {  # 空字符串表示根logger
                'level': 'DEBUG',           # logger级别
                'handlers': ['console'],    # 使用的处理器
                'propagate': False          # 是否传播到父logger
            },
            
            # 应用主logger
            'myapp': {
                'level': 'DEBUG',
                'handlers': ['file', 'console', 'error_file'],
                'propagate': False
            },
            
            # 数据库相关logger
            'myapp.database': {
                'level': 'INFO',
                'handlers': ['file', 'rotating_file'],
                'propagate': False
            },
            
            # API相关logger
            'myapp.api': {
                'level': 'DEBUG',
                'handlers': ['file', 'console', 'info_file'],
                'propagate': False
            },
            
            # 第三方库的logger（降低级别避免过多日志）
            'requests': {
                'level': 'WARNING',
                'handlers': ['file'],
                'propagate': False
            },
            
            'urllib3': {
                'level': 'WARNING',
                'handlers': ['file'],
                'propagate': False
            }
        }
    }
    
    # 应用配置
    logging.config.dictConfig(LOGGING_CONFIG)
    
    # 测试配置的logger
    app_logger = logging.getLogger('myapp')
    db_logger = logging.getLogger('myapp.database')
    api_logger = logging.getLogger('myapp.api')
    
    print("字典配置测试:")
    app_logger.debug("应用调试信息")
    app_logger.info("应用普通信息")
    app_logger.error("应用错误信息")
    
    db_logger.debug("数据库调试信息（不会显示，因为级别是INFO）")
    db_logger.info("数据库普通信息")
    
    api_logger.info("API信息")
    api_logger.debug("API调试信息")
    
    # 测试第三方库logger
    requests_logger = logging.getLogger('requests')
    requests_logger.debug("requests调试信息（不会显示）")
    requests_logger.warning("requests警告信息")

dict_config_demo()
```

### 8.2 使用配置文件
```python
import logging.config
import os

def file_config_demo():
    """使用配置文件配置日志"""
    
    # 创建配置文件内容
    config_content = """
[loggers]
keys=root,myapp,myapp_database

[handlers]
keys=consoleHandler,fileHandler,errorHandler

[formatters]
keys=simpleFormatter,detailedFormatter

[logger_root]
level=DEBUG
handlers=consoleHandler

[logger_myapp]
level=DEBUG
handlers=fileHandler,consoleHandler
qualname=myapp
propagate=0

[logger_myapp_database]
level=INFO
handlers=fileHandler,errorHandler
qualname=myapp.database
propagate=0

[handler_consoleHandler]
class=StreamHandler
level=INFO
formatter=simpleFormatter
args=(sys.stdout,)

[handler_fileHandler]
class=FileHandler
level=DEBUG
formatter=detailedFormatter
args=('app_from_config.log', 'a', 'utf-8')

[handler_errorHandler]
class=FileHandler
level=ERROR
formatter=detailedFormatter
args=('errors_from_config.log', 'a', 'utf-8')

[formatter_simpleFormatter]
format=%(asctime)s - %(name)s - %(levelname)s - %(message)s
datefmt=%H:%M:%S

[formatter_detailedFormatter]
format=%(asctime)s | %(levelname)-8s | %(name)-15s | %(filename)s:%(lineno)d - %(message)s
datefmt=%Y-%m-%d %H:%M:%S
"""
    
    # 写入配置文件
    config_file = 'logging.conf'
    with open(config_file, 'w', encoding='utf-8') as f:
        f.write(config_content)
    
    try:
        # 使用配置文件
        logging.config.fileConfig(
            config_file,
            disable_existing_loggers=False  # 不禁用现有的logger
        )
        
        # 测试配置的logger
        app_logger = logging.getLogger('myapp')
        db_logger = logging.getLogger('myapp.database')
        
        print("配置文件测试:")
        app_logger.info("来自配置文件的APP日志")
        db_logger.info("来自配置文件的DB日志")
        db_logger.error("来自配置文件的DB错误")
        
    finally:
        # 清理配置文件
        if os.path.exists(config_file):
            os.remove(config_file)

file_config_demo()
```

## 9. 高级技巧和最佳实践

### 9.1 异常处理和堆栈跟踪
```python
import logging
import traceback

def exception_handling_demo():
    """演示如何在日志中正确处理异常"""
    
    logger = logging.getLogger('exception_demo')
    logger.setLevel(logging.DEBUG)
    
    handler = logging.StreamHandler()
    handler.setFormatter(logging.Formatter('%(levelname)s: %(message)s'))
    logger.addHandler(handler)
    
    def risky_operation(x, y):
        """可能抛出异常的操作"""
        return x / y
    
    print("异常处理演示:")
    
    # 不好的异常处理方式
    try:
        result = risky_operation(10, 0)
    except Exception as e:
        logger.error(f"操作失败: {e}")  # 只记录错误消息，没有堆栈跟踪
        # 输出: ERROR: 操作失败: division by zero
    
    # 好的异常处理方式1：使用exc_info
    try:
        result = risky_operation(10, 0)
    except Exception as e:
        logger.error("操作失败", exc_info=True)  # 记录完整的异常信息
        # 输出: ERROR: 操作失败
        # Traceback (most recent call last):
        #   File "example.py", line X, in exception_handling_demo
        #     result = risky_operation(10, 0)
        #   File "example.py", line X, in risky_operation
        #     return x / y
        # ZeroDivisionError: division by zero
    
    # 好的异常处理方式2：使用exception方法
    try:
        result = risky_operation(10, 0)
    except Exception as e:
        logger.exception("操作失败（使用exception方法）")  # 自动包含异常信息
        # 输出与上面类似，包含完整的堆栈跟踪
    
    # 好的异常处理方式3：手动获取堆栈跟踪
    try:
        result = risky_operation(10, 0)
    except Exception as e:
        stack_trace = traceback.format_exc()
        logger.error(f"操作失败，堆栈跟踪:\n{stack_trace}")
        # 输出包含完整堆栈跟踪的错误消息
    
    # 在函数中记录异常的最佳实践
    def process_data(data):
        try:
            # 模拟数据处理
            if not data:
                raise ValueError("数据不能为空")
            return f"处理结果: {data}"
        except Exception as e:
            logger.error(
                "数据处理失败",
                exc_info=True,
                extra={'input_data': str(data)[:100]}  # 添加上下文信息
            )
            return None
    
    process_data("")  # 触发异常

exception_handling_demo()

print("\n" + "="*50 + "\n")

def performance_optimization_demo():
    """演示日志性能优化技巧"""
    
    logger = logging.getLogger('performance_demo')
    logger.setLevel(logging.INFO)
    
    handler = logging.StreamHandler()
    handler.setFormatter(logging.Formatter('%(message)s'))
    logger.addHandler(handler)
    
    print("性能优化演示:")
    
    # 昂贵的字符串操作
    def expensive_operation():
        """模拟一个昂贵的操作"""
        import time
        time.sleep(0.01)  # 模拟耗时操作
        return "昂贵的结果"
    
    # 不好的做法：总是执行昂贵操作
    # logger.debug(f"结果: {expensive_operation()}")  # 即使DEBUG级别关闭也会执行
    
    # 好的做法1：先检查级别
    if logger.isEnabledFor(logging.DEBUG):
        logger.debug(f"结果: {expensive_operation()}")
    
    # 好的做法2：使用参数化日志（推荐）
    logger.debug("结果: %s", expensive_operation())  # 只有在级别启用时才会调用函数
    
    # 好的做法3：使用lazy evaluation
    class LazyMessage:
        def __init__(self, func, *args, **kwargs):
            self.func = func
            self.args = args
            self.kwargs = kwargs
        
        def __str__(self):
            return self.func(*self.args, **self.kwargs)
    
    logger.debug("结果: %s", LazyMessage(expensive_operation))
    
    # 测量不同方法的性能
    import time
    
    # 方法1：字符串拼接（最慢）
    start = time.time()
    for i in range(100):
        if logger.isEnabledFor(logging.DEBUG):
            logger.debug("消息 " + str(i) + " " + expensive_operation())
    time1 = time.time() - start
    
    # 方法2：格式化字符串（中等）
    start = time.time()
    for i in range(100):
        if logger.isEnabledFor(logging.DEBUG):
            logger.debug("消息 %d %s", i, expensive_operation())
    time2 = time.time() - start
    
    # 方法3：lazy evaluation（最快）
    start = time.time()
    for i in range(100):
        logger.debug("消息 %d %s", i, LazyMessage(expensive_operation))
    time3 = time.time() - start
    
    print(f"\n性能比较（DEBUG级别关闭时）:")
    print(f"字符串拼接: {time1:.3f}秒")
    print(f"参数化日志: {time2:.3f}秒") 
    print(f"Lazy Evaluation: {time3:.3f}秒")

performance_optimization_demo()
```

### 9.2 结构化日志和上下文信息
```python
import logging
import json

def structured_logging_demo():
    """演示结构化日志记录"""
    
    # 配置结构化日志
    logger = logging.getLogger('structured')
    logger.setLevel(logging.INFO)
    
    handler = logging.StreamHandler()
    handler.setFormatter(JsonFormatter())  # 使用之前定义的JSON格式化器
    logger.addHandler(handler)
    
    print("结构化日志演示:")
    
    # 方式1：使用extra参数添加上下文
    def user_login(user_id, ip_address):
        logger.info(
            "用户登录成功",
            extra={
                'user_id': user_id,
                'ip_address': ip_address,
                'action': 'login',
                'status': 'success'
            }
        )
    
    # 方式2：创建自定义LoggerAdapter
    class ContextLogger:
        def __init__(self, logger, **context):
            self.logger = logger
            self.context = context
        
        def _add_context(self, kwargs):
            extra = kwargs.get('extra', {})
            extra.update(self.context)
            kwargs['extra'] = extra
            return kwargs
        
        def info(self, msg, *args, **kwargs):
            kwargs = self._add_context(kwargs)
            self.logger.info(msg, *args, **kwargs)
        
        # 同样为其他级别定义方法
        def debug(self, msg, *args, **kwargs):
            kwargs = self._add_context(kwargs)
            self.logger.debug(msg, *args, **kwargs)
        
        def error(self, msg, *args, **kwargs):
            kwargs = self._add_context(kwargs)
            self.logger.error(msg, *args, **kwargs)
    
    # 方式3：使用logging.LoggerAdapter
    def adapter_factory(extra):
        return logging.LoggerAdapter(logging.getLogger('adapter_demo'), extra)
    
    # 测试结构化日志
    user_login('user123', '192.168.1.100')
    
    # 使用ContextLogger
    request_logger = ContextLogger(
        logging.getLogger('request_tracking'),
        request_id='req-12345',
        session_id='sess-67890'
    )
    request_logger.info("处理API请求", extra={'endpoint': '/api/users', 'method': 'GET'})
    
    # 使用LoggerAdapter
    adapter = adapter_factory({'service': 'auth-service', 'version': '1.0.0'})
    adapter.info("服务启动完成")

structured_logging_demo()

print("\n" + "="*50 + "\n")

def context_management_demo():
    """演示上下文管理在日志中的应用"""
    
    import contextlib
    
    logger = logging.getLogger('context_demo')
    logger.setLevel(logging.INFO)
    
    handler = logging.StreamHandler()
    handler.setFormatter(logging.Formatter('%(message)s'))
    logger.addHandler(handler)
    
    @contextlib.contextmanager
    def log_execution_time(operation_name):
        """记录操作执行时间的上下文管理器"""
        start_time = time.time()
        logger.info("开始执行: %s", operation_name)
        
        try:
            yield
        except Exception as e:
            logger.error("执行失败: %s - %s", operation_name, e)
            raise
        finally:
            end_time = time.time()
            duration = end_time - start_time
            logger.info("执行完成: %s - 耗时: %.3f秒", operation_name, duration)
    
    @contextlib.contextmanager  
    def log_exceptions(context_info=None):
        """捕获并记录异常的上下文管理器"""
        context = context_info or {}
        try:
            yield
        except Exception as e:
            logger.error(
                "操作执行异常",
                exc_info=True,
                extra=context
            )
            raise
    
    print("上下文管理演示:")
    
    # 使用执行时间日志
    with log_execution_time("数据处理操作"):
        time.sleep(0.1)  # 模拟耗时操作
        print("正在处理数据...")
    
    # 使用异常日志
    with log_exceptions({'operation': '文件读取', 'file_path': '/path/to/file'}):
        # 模拟可能失败的操作
        raise ValueError("文件不存在")

context_management_demo()
```

## 10. 常见错误和解决方案

### 10.1 配置相关错误
```python
import logging

def common_configuration_errors():
    """演示常见的配置错误及其解决方案"""
    
    print("常见配置错误演示:")
    
    # 错误1: 多次调用basicConfig
    print("\n1. 多次调用basicConfig:")
    try:
        logging.basicConfig(level=logging.INFO)
        logging.basicConfig(level=logging.DEBUG)  # 这个调用不会生效！
        logger = logging.getLogger('multiple_basicconfig')
        logger.info("这条日志使用INFO级别配置")  # 输出: INFO:multiple_basicconfig:这条日志使用INFO级别配置
        print("第二次basicConfig调用被忽略")
    except Exception as e:
        print(f"错误: {e}")
    
    # 错误2: handler重复添加
    print("\n2. Handler重复添加:")
    def wrong_handler_setup():
        logger = logging.getLogger('duplicate_handlers')
        # 每次调用都会添加新的handler（错误！）
        handler = logging.StreamHandler()
        logger.addHandler(handler)
        return logger
    
    def correct_handler_setup():
        logger = logging.getLogger('correct_handlers')
        # 检查是否已经配置过
        if not logger.handlers:
            handler = logging.StreamHandler()
            logger.addHandler(handler)
        return logger
    
    wrong_logger = wrong_handler_setup()
    wrong_logger = wrong_handler_setup()  # 再次调用，重复添加handler
    print(f"错误方式handler数量: {len(wrong_logger.handlers)}")  # 输出: 错误方式handler数量: 2
    
    correct_logger = correct_handler_setup()
    correct_logger = correct_handler_setup()  # 再次调用，不会重复添加
    print(f"正确方式handler数量: {len(correct_logger.handlers)}")  # 输出: 正确方式handler数量: 1
    
    # 错误3: 日志级别配置混乱
    print("\n3. 日志级别配置混乱:")
    def level_confusion_demo():
        logger = logging.getLogger('level_confusion')
        
        # 设置logger级别
        logger.setLevel(logging.DEBUG)
        
        # 创建handler但设置不同级别
        handler = logging.StreamHandler()
        handler.setLevel(logging.ERROR)  # handler级别高于logger级别
        
        logger.addHandler(handler)
        
        # 这些日志不会显示，因为handler级别是ERROR
        logger.debug("DEBUG消息")
        logger.info("INFO消息")
        logger.warning("WARNING消息")
        logger.error("ERROR消息")  # 只有这条会显示
        
        print("只有ERROR级别及以上的日志会显示")
    
    level_confusion_demo()
    
    # 错误4: 日志传播问题
    print("\n4. 日志传播问题:")
    def propagation_issue():
        # 配置根logger
        root_logger = logging.getLogger()
        root_handler = logging.StreamHandler()
        root_handler.setFormatter(logging.Formatter('ROOT: %(message)s'))
        root_logger.addHandler(root_handler)
        
        # 创建子logger
        child_logger = logging.getLogger('child')
        child_logger.info("这条消息会传播到根logger")  # 输出: ROOT: 这条消息会传播到根logger
        
        # 关闭传播
        child_logger.propagate = False
        child_logger.info("这条消息不会传播")  # 无输出（没有自己的handler）
    
    propagation_issue()

common_configuration_errors()

print("\n" + "="*50 + "\n")

def performance_and_memory_issues():
    """演示性能和内存相关问题"""
    
    print("性能和内存问题演示:")
    
    # 问题1: 过多的DEBUG日志影响性能
    print("\n1. DEBUG日志性能影响:")
    def performance_test():
        logger = logging.getLogger('performance_test')
        
        # 测试大量DEBUG日志的性能影响
        import time
        
        # 启用DEBUG级别
        logger.setLevel(logging.DEBUG)
        handler = logging.StreamHandler()
        logger.addHandler(handler)
        
        start = time.time()
        for i in range(1000):
            logger.debug(f"调试信息 {i}")
        debug_time = time.time() - start
        
        # 关闭DEBUG级别
        logger.setLevel(logging.INFO)
        
        start = time.time()
        for i in range(1000):
            logger.debug(f"调试信息 {i}")  # 不会实际输出
        info_time = time.time() - start
        
        print(f"DEBUG级别开启时: {debug_time:.3f}秒")
        print(f"DEBUG级别关闭时: {info_time:.3f}秒")
        print(f"性能差异: {debug_time/info_time:.1f}倍")
    
    performance_test()
    
    # 问题2: 内存泄露（长时间运行的应用程序）
    print("\n2. 内存泄露预防:")
    def memory_management_demo():
        logger = logging.getLogger('memory_demo')
        
        # 使用RotatingFileHandler避免单个文件过大
        rotating_handler = logging.handlers.RotatingFileHandler(
            'memory_test.log',
            maxBytes=1024,  # 1KB（测试用，实际应该更大）
            backupCount=3
        )
        logger.addHandler(rotating_handler)
        
        # 定期清理旧的日志文件
        import glob
        import os
        
        # 模拟日志记录
        for i in range(100):
            logger.info(f"测试消息 {i}")
        
        # 检查生成的日志文件
        log_files = glob.glob('memory_test.log*')
        print(f"生成的日志文件: {len(log_files)}个")
        for file in log_files:
            os.remove(file)  # 清理测试文件
    
    memory_management_demo()

performance_and_memory_issues()
```

### 10.2 多线程和多进程日志
```python
import logging
import threading
import time
import queue
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor

def threading_logging_demo():
    """演示多线程环境中的日志记录"""
    
    print("多线程日志演示:")
    
    # 配置线程安全的日志
    logger = logging.getLogger('threading_demo')
    logger.setLevel(logging.DEBUG)
    
    # 创建线程安全的handler
    handler = logging.StreamHandler()
    handler.setFormatter(logging.Formatter('%(threadName)s - %(message)s'))
    logger.addHandler(handler)
    
    def worker(worker_id):
        """工作线程函数"""
        logger.info(f"工作线程 {worker_id} 开始")
        time.sleep(0.1)  # 模拟工作
        logger.info(f"工作线程 {worker_id} 完成")
    
    # 创建多个线程
    threads = []
    for i in range(3):
        thread = threading.Thread(target=worker, args=(i,), name=f"Worker-{i}")
        threads.append(thread)
    
    # 启动所有线程
    for thread in threads:
        thread.start()
    
    # 等待所有线程完成
    for thread in threads:
        thread.join()
    
    print("多线程日志完成")

threading_logging_demo()

print("\n" + "="*50 + "\n")

def multiprocessing_logging_demo():
    """演示多进程环境中的日志记录"""
    
    print("多进程日志演示:")
    
    def setup_multiprocess_logging():
        """设置多进程安全的日志系统"""
        import multiprocessing
        from logging.handlers import QueueHandler, QueueListener
        
        # 创建进程安全的队列
        log_queue = multiprocessing.Queue(-1)
        
        # 设置文件handler
        file_handler = logging.FileHandler('multiprocess.log')
        formatter = logging.Formatter('%(asctime)s - %(processName)s - %(message)s')
        file_handler.setFormatter(formatter)
        
        # 创建QueueListener在主进程中处理日志
        listener = QueueListener(log_queue, file_handler)
        listener.start()
        
        return log_queue, listener
    
    def worker_process(log_queue, process_id):
        """工作进程函数"""
        # 为每个进程设置QueueHandler
        queue_handler = QueueHandler(log_queue)
        logger = logging.getLogger()
        logger.addHandler(queue_handler)
        logger.setLevel(logging.INFO)
        
        # 记录日志
        logger.info(f"进程 {process_id} 开始工作")
        time.sleep(0.1)
        logger.info(f"进程 {process_id} 完成工作")
    
    # 设置多进程日志
    log_queue, listener = setup_multiprocess_logging()
    
    try:
        # 使用进程池
        with ProcessPoolExecutor(max_workers=2) as executor:
            futures = []
            for i in range(2):
                future = executor.submit(worker_process, log_queue, i)
                futures.append(future)
            
            # 等待所有进程完成
            for future in futures:
                future.result()
        
        print("多进程日志完成，请查看multiprocess.log文件")
        
    finally:
        # 停止监听器
        listener.stop()

# 由于多进程在Windows和某些环境中的限制，这里注释掉实际执行
# multiprocessing_logging_demo()
print("多进程日志示例代码已提供，但在某些环境中可能需要调整")

print("\n" + "="*50 + "\n")

def queue_based_logging_demo():
    """基于队列的日志系统演示"""
    
    print("基于队列的日志系统演示:")
    
    import queue
    from logging.handlers import QueueHandler, QueueListener
    
    # 创建线程安全的队列
    log_queue = queue.Queue(-1)
    
    # 设置多个handler
    console_handler = logging.StreamHandler()
    console_handler.setFormatter(logging.Formatter('CONSOLE: %(message)s'))
    
    file_handler = logging.FileHandler('queue_based.log')
    file_handler.setFormatter(logging.Formatter('FILE: %(asctime)s - %(message)s'))
    
    # 创建QueueListener
    listener = QueueListener(log_queue, console_handler, file_handler)
    listener.start()
    
    # 配置logger使用QueueHandler
    logger = logging.getLogger('queue_logger')
    queue_handler = QueueHandler(log_queue)
    logger.addHandler(queue_handler)
    logger.setLevel(logging.DEBUG)
    
    # 记录日志
    logger.info("这条消息通过队列处理")
    logger.error("这条错误消息也通过队列处理")
    
    # 停止监听器
    listener.stop()
    
    print("基于队列的日志系统演示完成")

queue_based_logging_demo()
```

## 11. 实际项目最佳实践

### 11.1 项目日志配置模板
```python
# utils/logger.py
import logging
import logging.config
import os
import sys
from pathlib import Path

def setup_logging(level='INFO', log_dir='logs', log_file='app.log'):
    """
    设置项目日志配置
    
    Args:
        level: 日志级别
        log_dir: 日志目录
        log_file: 日志文件名
    """
    
    # 创建日志目录
    log_path = Path(log_dir)
    log_path.mkdir(exist_ok=True)
    
    # 完整的日志配置
    config = {
        'version': 1,
        'disable_existing_loggers': False,
        
        'formatters': {
            'simple': {
                'format': '%(asctime)s - %(name)s - %(levelname)s - %(message)s',
                'datefmt': '%H:%M:%S'
            },
            'detailed': {
                'format': '%(asctime)s | %(levelname)-8s | %(name)-20s | %(filename)s:%(lineno)d | %(funcName)s() - %(message)s',
                'datefmt': '%Y-%m-%d %H:%M:%S'
            },
            'json': {
                '()': 'utils.logger.JsonFormatter'
            }
        },
        
        'filters': {
            'info_and_above': {
                '()': 'utils.logger.LevelRangeFilter',
                'min_level': logging.INFO,
                'max_level': logging.CRITICAL
            }
        },
        
        'handlers': {
            'console': {
                'class': 'logging.StreamHandler',
                'level': 'INFO',
                'formatter': 'simple',
                'stream': 'ext://sys.stdout'
            },
            
            'file': {
                'class': 'logging.handlers.RotatingFileHandler',
                'level': 'DEBUG',
                'formatter': 'detailed',
                'filename': str(log_path / log_file),
                'maxBytes': 10485760,  # 10MB
                'backupCount': 5,
                'encoding': 'utf-8'
            },
            
            'error_file': {
                'class': 'logging.FileHandler',
                'level': 'ERROR',
                'formatter': 'detailed',
                'filename': str(log_path / 'errors.log'),
                'encoding': 'utf-8'
            },
            
            'debug_file': {
                'class': 'logging.handlers.TimedRotatingFileHandler',
                'level': 'DEBUG',
                'formatter': 'detailed',
                'filename': str(log_path / 'debug.log'),
                'when': 'midnight',
                'interval': 1,
                'backupCount': 7,
                'encoding': 'utf-8'
            }
        },
        
        'loggers': {
            '': {  # 根logger
                'level': 'DEBUG',
                'handlers': ['console'],
                'propagate': False
            },
            
            'app': {
                'level': level,
                'handlers': ['file', 'console', 'error_file'],
                'propagate': False
            },
            
            'app.database': {
                'level': 'INFO',
                'handlers': ['file', 'error_file'],
                'propagate': False
            },
            
            'app.api': {
                'level': 'DEBUG',
                'handlers': ['file', 'console'],
                'propagate': False
            },
            
            # 第三方库配置
            'requests': {
                'level': 'WARNING',
                'handlers': ['file'],
                'propagate': False
            },
            
            'urllib3': {
                'level': 'WARNING',
                'handlers': ['file'],
                'propagate': False
            },
            
            'sqlalchemy': {
                'level': 'WARNING',
                'handlers': ['file'],
                'propagate': False
            }
        }
    }
    
    # 应用配置
    logging.config.dictConfig(config)
    
    return logging.getLogger('app')

def get_logger(name):
    """
    获取配置好的logger
    
    Args:
        name: logger名称
    """
    return logging.getLogger(name)

# 在项目主文件中使用
if __name__ == "__main__":
    # 设置日志
    logger = setup_logging(level='DEBUG')
    
    # 使用日志
    logger.info("应用程序启动")
    
    try:
        # 模拟应用逻辑
        logger.debug("执行某些操作")
        logger.info("操作完成")
        
    except Exception as e:
        logger.exception("应用程序错误")
    
    logger.info("应用程序关闭")
```

### 11.2 日志分析和监控
```python
def log_analysis_and_monitoring():
    """演示日志分析和监控技巧"""
    
    print("日志分析和监控:")
    
    # 1. 日志轮转和归档
    def log_rotation_strategy():
        """日志轮转策略"""
        from logging.handlers import TimedRotatingFileHandler
        
        logger = logging.getLogger('rotation_demo')
        
        # 按时间轮转
        timed_handler = TimedRotatingFileHandler(
            'timed_rotation.log',
            when='midnight',  # 每天午夜
            interval=1,
            backupCount=30,   # 保留30天
            encoding='utf-8'
        )
        
        # 按大小轮转
        size_handler = logging.handlers.RotatingFileHandler(
            'size_rotation.log',
            maxBytes=10485760,  # 10MB
            backupCount=10,     # 保留10个备份
            encoding='utf-8'
        )
        
        logger.addHandler(timed_handler)
        logger.addHandler(size_handler)
        
        print("配置了时间和大小轮转的日志处理器")
    
    log_rotation_strategy()
    
    # 2. 日志级别动态调整
    def dynamic_level_adjustment():
        """动态调整日志级别"""
        logger = logging.getLogger('dynamic_level')
        
        def set_log_level(level_name):
            """设置日志级别"""
            level = getattr(logging, level_name.upper())
            logger.setLevel(level)
            print(f"日志级别已设置为: {level_name}")
        
        # 模拟根据条件调整级别
        set_log_level('INFO')   # 正常运行
        set_log_level('DEBUG')  # 调试阶段
        set_log_level('ERROR')  # 生产环境
    
    dynamic_level_adjustment()
    
    # 3. 日志监控和告警
    def log_monitoring():
        """日志监控和告警"""
        class MonitoringFilter(logging.Filter):
            def __init__(self, alert_callback):
                super().__init__()
                self.alert_callback = alert_callback
                self.error_count = 0
            
            def filter(self, record):
                # 监控错误频率
                if record.levelno >= logging.ERROR:
                    self.error_count += 1
                    
                    # 如果错误频率过高，触发告警
                    if self.error_count > 10:  # 阈值
                        self.alert_callback("错误频率过高！")
                        self.error_count = 0  # 重置计数器
                
                return True
        
        def alert_handler(message):
            """告警处理函数"""
            print(f"告警: {message}")
            # 这里可以发送邮件、短信、Slack通知等
        
        logger = logging.getLogger('monitoring_demo')
        handler = logging.StreamHandler()
        
        # 添加监控过滤器
        monitor_filter = MonitoringFilter(alert_handler)
        handler.addFilter(monitor_filter)
        logger.addHandler(handler)
        
        # 模拟错误日志
        for i in range(15):
            logger.error(f"模拟错误 {i}")
        
        print("监控演示完成")
    
    log_monitoring()

log_analysis_and_monitoring()
```

这份超详细的Python日志笔记涵盖了从基础到高级的所有重要知识点，包括：

1. **基础概念** - 日志的重要性、级别定义
2. **核心组件** - Logger、Handler、Formatter、Filter的详细用法
3. **配置方式** - basicConfig、字典配置、文件配置
4. **高级特性** - 自定义组件、结构化日志、上下文管理
5. **性能优化** - 懒加载、级别检查、参数化日志
6. **错误处理** - 异常记录、堆栈跟踪
7. **多线程/多进程** - 线程安全、进程间日志
8. **实际应用** - 项目模板、最佳实践、监控告警

每个部分都包含了详细的代码注释、易错点说明和使用技巧，确保初学者能够全面理解和掌握Python日志系统的使用。