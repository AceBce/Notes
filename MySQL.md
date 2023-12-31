# MySQL
## MySQL是怎么执行的？
￼![图片](assets/IMG_39.png)

MySQL架构分为两层：Server层和存储引擎层
* Server层：负责建立连接、分析和执行SQL
* 存储引擎层负责数据的存储和提取 
InnoDB是MySQL的默认存储引擎，它支持的索引类型是B+树
1. 连接器
    ```
    mysql -h$ip -u$user -p
    ```
    执行上述语句连接数据库,-h指定MySQL服务的IP地址，如果连接本地服务，不需要这个参数，-u指定用户名，管理员名为root，-p指定密码
    
    连接器的工作有：
    * 与客户端建立TCP连接
    * 校验客户端用户名和密码
    * 读取用户权限，后面的权限判断都是基于此时读到的权限
2. 查询缓存
    
    缓存里有就直接返回，没有就查数据库，
    MySQL8.0直接把缓存删掉了，因为表一更新，这个表的查询缓存就会被清空
3. 解析SQL
    
    由「解析器」对SQL语句进行解析，
    
    也是分为「词法分析」和「语法分析」两个阶段，但也只检查了输入的语句是否满足SQL的语法
4. 执行SQL 
    每条查询语句的流程主要分为三个阶段：
    * 预处理
        * 检查SQL查询语句中的表或字段是否存在
        * 将select * 中的 *符号扩展为表上的所有列
    * 优化
        
    * 执行