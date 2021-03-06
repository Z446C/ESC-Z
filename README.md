# ESC For Shell
基于shell脚本的广东天翼校园登录&校园网认证

---

## 脚本说明
- 使用shell轻量脚本，兼容大部分系统，弥补了天翼无法在linux上使用的缺陷
- 支持window、linux、mac、路由器系统、其他支持shell的系统（如termux）等

### 基本功能
- 自动认证Portal网页
- 一键登录，可根据IP注销网络
- 断线重连
- 日志输出


## 运行环境
### 终端
- 对于Linux/Mac系统可以直接运行
- 对于Windows系统可以安装git，默认带一个bash终端，可以在bash中运行

### 工具包
- curl: 用于http请求（必装）。
- base64: 用于密码编码（非必装），可以自行去工具网站用base64编码密码，再填写到程序对应位置上，使用base64这个工具包可以自动编码，base64包的名字可以自行搜索对应系统的，不同系统有所区别。
- cron(crond): 用于自动执行计划任务，openwrt自带cron，其他系统可以测试一下。

**对于openwrt系统，安装命令**
```shell
opkg install curl
opkg install coreutils-base64
```


## 如何使用

### 准备工作
1. 先在windows等系统填写账号密码（有些终端对中文显示不了）
```
# 必填数据：账号、密码，例如
username="123456789"
passwd="123456"
################################
# 选填数据（保持默认或留空自动获取，最好填写“建议填写”的，一劳永逸）注意格式
# 【建议填写】base64编码过的密码（建议自行编码，不用安装base64），例如：123456编码
pwd64="MTIzNDU2"
# 【建议填写】学校服务器，登录后查看日志，将nasip填入下面，否则会影响注销（wyu默认119.146.175.80）例如
nasip="119.146.175.80"
# 【建议填写】WAN口MAC地址(注意自动获取MAC的函数里的指令是否能成功获取到，若不能请手动填写)，例如：
mac="62:37:E9:BD:F1:C5"
# 用户ip地址，会动态变化，留空
clientip=""
# 【建议填写】学校代号,建议手动填写，节省访问资源（wyu默认1414），例如
schoolid="1414"
# 日志路径、文件大小（kB）
path="/root/ESC-Z/ESC-Z.log"
logmaxsize=256
```
2. 拷贝项目到/root，确认脚本所在位置为/root/ESC-Z/ESC-Z.sh

### 执行脚本
1. 给权限，直接运行，会在/root/ESC-Z生成一个日志文件ESC-Z.log
```sheel
chmod 755 /root/ESC-Z/ESC-Z.sh
/root/ESC-Z/ESC-Z.sh login
```
2. 查看ESC-Z.log（成功登录的话可以进行下一步）
```shell
cat /root/ESC-Z/ESC-Z.log
```

### 配置计划任务
1. 编辑crontab文件内容
```shell
crontab -e
```
2. 填入一下内容，表示每隔1分钟执行一次脚本（可以在[Cron在线表达式生成器](http://cron.ciding.cc/)s生成）
```
*/1 * * * * /bin/sh /root/ESC-Z/ESC-Z.sh login
```
3. 启动cron服务（有些系统是crond）
```shell
# 启动服务
service cron start
# 查看状态
service cron status
```
**到此为止，脚本会每分钟执行一次**

4. 注销（xxx:xxx:xxx:xxx是用户IP，建议自行实现获取本地网口ip，就不用带参数了）
```shell
/root/ESC-Z/ESC-Z.sh logout xxx:xxx:xxx:xxx
```

### 使用的建议
- 在工作日早上恢复网络后，定时重启路由器或重启wan口, 以刷新网络状态
```shell
# 重启名为wan的网口（自行查看设备的网口名称）
ifup wan
# 重启设备
reboot
```
- 对于一些工作日晚上断网，周末不断网的学校，自行配置crontab来计划执行脚本来适应断网
```shell
# 编辑crontab文件内容
# 每天从6:00到22:55，每隔5分钟执行一次
*/5 6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22 * * * /bin/sh /root/ESC-Z/ESC-Z.sh login
# 周五周六0:00到5:55，每隔5分钟执行一次
*/5 0,1,2,3,4,5 * * 5-6 /bin/sh /root/ESC-Z/ESC-Z.sh login
# 每天23:00至23:30，每隔5分钟执行一次
0,5,10,15,20,25,30 23 * * * /bin/sh /root/ESC-Z/ESC-Z.sh login
# 周五周六23:35至23:55，每隔5分钟执行一次
35,40,45,50,55 23 * * 5-6 /bin/sh /root/ESC-Z/ESC-Z.sh login
```


## 参考项目

致谢大佬们的项目
- https://github.com/OJZen/FckESC
- https://github.com/hzwjm/iNot-eclient
- https://github.com/6DDUU6/SchoolAuthentication
- https://github.com/OpenWyu/lua-esurfing-client


## 开源协议

[GPL-3.0](https://github.com/Z446C/ESC-Z/blob/main/LICENSE)


## 声明

严格遵守GPL-3.0开源协议，禁止任何个人或者公司将本代码投入商业使用，由此造成的后果和法律责任均与本人无关。
本项目只适用于学习交流，请勿商用！
