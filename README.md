- 本人github的项目可能目前主要分为两大类，🐸Frog系列为自动化扫描方向，🐶Doge系列为免杀及内网渗透方向

- 本系列命名为Frog可能是因为这种生物的寿命长 🐸 🤓 +1s 

- Frog-Fp为Frog系列第三个项目🐸，写的有点累了

## 序
```
1.关于自动化的设计，难点在于经验如何转化为程序的逻辑

2.大规模攻防对抗初期拼的是"以漏洞找资产的能力"

3.个人能力有限，精力有限，此项目的完成过程中非常纠结
```
# 🐸Frog-Fp

Frog-Fp批量深度指纹识别，采用python3实现，具体实现流程如下所示：

```
不开启深度扫描:
input: domain/ip/cidr-->web端口发现-->浅层指纹识别
input: url-->浅层指纹识别

开启深度扫描:
input: domain/ip/cidr-->web端口发现-->浅层指纹识别-->爬虫 && dir fuzz-->目录过滤与去重-->深度指纹识别
input: url-->初次指纹识别-->爬虫 && dir fuzz-->目录过滤与去重-->深度指纹识别
```
指纹库采用yaml格式进行解析, 需自行添加, 支持get,post,md5三种方式进行识别, 指纹库不公开, 具体格式见fingerprint内的demo文件

web端口发现使用[c26root/hb](https://github.com/c26root/hb), 爬虫使用[chaitin/rad](https://github.com/chaitin/rad), 目录fuzz使用[ffuf/ffuf](https://github.com/ffuf/ffuf)

支持ip/domain/CIDR输入

## about

在大规模红蓝对抗中，不触发waf拦截的指纹识别是比较好的自动化切入点。

尤其是在手上有了一部分0day后，深度的指纹识别框架能提升"以漏洞找资产的能力"。

此框架的构思基于个人的一部分打点经验，有很多不足之处，开源是希望大家一起完善起来。

对于我个人来说，我是不太想公开指纹库的，这会增加被waf拦截的可能性

但如果您能对本项目进行核心代码贡献/修复被认可，或是可以贡献超过5个指纹yaml并被认可，即可共享目前的指纹库。

## Usage
若在linux下使用，请给 rad_linux/ffuf_linux/hb_linux三个文件可执行权限

并且执行pip install安装依赖
```
python3 -m pip install requirements.txt
```
将指纹库yaml文件放入fingerprint文件夹内，执行check.py可进行yaml格式检查。

## 常用命令
```
python3 Frog-Fp.py win/linux -tL urls.txt
python3 Frog-Fp.py win/linux -dL ips.txt
```
输入的文本按行划分，支持ip/domain/CIDR格式

## yaml格式解析

GET,POST:
```
#name为yaml的标题，请以fp开头，以请求方法结尾，中间以-连接
name: fp-demo-get

#method目前支持GET,POST
method: GET

#目录采用list格式，可写多个
path: ["/","/login"]

#Header采用dict格式，可写入""留空，即采用chrome默认header
Header: {"Accept-Language":"zh-CN", "Content-Type": "text/javascript"}

#Cookie采用;分隔多个key=value，中间不要有空格
Cookie: key1=value1;key2=value2

#follow_redirects可赋值True或False，记得第一位字母大写
follow_redirects: True

#expression表达式采用python语句直接执行，requests请求的返回值为r
expression: |
      r.status_code == 200 and ('test' in str(r.headers) or 'test1' in r.content.decode())

#若请求方法为POST可加入data字段,格式为string
data: ''

```

MD5:
```
#name为yaml的标题，请以fp开头，以md5结尾
name: fp-demo-md5

#md5_path为文件路径，list格式，与md5的list一一对应
md5_path: ["/test.ico","/favicon.ico"]

#md5为对于path的md5值，与md5_path一一对应
md5: ["18b786ca7913a58cb8463f1a5feca293","ffaadddssa7913a58cb8463f1a5feca2"]

```

## config.py

```
#指纹识别代理(去掉井号)(dirfuzz,爬虫代理暂不支持)
proxies = {
	#"http":"http://127.0.0.1:8080",
	#"https":"http://127.0.0.1:8080",
}

#目录fuzz递归深度
recursion = 2

#目录fuzz超时(s)
fuzztime = 5

#指纹识别超时(s)
timeout = 5

#单个目标爬虫超时(s)
crawler_timeout = 600

#指纹识别线程数
threads = 100

#目录fuzz线程数
fuzz_t = 20

#web端口扫描超时
hb_time = 5

#端口扫描线程数
hb_t = 200

#端口扫描端口list
ports = "80,81,443,444,8000,8080,8088,8880,8443,18080,18443"

#仅基础扫描,此配置为True就不会进行爬虫与目录fuzz
only_basic = True

```
