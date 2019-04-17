# node.js-xmysql-postman

node.js+xmysql+postman实现接口自动化测试，并与数据库对比   
所需工具：postman 、nodejs、mysql

### 一、安装nodejs https://nodejs.org/en/download/ 在nodejs官网下载widows版本即可

![nodejs官网](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/nodejs1.jpg)  
安装完毕，可以在cmd命令中检查是否安装成功   
查看nodejs版本：nodes -v   
查看npm 版本：npm -v,正常情况下，安装node的时候会自动安装npm  
![查看nodejs版本，npm版本](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/version.png)  

### 二、安装xmysql插件

打开cmd 执行npm install xmysql -g  
安装成功后就可以使用xmsql连接数据库了，命令： xmysql -u username -p password -h host -o port -d database ,如下图   
![xmsql](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/xmysql.jpg)

### 三、通过 http://localhost:3000/api/ 访问 xmysql 自动生成的 Rest API

例如：查询robot_speak表中id,face_id,face_url,face_name,speak字段信息  
http://localhost:3000/api/robot_speak?_fields=id,face_id,face_url,face_name,speak
![postman调用xmysql查询数据库](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/select1.jpg)  
查看xmyslq窗口中的log
![xmysql log](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/log1.jpg)

### 四、xmysql 数据库操作常用的写法，可以参考官方文档https://github.com/o1lab/xmysql#api-overview

| 请求类型   | 请求路径                      | 备注                                             |
| ------ |:-------------------------:|:---------------------------------------------- |
| get    | /api/tableName            | 查询所有行                                          |
| psot   | /api/tableName            | 新建一行                                           |
| put    | /api/tableName            | 替换行为新的行                                        |
| psot   | /api/tableName/bulk       | 批量新增行 -- 在请求值发送数组                              |
| get    | /api/tableName/bulk       | 批量列出行 -- /api/tableName/bulk?_ids=1,2,3        |
| delete | /api/tableName/bulk       | 批量删除行 -- /api/tableName/bulk?_ids=1,2,3        |
| get    | /api/tableName/:id        | 根据主键查询一行                                       |
| patch  | /api/tableName/:id        | 根据主键更新一行                                       |
| delete | /api/tableName/:id        | 根据主键删除行                                        |
| get    | /api/tableName/findOne    | 列出所有行                                          |
| get    | /api/tableName/count      | 统计表行数                                          |
| get    | /api/tableName/distinct   | 表数据去重复 -- /api/tableName/distinct?_fields=col1 |
| get    | /api/tableName/:id/exists | 验证行是否存在，返回true 或 false                         |
| get    |/api/tableName/aggregate |统计表字段中的最小值、最大值、平均值、求和、标准差、方差,例：http://localhost:3000/api/robot_speak/aggregate?_fields=id |
| get| 	/api/tableName/groupby|分组查询|
|get | 	/api/xjoin|内连接：/api/xjoin?_join=pl.productlines,_j,pr.products&_on1=(pl.productline,eq,pr.productline)&_fields=pl.field1,pr.field2|
|get | 	/upload|上传单个文件：curl --form file=@/Users/me/Desktop/a.png http://localhost:3000/upload|
|get | 	/uploads|上传一组文件：curl --form files=@/Users/me/Desktop/a.png --form files=@/Users/me/Desktop/b.png http://localhost:3000/uploads|
|get | 	/download|下载一个文件：http://localhost:3000/download?name=fileName|
|get |	/api/tableName/describe| 查询表字段详细信息 如：http://localhost:3000/robot_speak/describe	|
|get |	/api/tables|查询库中所有表|
|get |	/_health| 查询数据库连接数，获取其他详细信息如：http://localhost:3000/_health http://localhost:3000/_health?details=1 http://localhost:3000/_health?voila http://localhost:3000/_version|
|get |	/_version| 	gets version of Xmysql, mysql, node|

### 五、postman+nodejs+xmysql 实现接口数据与数据库对比  
#### 1、配置postman环境变量，点击设置按钮，进入新建环境变量界面，点击add,输入环境名称 机器人测试环境，新建环境变量URL(待验证的接口host) ，restAPI(xmysql接口host)
![新建环境](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/config1.jpg)

#### 2、环境变量的引用：  
a、切换到机器人测试环境（可以设置多个环境，如生产环境）   
![切换环境](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/changeENV.jpg)  

b、应用环境变量URL,写法{{URL}}  
![环境变量的引用](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/getENV.jpg)

#### 3、接口请求结果处理 
		``` 清除postman的环境变量 search 
		pm.environment.unset("search");
		```  
		``` 用json解析响应结果
		var jsonData = JSON.parse(responseBody);
		```    
		``` 读取json数据中result
		result = jsonData.pageResult.result;
		// console.log(result.length);
		```    
		``` 新建一个空数组 rcodes
		var rcodes = [];
		```    
		```` 循环读取result，读取其中的robotCode,存入rcodes
		for (i=0;i<result.length;i++){
			rcodes[i] = result[i].robotCode;
			}
		// console.log(rcodes.sort().toString());
		```   
		``` 将数组rcodes降序排列并转化为字符串，存入环境变量search
		pm.environment.set('search',rcodes.sort().toString());
		```  
![响应结果处理](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/response1.jpg)
#### 4、读取前一个接口返回的值search，并请求xmysql，对结果进行对比
		``` 新建postman test 
		pm.test('条件查询机器人校验',function(){
		``` 
		``` 把从数据库读取的结果存入数组，并转化为字符串
			var jsonData = JSON.parse(responseBody);
			var codes = [];
			for (i=0;i<jsonData.length;i++){
				codes[i] = jsonData[i].pr_robot_code;
			}

			var expect = codes.sort().toString();  
			console.log(expect);
		```
		```  断言结果
			if (pm.environment.get("search") == expect){
				tests['条件查询验证通过：'+ expect] = true;
			}else{
				tests['条件查询验证失败：'+ expect] = false;
			}    
		});
		```  
![添加断言](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/assert1.jpg)
![断言结果](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/assert2.jpg)

### 六、使用postman的Runner批量验证接口
#### 1、新建runner ，并设置以下参数
		1、选择测试用例接口文件夹
		2、选择环境
		3、设置迭代次数
		5、接口请求时间间隔
		6、响应结果日志级别
		7、数据来源（可以是csv或者是txt格式，填写路径即可，调用的时候先要编写前置脚本读取csv的字段，并保存为环境变量；
		例如列明为book 则前置脚本如下：
		var book = data.book;
		pm.environment.set("book",book);
		在接口中引用直接写成{{book}}即可）  
![新建Runner](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/runner1.jpg)

#### 运行runner ，查看测试结果
##### 1、pass代表校验通过，faill代表校验失败，可以点击请求查看详细请求过程  

![result1](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/result1.jpg)


##### 2、使用 newman 命令生成接口测试报告  
		1、安装newman ,cmd命令：npm install -g newman
		2、导出json格式的测试用例,注意：要使用的环境变量也要一并导出
		![export1](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/export1.jpg)![export2](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/export2.jpg)![export3](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/export3.jpg)
		3、使用newman执行测试用例，并生成测试报告
		cmd:newman run C:\Users\lenovo\Desktop\postman_collection.json --environment C:\Users\lenovo\Desktop\postman_environment.json --reporters html --reporter-html-export C:\Users\lenovo\Desktop\htmlReport.html
![testReport](https://github.com/ming-zh/node.js-xmysql-postman/blob/master/imags/testreport.jpg)
















