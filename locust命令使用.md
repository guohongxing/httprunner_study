#locust性能测试
##安装
1.pip install locustio

or easy_install locustio 

losust -help

2.想要在多个机器上运行locust

pip install pyzmq

or 

easy_install pyzmq
##使用
    from locust import Httplocust ,TaskSet ,task
    class UserBehavior(TaskSet):
    	def on_start(self):
    		self.login()
    	
    	def login(self):
    		self.client.post("/login",{user:"aaa",password:"123456"})
    	
    	@task(2)
    	def index(self):
    		self.client.get('/')
    	
    	@task(1)
    	def profile(self):
    		self.client.get('/profile')
    class websiteuser(Httplocust):
    	task_set= UserBehavior
    	min_wait=5000
    	max_wait=9000
##运行
1. 文件在当前目录：locust --host=http://example.com
2. 文件不在当前目录：locust -f 文件路径 --host=http://example.com
3. 分布式，运行主进程 locust --master
4. 分布式，运行其他进程 locust --slave
5. 想要在多个机器上运行分布式系统，可以在启动奴隶时候，制定主主机 locust -f 文件名 --slave --master-host=192.168.0.100 --host=http://example.com


### no-web 模式
1. locust -f load_test.py --host=http://www.baidu.com  --no-web -c 10 -r 2 -t 1m
   -c 设置虚拟用户数
   -r 设置每秒启动虚拟用户数
   -t 设置运行时间
### 分布式运行
master和每一台slave机器，在运行分布式测试的时候都必须要有locust的测试文件

事例：
在master模式下启动locust
1.locust -f my_locustfile.py --master
在每个slave中执行
locust -f my_locustfile.py --salve --master-host=192.168.0.14

–master-bind-host=X.X.X.X`

可选项，与 --master 一起结合使用。决定在 master 模式下将会绑定什么网络接口。默认设置为*(所有可用的接口)。

–master-bind-port=5557

可选项，与 --master 一起结合使用。决定哪个网络端口 master 模式将会监听。默认设置为 5557。注意 Locust 会使用指定的端口号，同时指定端口+1的号也会被占用。因此，5557 会被使用，Locust 将会使用 5557 和 5558。
–expect-slaves=X

在 no-web 模式下启动 master 时使用。master 将等待X连接节点在测试开始之前连接。

    	
