# python webservice 接口封装

## gunicorn+flask

### 启动流程
```
1. config文件配置
参考：gunicorn_config.py
2. 启动服务
如：gunicorn -b 0.0.0.0:1080 segment_service:app -c gunicorn_config.py

也可以直接启动配置启动服务相关信息
如：nohup gunicorn -w 24 -b 0.0.0.0:1080 flask_service:app > log.txt &
```

# Reference

- [https://blog.csdn.net/u013421629/article/details/99584403](https://blog.csdn.net/u013421629/article/details/99584403)
