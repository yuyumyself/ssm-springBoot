介绍：
	利用代理工厂简易实现springAOP.
	*Test结尾为测试入口
实现：
	1.创建通知
		通知实现MethodInterceptor接口即可，通知里写拦截住目标对象后干什么即可。
	2.创建一个目标对象
	3.
		1.主线程中实例化一个代理工厂ProxyFactory。
		2.往该工厂里添加通知和目标对象。
		3.调用工厂的getProxy()方法获取一个对象（该对象暂叫代理器），
		把代理器强转为目标对象即可。
		注：代理器可理解为已经被添加了通知的目标对象。
		4.执行修改过的目标对象的方法。成功了。
	注：该织入方式，是织入到目标对象所有方法前|后。