\# 安装ShadowsocksR服务

sudo -i

wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh && chmod +x shadowsocksR.sh

./shadowsocksR.sh

\# 开启Debian9自带的TCP BBR加速

echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf

echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf

sysctl -p

sysctl net.ipv4.tcp_available_congestion_control

lsmod | grep bbr

\# V2Ray + Shadowsocks

\# Debian 9

sudo lsof -i -P | grep -i "listen"

ssh-keygen -R <ip>

bash <(curl -s -L https://git.io/v2ray.sh)

\# 详细步骤：

1.创建VM实例，我们点击左上角的导航菜单选项，选择Compute Engine（计算机引擎），在子菜单选择VM实例：

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_1.jpg)

2.我们进入VM实例创建页面，直接点击创建实例选项：

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_2.jpg)

3.我们接下来进行VM实例创建的配置选项如图所示，VM实例名称自定义，VM实例地区选择靠近亚洲地区asia开头的地区，机器类型选择微型即可：

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_3.jpg)

4.我们接下来选择VM实例的启动磁盘，这里需要我们更改成成功率比较高的Debian GNU/Linux 8(jessie)，最后记得勾选上允许HTTP流量和允许HTTPS流量，最后设置完成后我们点击创建：

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_4.jpg)

5.我们创建成功就会出现一个VM实例的列表，如下图所示：

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_5.jpg)

6.VM实例创建好之后我们接下来进行SSR和BBR的安装。我们在创建好的VM实例名称末尾的连接选项中选择在浏览器窗口中打开，之后正式进入搭建的配置界面：

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_6.jpg)

\7. sudo -i

\8. wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocksR.sh && chmod +x shadowsocksR.sh

\9. ./shadowsocksR.sh

10.一直回车，记录最后的账号密码

11.我们安装完SSR和BBR之后，我们需要设定一下外网IP地址为静态，保证IP地址的稳定性。我们选择左上角的导航菜单，选择VPC网络，选择子菜单外网IP地址选项：

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_7.jpg)

12.我们进入到外网IP地址页面，我们找到我们创建好的IP地址，IP地址类型分为了临时和静态两种，我们将IP地址修改为静态，这时我们会有一个弹窗信息需要我们输入，我们起一个名字点击保留即可。注意在同一个主机地址下只能保留一个静态IP地址，否则将会收到Quota ‘STATIC_ADDRESSES’ exceeded. Limit: 1.0 in region asia-east1 提示。

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_8.jpg)

13.我们接下来设置防火墙规则，我们在侧边栏VPC网络中找到防火墙规则：

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_9.jpg)

14.我们需要将http-server和https-server分别进行协议/端口号的设置：

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_10.jpg)

15.我们直接点击http-server和https-server的名称进入到详细页面并点击修改按钮进行端口的设置，这里的端口号就是我们刚才安装完SSR和BBR之后获得的SSR的端口号，这里在输入的时候采用英文输入模式下进行输入，否则会出现报错。输入完成之后记得点击保存按钮。

![](/Users/friedrich/PycharmProjects/GitHub/Wiki/images/GCP搭建ShadowsocksR_11.jpg)

16.我们将以上的工作完成之后就可以下载SSR的客户端进行相关配置了，SSR的下载地址我们访问SSR的官网地址根据个人的设备下载不同的客户端即可。