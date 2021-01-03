# Cisco Packet Tracer基本操作

##### 1.Telnet配置

```c#
Switch>en //特权视图
Switch#config //全局配置视图
Switch(config)#hostname SW1 //交换机更名
Switch(config)#enable pass 1234 //设置特权模式密码
Switch(config)#int vlan 1 //进入vlan1 接口配置视图
Switch(config-if)#ip add 192.168.1.1 255.255.255.0 //配置交换机管理IP地址
Switch(config)#no shut //开启端口 为Up状态
SW1(config-if)#line vty 0 4 //进入VTY用户配置视图
SW1(config-line)#pass 5678 //设置用户登录密码为5678
SW1(config-line)#end 
    
以上设置设置完毕后就可以去PC里面ping网关和进入sw特权视图了 使用Telnet+网关ip 输入密码 进入视图
```

 

##### 2.单交换机划分VLAN

```c#
//先创建VLAN
Switch(config)#vlan 100
Switch(config)#vlan 200
Switch(config)#int f0/1 //然后进入端口
Switch(config-if)#sw acc vlan 100 //设置该端口的VLAN
and so on
    
以上设置完毕后就可以在全局视图界面show vlan 查看每个vlan有哪些端口。VLAN相同的端口就ping的通了
```



##### 3.多交换机划分VLAN

```c#
//按单交换一样正常设置接口的VLAN
Switch(config-if)#int f0/24
Switch(config-if)#sw mode trunk //设置交换机和交换机相互连接的那个接口为Trunk模式
//两台交换机一样的设置
然后VLAN相同的端口就ping的通了
```



##### 4.利用三层交换机来实现VLAN的划分

```c#
//普通交换机设置 就划分下VLAN 然后 开启trunk端口就ok
//三层交换机设置 也要先创建VLAN 然后将链接的那台PC添加到VLAN中
Switch(config)#ip routing //开启三层交换机ip路由模式
Switch(config-if)#int f0/24 //进入链接交换机的端口
Switch(config-if)#sw trunk  encapsulation dot1q  //设置端口协议
Switch(config-if)#int vlan 100 //通过0/24接口接管下面那台交换机的vlan100
Switch(config-if)#
%LINK-5-CHANGED: Interface Vlan100, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan100, changed state to up
ip add 192.168.1.254 255.255.255.0 //进入端口VLAN 设置允许通过该端口的网关
    
Switch(config-if)#int vlan 200 //通过0/24接口接管下面那台交换机的vlan200
Switch(config-if)#
%LINK-5-CHANGED: Interface Vlan100, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan100, changed state to up
ip add 192.168.2.254 255.255.255.0 //进入端口VLAN 设置允许通过该端口的网关

//设置完毕后，全网互通 
```

一个严重失误记录：把三层交换机的那台PC的ip设置成了192.168.1.2  而设置能进入VLAN200的网关是2.254所以导致无法该PC无法回信所有ping它的pc。



##### 5.交换机端口聚合

```c
Switch(config)#int range f0/1-2 //进入f0/1和f0/2端口
Switch(config-if-range)#sw mode trunk  //设置端口（两个）为trunk端口
Switch(config-if-range)#channel-group 1 mode  on  //设置端口聚合协议                         Switch(config)#port-channel load-balance dst-ip  //配置以太通道的负载平衡方式                 Switch#show etherchannel  summary //查看通道接口状况                                         //设置完毕后就可以相互ping了                                              
```



##### 6.综合路由

```c
Switch(config)#ip route 0.0.0.0  0.0.0.0 192.168.3.1 //ip route 目标ip 掩码 跃点ip
Switch(config)#router rip //设置rip协议
Switch(config-router)#network 192.168.1.0
Switch(config-router)#network 192.168.3.0 //声明本设备的直连网段
Switch(config-router)#version 2 //设置版本号
```

端口要启用才能通讯，分端口的话要打开总端口，分端口就都会打开