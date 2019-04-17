# node.js-xmysql-postman
node.js+xmysql+postman实现接口自动化测试，并于数据库对比   
所需工具：postman 、nodejs、mysql

### 安装nodejs https://nodejs.org/en/download/ 在nodejs官网下载widows版本即可   
![nodejs官网](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/nodejs1.jpg)  
安装完毕，可以在cmd命令中检查是否安装成功   
查看nodejs版本：nodes -v   
查看npm 版本：npm -v,正常情况下，安装node的时间会自动安装npm  
![查看nodejs版本，npm版本](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/version.png)  

### 安装xmysql插件     
打开cmd 执行npm install xmysql -g  
安装成功后就可以使用xmsql连接数据库了，命令： xmysql -u username -p password -h host -o port -d database ,如下图   
![xmsql](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/xmysql.jpg)

### 通过 http://localhost:3000/api/ 访问 xmysql 自动生成的 Rest API  
例如：查询robot_speak表中id,face_id,face_url,face_name,speak字段信息，条件是face_id,faceId存在环境变量中
http://localhost:3000/api/robot_speak?_fields=id,face_id,face_url,face_name,speak&_where=(face_id,eq,{{faceId}})
![postman调用xmysql查询数据库](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/select1.jpg)  
查看xmyslq窗口中的log
![xmysql log](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/log1.jpg)

### xmysql 数据库操作常用的写法，可以参考官方文档https://github.com/o1lab/xmysql#api-overview  
| 请求类型 |                 请求路径               |                     备注                   |
|----------|:--------------------------------------:|:-------------------------------------------|
|    get   |/api/tableName                          |查询所有表字段                              |
