---
title: 阿里巴巴Java开发手册阅读记录
date: 2019-02-22 21:38:14
tags: [java]
categories: Java

---


# 命名规范

## 各层命名规范

Service/Dao 层

1. 获取单个对象 getXXX
2. 获取多个对象 listXXX
3. 获取统计值的方法 countXXX
4. 插入的方法 saveXXX/insertXXX
5. 删除的方法 removeXXX/deleteXXX
6. 修改的方法 updateXXX

领域模型命名规范

1. 数据对象：xxxDO, xxx为数据表名
2. 数据传输对象：xxxDTO, xxx 为业务领域相关的名称
3. 展示对象: xxxVO, xxx 一般为网页名称
4. POJO 是 DO/DTO/BO/VO 的统称，禁止命名成 xxxPOJO

# 常量相关

1. 不要使用一个常量类维护所有常量，要按照常量功能进行归类，分开维护

# 代码格式

1. 使用 4空格 缩进(或在 ide 中设置 tab=4space 并且勾选 Use tab character/insert spaces for tabs);
2. 注释的双斜线与注释内容中加一个空格

# OOP 规约

1. 函数过时使用 `@Deprecated` 修饰，并说明采用的新函数
2. 不使用过时的类
3. 构造函数中禁止加入任何业务逻辑，如果有初始化逻辑，请放在 init 方法中
4. POJO 类必须写 toString 方法，在方法抛出异常的时候可以直接调用 toString 方法打印其属性值
5. 类方法顺序：共有方法或保护方法 > 私有方法 > getter/setter
6. 慎用 Object 的 clone 方法来拷贝对象.

# 集合处理

1. 关于 hashCode, equals 处理
    1. 只要重写 equals, 必须重写 hashCode
    2. Set 存储不重复的对象，根据 hashCode, equals 判断，Set 存储的对象必须重写这两个方法
    3. 如果自定义对象作为 Map 的 key, 必须重写 hashCode, equals。
    4. String 类重写了 hashCode, equals， 所有可以作为 key 使用
2. 不要在 foreach 循环里面操作元素的 remove/add 操作，可以使用 Iterator 方式操作，如果是并发还需要加对象锁
3. 使用 entrySet 遍历 Map 类集合，不要使用 keySet 方式
4. Map 中哪些可以为 null
    1. Hashtable: 不能为 null, 线程安全
    2. ConcurrentHashMap: 不能为 null, 锁分段技术
    3. TreeMap: Value 可以为null, 线程不安全
    4. HashMap: Key, Value 可以为 null, 线程不安全

# 并发处理

1. 创建线程和线程池时指定有意义的线程名称
2. 线程资源必须通过线程池提供
3. 线程池使用 ThreadPoolExecutor 方式创建
4. 对多个资源，数据库表，对象同时加锁时，需要保持一致的加锁顺序，否值可能造成死锁
5. 并发修改同一记录时，避免更新丢失，需要加锁。在应用层加锁或者在缓存加锁，或者在数据层使用乐观锁，使用 version 作为数据更新依据
6. 在并发场景下，通过使用双重检查锁(double-checked locking)实现延迟初始化的优化问题隐患
7. volatile 解决多线程内存不可见问题。

# 控制语句

1. switch 必须包含 default 语句
2. 高并发场景下，避免使用 == 作为中断或退出的条件，容易击穿(比如直接到-1)
3. 表达异常的分支时，不使用 else，而是使用 if return.
4. 循环体内语句要考量性能，比如在循环体外补货异常
5. 不需要参数校验的情况
    1. 底层调用频率高的方法，比如 dao, service 层通常不需要参数过滤，因为在上层已经做了参数校验
    2. 被声明 private 被自己调用的方法，如果可以确认参数合法可以不用校验

# 注释规约

1. 所有类必须添加创建者和创建日期
2. 所有枚举类字段必须有注释

# 异常

1. NPE 防止
    1. 数据库查询结果可能为 null
    2. 集合里的元素即使是 isNotEmpty, 取出的数据也可能是 null
    3. 远程调用返回的对象，一律要求 null 判断
    4. Session 中获取的数据，null 判断
2. 应用中不能直接使用日志系统(Log4j, Logback)中的API, 应该依赖使用日志框架 SLF4J 中的, 使用门面模式的日志框架，有利于维护各个类的日志处理方式统一
3. 日志文件至少保存 15 天
4. 谨慎记录日志。生产环境禁止输出 debug 的日志；有选择输出 info 日志。
5. 可以使用 warn 日志级别记录用户输入参数错误的情况，方便查找

# 单元测试

1. 单元测试代码遵守 BCDE 原则
    1. B: Border, 边界值测试，包括循环边界，特殊值，特殊时间点，数据顺序
    2. C: Correct, 正确的输入，并得到预期的结果
    3. D: Design, 与设计文档相结合，来编写单元测试
    4. E: Error, 强制错误信息输入(如：非法数据，异常流程，非业务允许输入等)，并得到预期的结果
2. 和数据库相关的单元测试，设置自动回滚机制

# 安全规约

1. 用户敏感数据禁止直接展示(比如手机号脱敏处理)
2. 用户输入的 SQL 参数严格使用参数绑定，防止 SQL 注入
3. 用户请求传入的任何参数都必须做有效性验证：使用正则验证客户端的输入
4. 表单，AJAX 提交必须执行 CSRF 安全验证
5. 发帖，评论，发送即时消息等用户生成内容的场景必须实现防刷，文本内容违禁词过滤等风控策略

# MySQL 数据库

1. 表示 是否 的字段，命名：is_xxx, 数据类型 unsigned tinyint(1 是，0 否)
2. 表名不使用复数名词
3. 主键索引：pk_xxx; 唯一索引 uk_xxx; 普通索引 idx_xxx
4. 小数类型为 decimal, 禁止使用 float, double
5. 表必备字段：id, gmt_create, gmt_modified(数据类型 datetime, 表示创建时间和更新时间)
6. 表名：业务名称_表作用
7. 单表行数超过 500w，或者容量超过 2gb，才推荐进行分库分表

## 索引规约

跳过
。。。。

## SQL 语句

1. 不要使用 count(列名|常量) 代替 count(*)
2. count(distinct col) 计算该列除 NULL 之外的不重复行数
3. 使用 ISNULL() 判断是否是 NULL 值
4. 在代码中写分页查询逻辑的时，若 count 为 0 应直接返回

# 应用分层

1. DO(Data Object): 此对象与数据库表结果一一对应，通过 DAO 层向上传输数据源对象
2. DTO(Data Transfer Object): 数据传输对象，Service 或 Manager 向外传输的对象
3. BO(Bussiness Object): 业务对象，由 Service 层输出的封装业务逻辑的对象
4. AO(Application Object): 应用对象，在 Web 层与 Service 层之间抽象的复用对象模型
5. VO(View Object): 显示层对象，通常是 Web 向模版渲染引擎层传输的对象
6. Query: 数据查询对象，各层接收上层的查询请求。注意超过2个参数的查询封装，禁止使用 Map 类来传输




















