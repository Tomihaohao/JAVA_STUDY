# linux 常用命令

Ubuntu Linux: Start / Stop / Restart / Reload OpenSSH Server

### 启动SSH
- sudo /etc/init.d/ssh start   /restart  stop
- sudo service ssh start       /restart  stop
- sudo systemctl start ssh     /restart  stop

### 安装mongodb

- sudo apt-get install mongodb
- mongo -version
- service mongodb start / stop
- show dbs:显示数据库列表
- show collections：显示当前数据库中的集合（类似关系数据库中的表table）
- show users：显示所有用户
- use yourDB：切换当前数据库至yourDB
- db.help() ：显示数据库操作命令
- db.yourCollection.help() ：显示集合操作命令，yourCollection是集合名
- MongoDB没有创建数据库的命令，如果你想创建一个“School”的数据库，先运行use School命令，之后做一些操作（如：创建聚集集合db.createCollection('teacher')）,这样就可以创建一个名叫“School”的数据库
