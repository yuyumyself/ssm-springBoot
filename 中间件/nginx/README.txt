nginx:
	官网：http://nginx.org/en/download.html
	Nginx：
		Nginx("engine x")是一款是由俄罗斯的程序设计师Igor Sysoev，
		开发的高性能 Web（Http） 和 反向代理 服务器，也是一个 IMAP/POP3/SMTP 代理服务器。	
		ng特点：
			能够支持高达 50,000 个并发连接数的响应。
			使用ng能够让你达到不间断服务的情况下进行软件版本的升级。
			消耗小，Nginx采用C进行编写，不论是系统资源开销还是CPU使用效率都比 Perlbal 要好很多。
			稳定几乎可以做到7*24不间断运行，即使运行数个月也不需要重新启动。
	nginx的安装：
		./linux安装nginx.txt
	
	反向代理：
		https://www.cnblogs.com/anruy/p/4989161.html
		介绍：         
			Reverse Proxy是指以代理服务器来接受Internet上的连接请求，然后将请求转发给内部网络上的服务器；
			并将从服务器上得到的结果返回给Internet上请求连接的客户端，此时代理服务器对外就表现为一个服务器。
		反向代理的两种应用：
			可以作为内容服务器的替身，也可以作为内容服务器集群的负载均衡器。
			 1，作内容服务器的替身：
				Nginx 反向代理的指令不需要新增额外的模块，ng默认自带 proxy_pass 指令，只需要修改配置文件就可以实现反向代理。
				https://blog.csdn.net/linlin_0904/article/details/89633150
                配置文件：
				    server {
						listen 9888;
						server_name  58xuejia.cn;
						location / {
							proxy_pass https://www.baidu.com;   #代理地址，代理百度
							#proxy_redirect off; #禁止跳转
							# 便于获取用户真实ip https://blog.csdn.net/qq_28796345/article/details/88685245
							#proxy_set_header Host $host;
							#proxy_set_header  X-Real-IP        $remote_addr;
							#proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
							#proxy_set_header X-NginX-Proxy true;

						}
					}
					附：
						
					    #rewrite重写URL，代理后端接口来解决跨域
						location ^~ /api/ {
							rewrite ^/api/(.*) /$1 break;
							proxy_pass http://localhost:6081;
							
						}
				附：
					可提高内容服务器的安全性。
						如在防火墙外部设置一个代理服务器作为内容服务器的替身。
						当客户机向站点提出请求时，请求将转到代理服务器。然后，代理服务器通过防火墙中的特定通路，将客户机的请求发送到内容服务器。内容服务器再通过该通道将结果回传给代理服务器。代理服务器将内容发送给客户机。
						这样，代理服务器就在安全数据库和可能的恶意攻击之间提供了又一道屏障。与有权访问整个数据库的情况相对比，就算是侥幸攻击成功，作恶者充其量也仅限于访问单个事务中所涉及的信息。未经授权的用户无法访问到真正的内容服务器，因为防火墙通路只允许代理服务器有权进行访问。
						附：
							可以配置防火墙路由器，使其只允许特定端口上的特定服务器有权通过防火墙进行访问，而不允许其他任何机器进出。
			2.作为内容服务器集群的负载均衡器：
				./负载均衡.txt
	ng支持https：
		https://www.cnblogs.com/jingxiaoniu/p/6745254.html
		注：
			1.从nginx 1.15以及更新的版本，不在支持ssl on;的写法 了
			2.当一个域名开启https后，谷歌浏览器就会自动对该域名应用http转https，即使你切换端口也会。
		实现：
		1.开启nginx的ssl模块
			...
		2.配置文件：
			server{
				#listen 80;#监听端口
				listen 80;
				listen 443 ssl;
				#ssl证书(公钥.发送到客户端的)
					ssl_certificate ssl/1_58xuejia.cn_bundle.crt;
				#ssl私钥,
					ssl_certificate_key ssl/2_58xuejia.cn.key;
				server_name 58xuejia.cn;#域名
				
				location / {
					#root /myspace/developEnvironment/ng/nginxRunningEnvironment/html;#站点目录
					root /myspace/developEnvironment/projects/eas-fe-h5/;#站点目录
					index  index.html index.htm;
				}
			}
		附：
		1.https简介
			HTTPS其实是有两部分组成：HTTP + SSL / TLS，也就是在HTTP上又加了一层处理加密信息的模块。
			服务端和客户端的信息传输都会通过TLS进行加密，所以传输的数据都是加密后的数据
		2.https协议原理
			首先，客户端与服务器建立连接，各自生成私钥和公钥，是不同的。服务器返给客户端一个公钥，然后客户端拿着这个公钥把要搜索的东西加密，称之为密文，并连并自己的公钥一起返回给服务器，服务器拿着自己的私钥解密密文，然后把响应到的数据用客户端的公钥加密，返回给客户端，客户端拿着自己的私钥解密密文，把数据呈现出来
附：
	./ng配置文件介绍.txt