# 一  VPN概述
---
* VPN（全称Virtual Private Network）虚拟专用网络
* 依靠ISP和其他的NSP，在公共网络中建立专用的数据通信网络的技术,可以利用公共网络资源动态组成的，可以理解为通过私有的隧道技术在公共数据网络上模拟出来的和专网有同样功能的点到点的专线技术
* 所谓虚拟是指不需要去拉实际的长途物理线路，而是借用了公共inemet网络实现
* 类似VPN隧道
* SSH,LVS,TUN(IPIP),PPTP,IPsec，OpenVPN

# 二 VPN企业应用分类
---
### 2.1 远程访问VPN服务器
* 员工个人电脑通过远程拨号到企业办公网络，如公司oa系统
* 运维人员远程远程拨号的IDC机房，远程维护服务器
### 2.2 企业内部网络之间VPN服务
* 公司分支机构的局域网和总公司的LAN之间的VPN连接,如各大超市之间的业务结算等
### 2.3 互联网公司多IDC机房之间VPN服务
* 不同机房之间业务管理和业务访问，数据流动
### 2.4 企业外部vpn服务
* 在供应商，合作伙伴的LAN和本公司的LAN之间建立的vpn服务
### 2.5 访问国外的网站
* 翻墙业务应用

# 三 实现VPN的常见开源产品
---
> 3.1 PPTP VPN 最大的优势windows原声支持，不需要安装客户端，缺点是很多小区及网络设备不支持pptp导致无发访问


>3.2 SSL VPN 典型OpenVpen，不但适合用于pptp的场景，还适合对企业异地俩地总分公司之间的vpn不间断按需连接，缺点需要安装客户端

* 易用性： PPTP>L2TP>Openvpn
* 速度: Pppt>OpenVpen>L2tp>OpenVpentcp
* 安全性: OpenVpen>L2tp>Pptp
* 稳定性：OpenVpen>L2tp>Pptp

# 四  安装pptp
<pre>
>检查设备是否支持PPTP
# cat /dev/ppp 
cat: /dev/ppp: No such device or address
>设置内核转发 可以把公网的数据包转发的内网的网卡
# grep forw /etc/sysctl.conf
echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# yum -y install pptpd
# rpm -qa |grep pptpd
pptpd-1.4.0-2.el7.x86_64
</pre>

### 4.1.1 配置pptp
<pre>
# vim /etc/pptpd.conf

localip 123.56.150.1
remoteip 10.44.35.10-20

# vim /etc/ppp/chap-secrets
genglei * 1q2w3e4r *

# systemctl start pptpd
# netstat -lntup|grep 1723
tcp        0      0 0.0.0.0:1723 

# vim /etc/ppp/ip-up 打印日志
echo "$PEERNAME 分配IP: $5 登录IP: $6 登录时间: `date -d today +%F_%T` " >>/var/log/pptpd.log

vim /etc/ppp/ip-down #记录登录退出
echo "$PEERNAME 下线IP： $6 下线时间:  `date -d today +%F_%T`" >>/var/log/pptpd.log

genglei 下线IP： 125.35.5.254 下线时间:  2017-02-19_17:10:27
genglei 分配IP: 10.26.12.8 登录IP: 125.35.5.254 登录时间: 2017-02-19_17:10:45
</pre>
