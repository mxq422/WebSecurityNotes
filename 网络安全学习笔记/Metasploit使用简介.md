# metasploit使用简介

## 1、模块解析

* Auxiliaries（辅助模块）

  执行扫描、嗅探、指纹识别等相关功能。

* Exploit(漏洞利用模块)

  利用系统、应用或者服务中的安全漏洞进行攻击，流行的攻击技术包括缓冲区溢出、web应用程序攻击，以及利用配置错误等，包含攻击者或测试人员针对系统中的漏洞而设计的各种POC验证程序，用于破坏系统安全性的攻击代码，每个漏洞都有相应的攻击代码。

* Payload（攻击载荷模式）

  攻击载荷是我们期望目标系统在被渗透之后完成实际攻击功能的代码，成功渗透目标后，用于在目标系统上运行任意命令或执行特定代码，在框架中可自由选择、传送和植入。也可能是简单的在目标系统上执行命令，如添加账号等。

* Post（后期渗透模块）

  用于取得目标远程系统权限后，进行一系列的后渗透攻击动作，如获取敏感信息、实施跳板攻击等。

* Encoders（编码工具模块）

  负责免杀，以防止被杀毒软件、防火墙、IDS及类似的安全软件检测出来。

## 2、基本使用方法

进入metasploit后```./msfconsole ```，使用search语句进行搜索，比如搜索端口扫描工具，``` search oprtscan``` 搜索相关工具如图

![image-20230309222913891](https://gitee.com/mxq422/image/raw/master/img/image-20230309222913891.png)

name所列出的即为可用的工具名，Description即为工具的描述，根据描述去选择对应的工具，name中第一个名称即为模块名称，根据上述模块解释可进行归类。

确定工具后，使用use命令选择工具，``` use  auxiliary/scanner/portscan/tcp``` ，可观察到终端变化。

![image-20230309223247714](https://gitee.com/mxq422/image/raw/master/img/image-20230309223247714.png)

使用``` show options``` 命令查看命令需要设置的参数

![image-20230309223549696](https://gitee.com/mxq422/image/raw/master/img/image-20230309223549696.png)

Required标记为yes的参数必须包含实际的值，其中RHOSTS为待扫描地址IP，PORTS为扫描端口范围，THREADS为扫描线程，数量越高速度越快，可以根据实际使用情况用set命令进行设置，如设置IP信息```set RHOSTS 127.0.0.1``` （注意大小写），再次使用show命令查看可发现已完成修改 

![image-20230309224216607](https://gitee.com/mxq422/image/raw/master/img/image-20230309224216607.png)

也可使用unset命令取消某个参数的值

最后run即可开始运行工具。

如需要退出工具，使用exit命令即可完成退出。