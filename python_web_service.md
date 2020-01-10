# python webservice 接口封装




## gunicorn+flask





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



# Reference

- [https://blog.csdn.net/u013421629/article/details/99584403](https://blog.csdn.net/u013421629/article/details/99584403)
