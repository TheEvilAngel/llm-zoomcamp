## Additional notes for those trying the streamlit/grafana out

1) The following packages are required when you run some of .py scripts

```
pip install psycopg2-binary python-dotenv
pip install pgcli
```

2) To download the phi3 model to the container
```
docker-compose up -d
docker-compose exec ollama ollama pull phi3
```

## some step
0. export POSTGRES_HOST=localhost, 让db_host链接本地（视频这么做）
    or
    直接修改.env中 POSTGRES_HOST=localhost

1. To see what db saves

```
pgcli -h localhost -U your_username -d course_assistant -W
crtl + d
退出
```

2. python-dotenv从.env文件中拉取环境变量

3. docker-compose中只有streamlit使用本地的dockerfile

4. docker-compose中有volumes, 实际就是本地/var/lib/docker/volumes/，每次直接用里面的存储数据构建containner

5. use grafana, host_url=postgres(no idea), 也有相应的json file

6. 运行流程：docker-compose up -> python prep.py(如果只是init-db则前面记得注释) -> python generate_data -> 随机生成可以查看动态数据了，其中db问题见1

7. 文件构成：
    app             就是streamlit，用localhost打开。
    assistant       就是本地的rag，和之前写的一样，这次又评估
    db              就是数据库，有坑（localhost）
    docker-compose  把所有的docker同时启动，很神奇，之后都可以用docker-compose指令（up/down/start xx/stop xx/ build xx）
    dockerfile      是构建streamlit所用的（其余镜像都是拉取，就这个是build
    generate_data   仿真生成数据，用于观察
    prep            初始化，读取本地rag并且init db
    dashboard       可视化的配置文件
    grafana         用于构建的sql语言
