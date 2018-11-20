# 基于Redis、Mysql的GRPC C++秒杀系统

- 目前版本是基于mac 版本的GRPC秒杀系统,代码采用bazel编译,分别有客户端、服务器的C++实现

- 服务端整体流程: 一. 开始准备阶段: 
                                   服务开始前,模拟一批商城注册用户,往mysql中插入用户注册信息和商品信息. 用户信息包括用户名 以及 用(用户名+密码+公有盐)生成的md5码,暂时使用的是客户端服务器约定的公有盐,后续可以考虑换成随机盐.
                             二. 信息校验阶段:
                                  1.秒杀开始之前,将用户信息、商品信息提前加载到redis中.
                                  2.秒杀开始,接受客户端请求,首先进行身份信息确认,未注册用户返回秒杀失败.
                                  3.订单重复性校验,已购买用户返回秒杀失败.
                             三. 秒杀阶段
                                  用户信息、订单重复性校验成功则开始秒杀,秒杀阶段采用redis乐观锁+失败重试的方案(重试3次都失败返回秒杀失败).
                            四. 秒杀结束,订单入库
                                 秒杀结束将用户订单同步到mysql,并更新商品数量信息.

   注:整体秒杀阶段在redis中操作,秒杀结束同步mysql
   
   
-  客户端流程:1. 本地开启500线程模拟用户并发请求
                      2. 用户请求参数为用户名 以及 用(用户名+密码+公有盐)生成的md5码.
                      
                      
                      
                      - 系统要求:macOS系统,需要安装mysql、redis、grpc C++、bazel环境,服务端代码中mysql、redis连接需要修改成自己的用户名、密码和端口号
                      
                      - 操作流程:
                      
                      服务端:
                      cd seckill_server
                      ulimit -n 2000
                      bazel build src:seckill_server
                      bazel-bin/src/seckill_server
                      
                      客户端:
                      cd seckill_server
                      ulimit -n 2000
                      bazel build src:seckill_client
                      bazel-bin/src/seckill_client


- 
