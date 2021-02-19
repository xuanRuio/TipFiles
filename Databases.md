# **Mysql**

---

### 数据库操作

```mysql
- 展示所有数据库
	show databases;
- 创建数据库（默认拉丁1字符集，不支持中文存储）
	create database [dbname] charset=utf8;
- 展示建库语句
	show create database [dbname];
- 查看当前数据库e
	select database();
- 切换数据库
	use [dbname];
- 删除数据库
	drop database [dbname];
```



### 表结构操作

```mysql
- 展示所有数据表
	show tables;
- 新建表结构
	create table [tbname](
        [字段名] [数据类型] [字段约束] ，
        ...		...		... 
    );
    - example:
                create table stutends(
                    id int unsigned(无符号) primary key(主键) auto_increment(自增),
                    name varchar(16) not null,
                    age tinyint unsigned default(默认值) 18,
                    gender enum('man','women') default 'unknow',
                );
- 展示建表语句
	show create table [tbname];
- 描述(describe)表结构
	desc [tbname];
- 删除表
	drop table [tbname];
# 修改表结构
- 添加字段
	alter table [tbname] add [字段名] [数据类型] [字段约束];
- 删除字段
	alter table [tbname] drop;
- 修改字段类型
	alter table [tbname] modify [旧字段名] [数据类型] [字段约束];
- 修改字段名
	alter table [tbname] change [旧字段名] [新字段名] [数据类型] [字段约束]；

	
```



### 表数据操作

```mysql
- 查询表数据
	select [字段名1],[字段名2] from [tbname];
	select * from [tbname]; 查询所有字段,大数据表慎用；
- 插入表数据
	insert into [tbname] (字段名1,...) values(); 字段名与表结构和数据值需对应；
	全列插入数据时，插入字段名可省略：
	insert into [tbname] values();
	同时插入多条数据：
	insert into [tbname] values(),(),();
	- example:
                insert into students (id,name,age) values (1,'xuanRui',18);
                insert into students values(0,'xuanrui',18);
                insert into students values(0,'xuanrui',18),(1,'xuanrui2',19);
# 导入.sql文件数据
	source /home/user/Desktop/areas.sql;
- 修改表数据
	update [tbname] set [字段名]=[数据],[字段名]=[数据] where [条件语句];
- 删除表数据(物理删除，难以恢复)
	delete from [tbname] where [条件语句];
- 删除表数据（逻辑删除，假删，本质是为该条数据添加标识）
	[新建逻辑删除字段，默认值0，删除数据更值为1]
	- example:
                alter table students add is_delete bit default 0;
                update students set is_delete=1 where name = 'xuanrui';
```



### 查询进阶操作

```mysql
- as
	[为表名或字段名起别名,仅当前sql语句生效,as关键字可省略;]
	select s.id,s.age form [tbname] as s;
	select id as '编号',age as '年龄' from [tbname]; 
- distinct
	[将查询结果中的重复数据去重]
	select distinct age from [tbname];
- where
	[条件语句关键词，筛选符合条件的数据]
	select * from [tbname] where age > 18; (查询表中年龄大于18的所有用户)
	- 比较运算符
		大于等于：>= 	不等于：!= or <>
	- 逻辑运算符
		and		or		not
- like
	[模糊查询]
	% 表示任意个任意字符
	_ (下划线)表示任意一个字符
- between and
	[连续范围查询]
	select * from [tbname] where age between 18 and 22; (查询表中年龄在18-22岁的用户)
- in / not in
	[非连续范围查询]
	select * from [tbname] where age in (18，20，22); (查询表中年龄为18\20\22岁的用户)
	select * from [tbname] where age not in (18，20，22); (年龄不为18\20\22岁的用户)
- IS NULL / IS NOT NULL
	[判空查询]
	select * from [tbname] where age IS NULL; (查询表中年龄为空的用户)
	select * from [tbname] where age IS NOT NUll; (查询表中年龄不是空的用户)
- order by / ASC(升序;默认) / DESC(降序)
	[排序查询]
	- 单字段排序
        select * from [tbname] order by age; (根据年龄排升序)
        select * from [tbname] order by age DESC; (根据年龄排降序)
    - 多字段排序(根据sql语句中字段的顺序依次进行，相等值则跟据后一字段排)
    	select * from [tbname] order by age,height DESC,id; 
    	(首先根据年龄排升序，年龄相同则根据身高排降序，身高相同则根据id排升序)
- limit
	[分页查询;多用于热搜榜/排行榜,排序+分页;]
	select * from [tbname] limit 5;== select * from [tbname] limit 0,5;
	(查询表中的前5条数据,limit默认起始下标0)
	第n页，每页显示m条数据：limit m*(n-1),m
	
# 聚合函数
  [聚合函数均不计算NULL值]
- sum / avg / count / max / min
  和	/ 平均值 / 数量 / 最大值 / 最小值
	select sum(age) from [tbname]; (表中年龄总和)
	- 如果计算时，NULL值需要计算时：
    	select avg(ifnull(height,180)) from [tbname]; 
    	(计算表中的平均身高，若为NULL值，赋值为180参与计算)
    	
# 分组函数
- group by / group_concat
	- 注意:分组之后的数据不再是二维数据,所以select只能查询分组字段
	select age from [tbmname] group by age;
	- 查询除分组字段之外的字段，需借助关键字group_concat或聚合函数实现
	select age,group_concat(name) from [tbmname] group by age;
- having
	[分组条件]
	- 查询以年龄分组后，组内成员大于3的组数据
	select age,group_concat(name) from [tbmname] group by age having count(*)>3;
- with rollup
	[汇总行]用于显示select查询时聚合函数的统计和计算结果
	select age from [tbmname] group by age with rollup;
	
# 分组函数 + 聚合函数
	select age,group_concat(name),avg(id),avg(age) from [tbmname] group by age;
	
```



### 查询高级操作

```mysql
 - 数据准备 :
------------------------------------------------------------------------------------
create table hero(id int unsigned primary key auto_increment,name varchar(64) not null,gongfuid int unsigned not null
);
create table gongfu(id int unsigned primary key auto_increment,name varchar(64) not null
);
insert into hero (name,gongfuid) values ('妲己',2),('李白',1),('程咬金',3),('公孙离',5);
insert into gongfu (name) values ('吟诗作对'),('魅惑'),('三板斧'),('沉默');
------------------------------------------------------------------------------------

# 连接\链接\联合查询
- join on
	select * from hero join gongfu; (笛卡尔积，左右表相应拼接)
- 内连接
	select * from hero inner join gongfu on hero.gongfuid=gongfu.id;
- 左外连接 (outer可省略) [内连接基础上，加上左表数据，右表数据以NULL填充]
	select * from hero left outer join gongfu on hero.gongfuid=gongfu.id; 
- 右外连接 (outer可省略) [内连接基础上，加上右表数据，左表数据以NULL填充]
	select * from hero right join gongfu on hero.gongfuid=gongfu.id; 
- 自连接
	[将同一张表的数据通过起别名的方法以左右表的形式拼接]
	select * from [tbname] [name1] join [tbname] [name2] on name1.字段=name2.字段;

# 子查询
	[查询语句的嵌套使用]
	- 标量子查询：子查询的语句的返回为一个值
		select * from [tbname] where height > (select avg(height) from [tbname]);
	- 列级子查询：子查询的语句返回为一个字段中的多个(行)值
		select name from gonfu where id in (select gonfuid from hero);
	- 行级子查询：子查询的语句的返回为一行中的多个字段
		select * from [tbname] where (height,age)=(select max(height), max(age) from [tbname]); 查询身高和年龄均为最大的数据;

# 外键约束
- 添加外键约束
	alter table [sub_tbname] add foreign key(字段) references [main_tbname](字段)；
	example:
		hero表(子表)中的gongfuid字段来自gongfu表(主表)中的id字段 ；
		alter table hero add foreign key(gongfuid) references gongfu(id);
```



### SQL执行顺序

```mysql
(8)SELECT (9)DISTINCT<select_list>
(1)FROM <left_table>
(2)<join_type>JOIN<right_table>
(3)ON <join_condition>
(4)WHERE <where_condition>
(5)GROUP BY <group_by_list>
(6)WITH {CUBE|ROLLUP}
(7)HAVING<having_condition>
(10)ORDER BY<order_by_list>
(11)LIMIT <limit_number>
										语句顺序：由上至下;	执行顺序：编号由小到大
```



### pymysql操作

- 查询操作

```python
import pymyql

if __name__ == '__main__':
    # 建立与数据库的连接
    conn = 
    pymsql.connect(host='127.0.0.1', port=3306, user='root', password='mysql',
                  database='tb_users', charset='utf8'(字符集，建议与数据库一致)
                  )
    # 连接获取游标
    cur = conn.cursor()
    # 游标执行SQL语句
    sql = "select * from tb_users;"
    cur.execute(sql)
    # 获取游标中的一条数据
    cur.fetchone() # 返回值为元组,取完返回None
    ------
    # 获取全部数据
    cur.fetchall()
    # 销毁游标
    cur.close()
    # 关闭连接
    conn.close()
    
```

- 数据操作

```python
import pymyql

if __name__ == '__main__':
    # 建立与数据库的连接
    conn = 
    pymsql.connect(host='127.0.0.1', port=3306, user='root', password='mysql',
                  database='tb_users', charset='utf8'(字符集，建议与数据库一致)
                  )
    # 连接获取游标
    cur = conn.cursor()
    # 游标执行SQL语句
    sql = "insert into tb_users VALUES (0,'xuanRui',11)"  # 增删改替换SQL语句
    ****************************************************************************
    * 注意：当SQL语句中需要传入参数时											 *
    *		如：sql = "select * from tb_users where id='%s';" % id 			*
    *											 # %s加引号，表字符串 		 *
    *	  	为防止SQL注入或脱库等安全问题，应将参数以列表的形式传给execute的第二个参数 *
    *       即参数化列表：													  *	
    *       sql = "select * from tb_users where id=%s;" # %s不加引号，仅用于占位   *	
    *        sql_args = [id]												*
    *        cur.execute(sql,sql_args)										 * 
  	*****************************************************************************
    row_count = cur.execute(sql) # 返回值int,表示影响数据表的行数
    # 提交操作 将修改的数据保存至数据库中
    conn.commit()
    # 销毁游标
    cur.close()
    # 关闭连接
    conn.close()
```



# Redis

---

特点：

- 底层是C，性能高；
- 内存型数据库亦可持久化存储；
- K:V形式存储；
- 可用做缓存；

使用场景：

- 大数据，爬虫；
- 浏览记录，购物车等；



### Redis操作

```mysql
[Redis数据存储均为 key:value 形式]

# 键指令
- 查看当前库中所有Key - keys *
- 精确查看当前库中所有A开头的Key - keys A*
- 删除 - del k
- 是否存在key - exists k
- 查看kv类型 - type k
- 设置key的过期时间 - expire k t
- 查看过期时间 - ttl            [没有该Key或过期时间到了，返回-2;未设置过期时间返回-1]

- 清空所有数据库[16个] - flushall    
- 清空当前数据库 - flushdb
- 切换数据库 - select dbid      [0-15 共16个数据库,默认0号数据库]

# String
# [字符串; k:键 v:值]
- 增 - set k v
- 增多值 - mset k1 V1 k2 V2
- 删 - del k
- 改 - set k v
- 查 - get k
- 查多值 - mget k1 k2
- 追加 - append k v
- 有效期 - setex k t v

# Hash
# [哈希; 存储对象; o:对象 a:属性 v:值]
- 增 - hset o a v
- 增多值 - hmset o a1 v1 a2 v2
- 删对象 - del o
- 删属性 - hdel o a
- 改 - hset o a v
- 查 - hget o a
- 查多值 - hmget o a1 a2 
- 获取对象的所有属性 - hkeys o
- 获取对象的所有值 - hvals o
- 获取对象的所有属性和值 - hgetall o

# List
# [列表; 可重复; 有序; k:键 v:值 ov:旧值 nv:新值 index:下标 count:个数]
- 增 - lpush/rpush      [左插入lpush,先进后出; 右插入rpush,先进先出]
- 删 - lrem k count v   [-2 A:从右侧开始删除2个A; 3 A:从左侧开始删除3个A;0 A 全部删除A]
- 删 - rpop k  		   [边取边删]
- 改 - lset k index nv
- 查 - lrange k 0 -1    [0 -1 表示下标范围:开头至结尾]
- 插入 - linsert k before/after ov nv  [before前方插入;after后方插入]
- 截取 - ltrim k 0 -1   [0 -1 表示下标范围:开头至结尾;全闭区间]

# Set
# [无序集合; 不可重复; 无序；k:键 v:值]
- 增 - sadd k v
- 删 - srem k v
- 查 - smembers

# zSet
# [有序集合; 不可重复; 有序；k:键 v:值 s:权重值score]
- 增 - zadd k s1 v1 s2 v2
- 删 - zrem k v 
- 获取权重 - zscore k v 
- 根据权重删除 - zremrangebyscore k s1 s2   [权重范围]
- 查 - zrange k 0 -1                       [0 -1 表示下标范围:开头至结尾;全闭区间]
- 根据权重删除 - zrangebyscore k s1 s2      [权重范围]
```



### python&redis交互

```python
import redis

try:
	# 建立连接
	client = redis.StrictRedis(
        host = '127.0.0.1',
        port = 6379,
        db = 0
    )
    
    save_key = 'python_redis'
    # 增
	add = client.set(save_key,'example')
    # 改
    #modify = client.set(save_key,'example2')
    # 查 [返回值为bytes类型]
    #result = client.get(save_key)
    #print(result.decode())
    # 删
    #delete = client.delete(save_key)
	   
    # 关闭连接
    client.close()?
except Exception as e:
    print(e)
```



