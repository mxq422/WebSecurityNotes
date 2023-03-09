# burpsuite使用简介

> burpsuite是一款集成化的渗透测试工具，在渗透测试中使用的十分频繁，本文主要记录其内部模块，不包含安装，安装教程在网络上很多，可自行进行查阅。

## 1.使用前准备

burp suite代理工具是以拦截代理的方式，拦截所有通过代理的流量，并以中间人的方式对请求和返回的信息做各种处理，并达到测试的目的，通常与火狐（firefox)浏览器配合比较方便使用。

![image-20230308101213785](https://gitee.com/mxq422/image/raw/master/img/image-20230308101213785.png)

其默认本地代理端口为8080，可在设置中进行查看。

在使用过程中，我们通过设置浏览器代理拦截流量，并通过burpsuite进行处理，火狐设置代理方式如下：

右上角打开菜单，单击设置→常规设置→网络设置，进行手动配置，如图。

![image-20230308101706763](https://gitee.com/mxq422/image/raw/master/img/image-20230308101706763.png)

也可以使用浏览器插件进行代理切换，推荐的插件为FoxyProxy，按上述地址和端口添加即可。

在设置进行字体修改，如图，避免出现乱码问题。

![image-20230308102143689](https://gitee.com/mxq422/image/raw/master/img/image-20230308102143689.png)

## 2.proxy

> 是利用burp开展测试流程的核心，通过代理模式，我们拦截，查看，修改数据

![image-20230308102304456](https://gitee.com/mxq422/image/raw/master/img/image-20230308102304456.png)

* intercept选项卡
  * forward 将拦截的数据包或修改后的数据包发送至服务器端
  * drop将当前拦截的数据包丢弃
  * intercept is off/on 关闭/开启拦截功能
  * action 将数据包进一步发送到repeater/intruder/sacnner等功能组件进行进一步测试，同时也包含改变数据包请求方式及其body编码等功能
* burp suite拦截的消息中可查看请求的实体内容，消息头，请求参数等信息。
  * raw，web请求的raw格式，以纯文本形式显示数据包，包含请求地址，http协议版本，主机头，浏览器信息，cookie等可以手动修改并进行发送。
  * hex 对应的是raw中信息的二进制内容，可以进行修改，在进行00截断时非常好用。

![image-20230308105018779](https://gitee.com/mxq422/image/raw/master/img/image-20230308105018779.png)

## 3.intruder

> 爆破选项，可以对web应用程序进行自动化攻击，如通过枚举用户名、密码，SQL注入，目录遍历等。在原始请i去的基础上，通过修改参数获取不懂的请求应答，通过对比数据进行分析。

![image-20230308112759196](https://gitee.com/mxq422/image/raw/master/img/image-20230308112759196.png)

* positions

抓包后使用action，将包发送到intruder模块，此时会自动$$选中参数，$$中间极为后续需要替换的参数，可先clear全部清除后在选择需要的参数单机add进行添加。

![image-20230308111424513](https://gitee.com/mxq422/image/raw/master/img/image-20230308111424513.png)

完成参数设定后，在上方选择模式。

![image-20230308111119311](https://gitee.com/mxq422/image/raw/master/img/image-20230308111119311.png)

Sniper --  一个字典列表匹配一个目标参数，通过使用一组Payload，依次替换目标参数位置（从左到右，从上至下），一个变量替换完成，再接着替换另一个变量，例如：针对用户名不变，爆破密码的场景

Battering ram -- 一个字典列表匹配多个目标参数，通过使用一组Payload集合，依次替换，跟Sniper的区别就是如果有多个参数，Battering ram会每个参数都同时去匹配，例如：用户名不变，同时爆破密码和验证码

Pitchfork -- 使用多个字典列表分别取这些字典列表中的一个值，然后取匹配对应数量目标参数，简单点理解就是使用两个密码字典取匹配目标两个位置，例如：使用两个密码字典匹配目标站点的密码和验证码（用户名，密码字典一一对应）

Cluster Bomb -- 使用多个字典列表，将这多个字典列表进行排列组合然后匹配目标多个参数值，不是一一对应，尝试所有可能进行遍历，这种情况匹配事件很长，概率更高。

* payloads

![image-20230308113958319](https://gitee.com/mxq422/image/raw/master/img/image-20230308113958319.png)

 1.Simple list（简单列表）

​	最基本的，可以使用add添加，也可以上传文件进行添加

2.Runtime file（运行时文件）

​	指定文件，每一行一个payload。simple会显示出来文件内容，这个不显示。

3.Custom iterator（自定义迭代器）

​	有8个站位，每一个站位可以指定简单列表payload的类型，然后根据站位的多少，和每一个简单列表的payload进行排列组合生成最终的payload，例如：有个参数值类型是username@@@password，那么就要设置3给站位

4.Character substitution（字符串替换）

​	根据预先定义好的字符串替换的规则，在payload执行的过程中按照规则匹配替换

5.Case modification（大小写替换）

​	根据预定义的大小写替换规则，在payload执行的过程中按照规则匹配替换，这期间经过规则生成的新payload是从上到下的顺序，依次是：No change(不改变使用原来的字符串)，To lower（转换为小写），To Upper case（转换为大写），To Propername（首字母大写，其他小写），To ProperName（首字母大写，其他不变）

6.Recursive grep（递归grep）

​	是从上一次的响应结果中，取得部分特定内容作为下次请求的Payload。

7.Illegal Unicode（非法的Unicode编码）

​	在进行攻击的时候指定payload里面加入非法的Unicode编码，功能选项不太好理解

8.Character blocks（字符块）-- 可以理解为字符长度

​	根据要求给出的一个字符串，然后指定设置产生指定大小的字符块，最终的结果展示为生成指定长度的字符串，一般用在边界测试或者缓冲区溢出中

9.Number（数字类型）

​	数字范围：根据要求生成序列数字，随机数字

​	数字格式：

​	十进制：

​	Min integer digits -> 最小的整数个数有几个，简单点理解就是小数点前面最少有几位数

​	Max integer didits -> 最大的整数个数有几个，简单点理解就是小数点前面最多有几位数

10.Date（日期类型）

​	根据要求生成一序列各种格式的日期

11.Brute Forcer（暴力破解）

​	生成一个包含特定字符集的排列组合，一般都是用来穷尽账号密码用途

12.Null payloads（空payload）

​	在每个请求中添加null，实际查看就是没有更改原始请求重复发送给服务端，这个就是等到你遇到需要的场景就会知道意义了，通常可能是做观察与搜集数据的时候用，比如要确认一直重复发起一样的请求，是否[服务器](https://cloud.tencent.com/product/cvm?from=10680)都会给一样的响应。

13.Character frobber（字符frobber）

​	依次修改字符串中每个字符位置的值，每次都是在原来的字符串上递增一个该字符的ASCII码，通常使用的场景是目标系统使用了负载的会话令牌用来跟踪会话，如果修改了其中单个字符的值，不影响会话的下一步处理

14.Bit flipper（Bit翻转）

​	对预设的payload原始值按照比特位的形式依次修改

15.Username generator（用户名生成器）

​	主要用于用户名和电子邮件账号的自动生成

16.ECB block shuffler（ECB加密块洗牌）

​	基于ECB加密模式的Payload生成器

17.Extension-generated（payload扩展插件生成）

​	基于Burp插件来生成Payload值，因此使用前必须安装配置Burp插件，在插件里注册一个Intruder payload生成器，供此处调用，这里测试安装的插件是Bradamsa

18.Copy other payload（复制其他的payload）

​	这种类型的Payload是将其他位置的参数复制到Payload位置上，作为新的Payload值，通常适用于多个参数的请求消息中，它的使用场景可能是： 1.两个不同的参数需要使用相同的值，比如说，用户注册时，密码设置会输入两遍，其值也完全一样，可以使用此Payload类型。 2.在一次请求中，一个参数的值是基于另一个参数的值在前端通过脚本来生成的值，可以使用此Payload类型