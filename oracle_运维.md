# ORACLE 常见管理命令


### 启动流程
```
1. 进入系统su - oracle
2. 执行 lsnrctl start   开启监听
3. 执行 sqlplus / as sysdba
4. startup执行 开启数据库
5. alter pluggable database cdrpdb open;
6. alter pluggable database cdrkypdb open;
7. 在执行show pdbs; 
open model 显示为READ WRITE，说明启动成功;
```

### 容器container切换
```
切换容器后可以修改对应的账户信息并修改表空间
alter session set container=cdrpdb;
```


### 容器内表空间扩建
```
oracle 12c 数据库表空间扩展方法：
1.  ssh 进入oracle 数据库
su - oracle;
sqlplus / as sysdba;

2.  查看表所用的表空间
使用navicate工具查看需要扩增空间的表所使用的表空间名称，如'system'

3.  查看当前用户所在容器位置
show con_name;
若跟目标表空间所在的容器位置不一致，则切换到目标容器，即
进入表空间所在的容器container 
alter session set container=cdrpdb;

4.  查看表空间已有文件所在的位置
select * from dba_data_files;
如，已有表空间位置'/home/data/data/system01.dbf'

5.  根据已有表空间数据文件位置添加表新表空间数据文件位置
alter tablespace  add datafile '/home/data/data/system02.dbf' size 20G autoextend on next 200m maxsize unlimited;

6.  新建完成后表空间使用情况查询
--查看表空间的名字及文件所在位置
select tablespace_name,
       file_id,
       file_name,
       round(bytes / (1024 * 1024), 0) total_space
  from sys.dba_data_files
 order by tablespace_name;

-- 查询表空间信息
select username,default_tablespace,t.* from dba_users t

--查询当前表空间下使用情况
select a.tablespace_name,
       a.bytes / 1024 / 1024 "sum MB",
       (a.bytes - b.bytes) / 1024 / 1024 "used MB",
       b.bytes / 1024 / 1024 "free MB",
       round(((a.bytes - b.bytes) / a.bytes) * 100, 2) "used%"
  from (select tablespace_name, sum(bytes) bytes
          from dba_data_files
         group by tablespace_name) a,
       (select tablespace_name, sum(bytes) bytes, max(bytes) largest
          from dba_free_space
         group by tablespace_name) b
 where a.tablespace_name = b.tablespace_name
 order by ((a.bytes - b.bytes) / a.bytes) desc;


7. 其他情况说明：
删除表文件（如，system 中表文件）： alter tablespace system drop datafile '/home/data/data/system02.dbf';
不推荐使用删除操作；

```


# Reference

[https://www.cnblogs.com/jianshuai520/p/9766970.html](https://www.cnblogs.com/jianshuai520/p/9766970.html)
