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

domain/ip/cidr-->web端口发现-->浅层指纹识别

url-->浅层指纹识别

开启深度扫描:

domain/ip/cidr-->web端口发现-->浅层指纹识别-->爬虫 && dir fuzz-->目录过滤与去重-->深度指纹识别

url-->初次指纹识别-->爬虫 && dir fuzz-->目录过滤与去重-->深度指纹识别

```
指纹库采用yaml格式进行解析, 需自行添加, 支持get,post,md5三种方式进行识别, 指纹库不公开, 具体格式见demo

web端口发现使用[hb](https://github.com/c26root/hb), 爬虫使用[rad](https://github.com/chaitin/rad), dir fuzz使用[ffuf](https://github.com/ffuf/ffuf)

支持ip/domain/CIDR输入
