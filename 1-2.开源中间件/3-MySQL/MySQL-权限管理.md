## MySQL权限管理

### 参考链接
- https://cloud.tencent.com/developer/article/1656008

### 1. 查询、插入、更新、删除
```
grant select on testdb.* to user@'localhost' 
grant insert on testdb.* to user@'localhost' 
grant update on testdb.* to user@'localhost' 
grant delete on testdb.* to user@'localhost'

等同于
grant select, insert, update, delete on testdb.* to user@'localhost'
```
- 特殊前缀：`grant select on `t\_%`.* to reader@'%';`

### 2. 创建表、索引、视图、存储过程、函数
```
grant create on testdb.* to user@'localhost' ;
grant alter  on testdb.* to user@'localhost' ;
grant drop   on testdb.* to user@'localhost' ;
```

### 3. 创建、修改、删除数据表结构权限
```
grant references on testdb.* to developer@'localhost'
grant create temporary tables on testdb.* to developer@'localhost'
grant index on  testdb.* to developer@'localhost'
grant create view on testdb.* to developer@'localhost'
grant show   view on testdb.* to developer@'localhost'
```

### 4. 存储过程、函数 权限
```
grant create routine on testdb.* to developer@'localhost'  -- now, can show procedure status
grant alter  routine on testdb.* to developer@'localhost'  -- now, you can drop a procedure
grant execute        on testdb.* to developer@'localhost'
```

### 5. 所有权限
```
grant all privileges on testdb to dba@'localhost' 
grant all on *.* to dba@'localhost'
```

### 6. 权限查看
```
show grants;
show grants for dba@localhost;
```
### 7. 权限撤销
```
grant  all on *.* to   dba@localhost;
revoke all on *.* from dba@localhost;
```
### 8. 注意事项
- grant, revoke 用户权限后，该用户只有重新连接 MySQL 数据库，权限才能生效
- 如果想让授权的用户，也可以将这些权限 grant 给其他用户，需要选项 "grant option"
