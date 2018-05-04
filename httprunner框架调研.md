#httprunner接口自动化测试框架调研报告
##背景
 
当前市面上存在的接口测试工具已经非常多，常见的如Postman、JMeter、RobotFramework等，但在项目中运行的话，还是有一定差距的，如：

	1.无法添加请求的头部引用；
	2.无法开展接口回归测试，线上接口监控；
	3.接口断言有一定局限性，无法根据业务进行开展；
		
##核心的特性
	1.支持api接口的多种请求，包括 GET/POST/HEAD/PUT/DELETE 等
	2.测试用例与代码分离，测试用例维护方式简洁优雅，支持YAML;说明：支持把抓好的包文件导出为har文件转换为YAML文件
	3.接口测试用例具有可复用性，便于创建复杂测试场景；说明：用例分层 
	4.测试结果统计报告简洁清晰，附带详尽日志记录，包括接口请求耗时、请求响应数据等
	5.身兼多职，同时实现接口管理、接口自动化测试、接口性能测试（结合Locust）
	6.测试执行方式简单灵活，支持单接口调用测试、批量接口调用测试、定时任务执行测试，说明：集成jenkins
	
##技术实现
>语言：python(3.3.6)
>
>接口请求：requests
>
>接口断言：unittest
>
>接口性能：locust

##框架的使用
###安装：
    pip install httprunner
安装成功后，会有如下命令：

1.httprunner:核心命令

	httprunner optional arguments:
	
		不加参数，后面跟一个yaml文件，执行用例测试
		
		-h:帮助
	
		--http-report-name:  生成的报告文件名
	
		--http-report-template ：生成报告使用的模版；后面加模版的路径
	
		--log-level:log等级，默认为INFO
	
		--dot-env-path: 请求需要的认证路径，env文件
		
		--failfast:遇到请求失败或是报错，是否要终止后面的用例执行；后面跟true或false
		
		--startproject:后面跟整个项目的用例路径
		
		--validate:未研究通，未在源码发现
		
		--prettify:未研究通，未在源码发现
		
   	源码解析：采用的是argparse，命令行参数解析模块

2.hrun:httprunnerde的缩写，功能和httprunner完全相同

3.locusts：基于locust实现性能测试

4.har2case:har文件转换为yaml文件命令
		
##基础框架实现
1.测试用例和代码的分离实现：
    基于http的API是采用的是Requests模块，统一采用接口。
    
        requests.request(method, url, **kwargs)
     kwargs可以是headers,cookies,params,data,auth等。
     
2.如何在用例中实现函数的定义和调用，解决的问题：请求的头部，包含需要算法生成的参数值

自定义函数模块进行导入
	yaml文件加入：
	
    - test:
   		 name: create user which does not exist
   		 import_module_functions:
       		 - tests.data.custom_functions
3.热加载机制，需要定义全局参数，或是引用外部函数

	初始化用例的时候，会默认导入ate/built_in.py文件
	模块文件的同级目录下，带入debugtalk.py文件
4.结果校验器。
	
    - test:
    name: get token
    request:
        url: http://127.0.0.1:5000/api/get-token
        method: GET
    extract:
        - token: content.token
    validate:
        - {"check": "status_code", "comparator": "eq", "expect": 200}
        - {"check": "content.token", "comparator": "len_eq", "expect": 16}
	校验函数引用，直接在comparator后面加入函数名
	
	下一个接口引用上一个接口的结果，extract参数后面添加
	
5.测试用例分层
	
	举例：三个接口：登陆，注册，注销
	测试场景1：注册-> 登陆-> 注销
		场景2：登陆-> 注销
		场景3: 注销-> 查看登陆状态-> 注册新账号
	需要重复编写接口，输入的参数每次都要改变，难度很大，测试用例的分层结构实现了这个功能
	>tree tests
	>tests
	   api
	      v1
	         account.yml
	   debugtalk.py
	   testcases:
	          scenario_a.yml
	 
	 - api:
    def: api_v1_Account_Login_POST($UserName, $Password)
    request:
        url: /api/v1/Account/Login
        method: POST
        json:
            UserName: $UserName
            Pwd: $Password
            VerCode: ""
    validators:
        - eq: ["status_code", 200]
        - eq: ["content.IsSuccess", True]
        - eq: ["content.Code", 200]
    - api:
    def: api_v1_Account_LoginOff_GET()
    request:
        url: /api/v1/Account/LoginOff
        method: GET
    validators:
        - eq: ["status_code", 200]
        - eq: ["content.IsSuccess", True]
        - eq: ["content.Code", 200]
        - 

    - config:
    name: demo
    variable_binds:
        - UserName: test001
        - Password: 123456
    request:
        base_url: http://xxx.debugtalk.com
        headers:
            Accept: application/json
            User-Agent: iOS/10.3
    - test:
    name: Login
    api: api_v1_Account_Login_POST($UserName, $Password)
    - test:
    name: Logoff
    api: api_v1_Account_LoginOff_GET()

6.skip用例跳转机制

	采用的unittest.skip(reson);unittest.skipif(condition,reason)条件为真；unittest.skipUnless(condition.reason)条件为假；
	yaml实现：
	 test:
    name: demo
    skip: "skip this test unconditionally"
    request: {...}
    validate: [...]
##后期要做
1.对测试报告的改进

2.locust框架了解

3.对该框架的进一步理解，如jenkins即成


##感想	
  之前对接口测试的理解不够深刻，该框架的特点：用例和代码的分离；热机制；测试用例的分层做的特别好，让我对接口测试有了新的认识，还需要进一步学习

##说明
  以上文档说明，只代表个人想法和观点，只是部分说明，后续会进一步更新。 


	

