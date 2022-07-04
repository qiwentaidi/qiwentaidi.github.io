### python爬虫

[python爬虫视频链接](https://www.bilibili.com/video/BV1ZT4y1d7JM?p=1)

#### 1. Requests 

##### 	1.1 GET

###### 		1.1.1 基础介绍

```python
import requests
#请求头
headers = {
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
}
query = input("请输入你要搜索的内容")
#将url的某个参数作为变量去控制
url = f"https://www.sogou.com/web?query={query}"
#获取网页源码
html = requests.get(url,headers=headers)
#设置字符格式
response = html.content.decode("utf-8")
print(response)
#将进程关闭
html.close()
```

###### 		1.1.2 案例:抓取豆瓣电影喜剧排行前20排行榜

```python
import requests
headers = {
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
}
url = "https://movie.douban.com/j/chart/top_list"
#parmams是重新封装参数
params = {
    "type": "10",
    "interval_id": "100:90",
    "action": "",
    "start": 0,
    "limit": 20
}
res = requests.get(url,params=params,headers=headers)#params可以拼接URL
print(res.json())
res.close()
```

###### 1.1.3 案例 抓取fofa指定内容的URL链接

https://api.fofa.info/v1/search?q=app%3D%22eyoucms%22&qbase64=YXBwPSJleW91Y21zIg%3D%3D&full=false&pn=2&ps=20



##### 	1.2 Json

###### 		1.2.1 基础介绍

```
json----轻量级的文本数据交换格式
用法：{
json.dumps():对数据进行编码，将字典转换成字符串
json.loads():对数据进行解码，将字符串转为字典
}
```

###### 		1.2.2 案例

```python
data = {
    "no": 1,
    "name":"baidu",
    "url": "https://www.baidu.com"
}
json_str = json.dumps(data)
print(f"python原数据格式为：{data}")
print(f"json数据格式为：{json_str}")
```

##### 	1.3 POST

###### 		1.3.1 案例：百度翻译

```python
import requests
s = input("请输入你要翻译的英文单词")
url = "https://fanyi.baidu.com/sug"
data = {
    'kw':s
}
#以字典的方式放在data参数中去请求
response = requests.post(url,data=data)
print(response.json())
```

##### 	1.4 防盗链

###### 		1.4.1 案例：抓取梨视频源文件链接

```python
import requests
#部分链接通过改变参数来阻止爬虫直接获取链接
url = "https://www.pearvideo.com/video_1750583"
contId = url.split('_')[1]
video_statusURL = f'https://www.pearvideo.com/videoStatus.jsp?contId={contId}&mrd=0.05754613101901107'
#Referer字段是防盗链，溯源请求的上一级
headers = {
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36",
    "Referer": url
}
res = requests.get(video_statusURL,headers=headers)
#转换为json格式可从子节点逐级寻找
video = res.json()['videoInfo']['videos']['srcUrl']
systemtime = res.json()['systemTime']
video1 = video.replace(systemtime,f'cont-{contId}')
print(video1)
```

#### 2. Re-正则表达式

##### 	2.1 基础介绍

```
import re
#正则表达式测试网址：https://tool.oschina.net/regex/
#下列字符被称为元字符（默认只匹配一个字符）
{
.       匹配除换行符意外的其他任意字符
\w      匹配字母|数字|下划线|汉字
\s      匹配任意的空白符
\d      匹配数字
\n      匹配一个换行符
\t      匹配一个制表符
\b		代表单词边界
\r		代表回车，光标向下一行

^       匹配字符串的开始
$       匹配字符串的结尾
    
\W      匹配非字母|数字|下划线
\D      匹配非数字
\S      匹配非空白符
()      匹配括号内的表达式，也表示一个组
[...]   匹配字符组中的字符
[^...]  匹配除了字符组中字符的所有字符
}

#量词：控制元字符出现的次数
{
*       重复0次或以上
+       重复1次或以上
?       重复0次或1次
{n}     重复n次
{n,}    重复n次或以上
{n,m}   重复n到m次
}

#贪婪匹配和非贪婪匹配
.*      贪婪匹配，尽可能多的匹配
.*?     非贪婪匹配，尽可能少的匹配
```

```python
import re
text1 = "我的qq邮箱是1554089600@qq.com，你的邮箱是1554089600@163.com"
#1、compile()预加载正则
obj = re.compile("\d+.\w+.\w+")

#2、findall()匹配所有符合正则表达式的内容，返回的是列表
list1 = obj.findall(text1)
print(list1)

#3、finditer()匹配所有符合正则表达式的内容,返回的是迭代器，从迭代器中拿到内容需要.group()
it = obj.finditer(text1)
for i in it:
    print(i.group())

#4、search()匹配第一个符合正则表达式的内容,返回的是match对象，拿出内容需要.group()
s = obj.search(text1)
print(s.group())

#5、match()从头开始匹配符合的正则表达式，等同于^，拿出内容需要.group()，None时不能使用.group()
m = obj.match(text1)
print(m)
```

##### 	2.2 案例1：抓取福布斯中国排行榜

```python
import requests
import re

headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
}
url = "https://www.forbeschina.com/lists/1773"
html = requests.get(url,headers=headers)
res = html.content.decode('utf-8')
#(?P<分组名字>正则)给匹配正则的字符串命名，可以在后续的group()中取出
obj = re.compile('<td>(?P<a>.*?)</td>',re.S)#re.S能让.匹配换行符
it = obj.finditer(res)
for i in it:
    print(i.group('a'))
html.close()
```

##### 	2.3 案例2：获得豆瓣电影top250

```python
import requests
import re
url = "https://movie.douban.com/top250"
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
}
params = {
    "start":0,
    "filter":""
}
res = requests.get(url,params=params,headers=headers).content.decode('utf-8')
obj1 = re.compile('<li>.*?<img width="100" alt="(?P<moviename>.*?)" src=.*?(?P<year>\d+)&nbsp;.*?(?P<score>\d\W\d)</span>',re.S)
result1 = obj1.finditer(res)
for i in result1:
    print(f'电影：{i.group("moviename")},年份：{i.group("year")}，评分：{i.group("score")}')
```

##### 	2.4 案例3：获取电影天堂磁力链接

```python
import requests
import re
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
}
#正则匹配子页网址
obj = re.compile('<a href="(?P<urlspl>.*?)" class="ulink">')

#控制抓取页数
for i in range(1,2):
    url = f'https://www.dytt8.net/html/tv/hytv/list_71_{i}.html'

    res = requests.get(url,headers=headers).content.decode('gbk')
    #获取每个子页的网址
    result = obj.finditer(res)
    for it in result:
        #将子页网址进行拼接
        url_correct = f'https://www.dytt8.net/{it.group("urlspl")}'
        res = requests.get(url_correct, headers=headers).content.decode('gbk')
        #正则匹配磁力链接
        obj1 = re.compile('.mp4">(?P<download>.*?)</a></td>')
        result1 = obj1.finditer(res)
        for it1 in result1:
            print(it1.group('download'))
```

#### 3. Beautiful Soup

##### 	3.1 基础介绍

```python
from bs4 import BeautifulSoup
response = requests.get(url,headers=headers).content.decode('utf-8')
soup = BeautifulSoup(response,"lxml")#创建对象，lxml是bs4的指定解析器
#按照html标准的缩进格式的结构输出,且会自动补齐成对标签
print(soup.prettify())

findAll()以列表的形式显示所有符合的标签
用法：findAll(标签，属性=值，[数量]),
标签可以单个，多个时需加[]如['a','p'] 
```

##### 	3.2 案例：抓取qq音乐热歌榜,歌名、歌手、歌曲链接

```python
import requests
from bs4 import BeautifulSoup
url = "https://y.qq.com/n/ryqq/toplist/26"
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
}
response = requests.get(url,headers=headers).content.decode('utf-8')
soup = BeautifulSoup(response,"lxml")#创建对象，lxml是bs4的指定解析器
#匹配所有img且标签为属性class值为songlist__pic
m = soup.findAll("img",attrs={"class":"songlist__pic"})
n = soup.findAll("a",attrs={"class":"playlist__author"})
#遍历标签
for music in m:
    print(music.get('alt'))#获取具体某个属性的值
for singer in n:
    print(singer.get('title'))
print(f'歌曲：{m.get("alt")}，歌手：{n.get("title")}')
```

#### 4. Xpath

##### 	4.1 基础介绍

```python
#lxml的etree模块中才包含xpath解析
from lxml import etree
#xml声明只能在开头进行
xml = '''<?xml version="1.0" encoding="ISO-8859-1"?>
<bookstore>
    <book>
      <div>
      <title lang="eng">Harry Potter1</title>
      <price>29.99</price>
        <div>
        <title lang="eng">Harry Potter2</title>
        <price>29.99</price>
        </div>
        <span>
        <title lang="eng">Harry Potter3</title>
        <price>29.99</price>
        </span>
      </div>
    </book>
    <parent>
      <title lang="eng">Learning XML</title>
      <price>39.95</price>
    </parent>
</bookstore>
'''
xml = xml.encode('utf-8')#将文档设置编码格式
tree = etree.XML(xml)#创建对象
result = tree.xpath("book/div/title")#寻找book下的title子节点
result1 = tree.xpath("book/div/title/text()")#text()拿取文本
print(result1)
result2 = tree.xpath("book/div//title/text()")#//可以拿取父节点下所有符合的子节点，子节点的子节点...
print(result2)
result3 = tree.xpath("//title/text()")
print(result3)
```

##### 	4.2 案例：获取浙理工某网站的所有a标签的herf的属性值

```python
import requests
from lxml import etree
url = "https://sky.zstu.edu.cn/sy.htm"
res = requests.get(url).content.decode('utf-8')
tree = etree.HTML(res)
#抓取网页中a标签的所有a标签的href值
url_link = tree.xpath('//a/@href')#@xxx代表属性值
print(url_link)
#在网页对应内容的源码中右键复制xpath可直接获得当前内容的xpath！！
```

#### 5. 多线程

##### 	5.1 基础介绍

```python
from threading import Thread
def func():
    for i in range(1000):
        print(f'func{i}')


if __name__=='__main__':
    t = Thread(target=func)#创建线程对象
    t.start()#将线程状态设置为开启
    for i in range(1000):
        print(f'main{i}')
#运行时可以观察到func和main函数是同时打印
```

##### 	5.2 线程池

###### 		5.2.1 案例：获取整本斗破苍穹小说

```python
import requests
from lxml import etree
from concurrent.futures import ThreadPoolExecutor#导入线程池
headers = {
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.99 Safari/537.36'
}

def getbook(url,page):#先写抓取某一页的函数
    res = requests.get(url,headers=headers).content.decode('utf-8')
    tree = etree.HTML(res)
    content = tree.xpath('//*[@id="content"]/p/text()')
    novel = str(content).replace("'","").replace("[","").replace("]","").replace(" ","").replace(",","")
    f = open(f'e:\\test\\{page}.txt',mode='w',encoding='utf-8')
    f.write(novel)
    f.close()


if __name__ == '__main__':
    # for i in range(1,2):                    #效率低下
    #     url = f'http://www.doupo321.com/doupocangqiong/{i}.html'
    #     getbook(url,page=i)
    with ThreadPoolExecutor(50) as t:#开辟50个线程为线程池
        for i in range(1,200):#获取200章
            t.submit(getbook,url=f'http://www.doupo321.com/doupocangqiong/{i}.html',page=i)
    print("done")

```

###### 		5.2.2 案例：抓取美剧网勿言推理视频的第一集

[勿言推理第一集]: http://www.meijuwang.cc/w/wuyantuili-1-1.html	"视频链接"

------

- [x] #1、找到m3u8文件
- [x] #2、通过m3u8下载到ts文件
- [x] #3、获取密钥进行解密ts文件
- [x] #4、把ts文件合并为一个mp4文件

```python
import requests
import re
#1、查看页面源码找到视频信息，用正则将需要的视频框架取出
def iframe(url):
    resp = requests.get(url, headers=headers)
    obj = re.compile(r'"link_pre":"","url":"(?P<url>.*?)","url_next', re.S)
    result = obj.search(resp.text).group('url')
    result = result.replace("\\", "")
    resp.close()
    return result

#2、找到m3u8文件正确的下载地址
def m3u8(url):
    resp = requests.get(url,headers=headers).content.decode('utf-8')
    spl_url = url.split('/share')[0]
    obj = re.compile(r'url":"(?P<m3u8_link>.*?)"}]',re.S)
    result = obj.search(resp).group('m3u8_link')
    resp.close()
    return f'{spl_url}{result}'

#3、获取m3u8
def get_m3u8(url):
    # 下载m3u8文件，执行完一次后注释
    resp = requests.get(url, headers=headers)
    f = open('勿言推理1.m3u8', mode='wb')
    f.write(resp.content)
    resp.close()
    f.close()

if __name__ == '__main__':
    url = 'http://www.meijuwang.cc/w/wuyantuili-1-1.html'
    if_url = iframe(url=url)
    m3u8_link = m3u8(url=if_url)
    get_m3u8(url=m3u8_link)
```

```python
#4、下载ts文件
import requests
ts_line = []
def download_ts():#下载加密的ts片段
    f = open('勿言推理1.m3u8',mode='r')
    lines = f.readlines()
    for line in lines:
        if line.startswith('#') is False:
            line = line.strip()
            ts_line.append(line)
    f.close()
    for i in range(len(ts_line)):
        resp = requests.get(url=ts_line[i])
        f1 = open(f'video\\{i}.ts',mode='wb')
        f1.write(resp.content)
        f1.close()
        
if __name__ == '__main__':
    download_ts()
```

```python
#5、获取key并解密ts文件
from Crypto.Cipher import AES#找到python3的安装目录下的Lib—-site-package将crypto文件夹改名为Crypto
def get_key():
    obj = re.compile('URI="(?P<key>.*?)"')
    f = open('勿言推理1.m3u8',mode='r')
    lines = f.read()
    keys = obj.search(lines).group('key')
    resp = requests.get(url=keys).text
    f.close()
    return resp

def dec_ts(key):#将所有片段进行解密
    aes = AES.new(key=key.encode('utf-8'), IV=b'0000000000000000', mode=AES.MODE_CBC)
    for i in range(len(ts_line)):
        f = open(f'video\\{i}.ts', mode='rb')
        f1 = open(f'video2\\{i}.ts', mode='wb')
        no_dec = f.read()
        f1.write(aes.decrypt(no_dec))
        f.close()
        f1.close()

if __name__ == '__main__':
    key1 = get_key()
    dec_ts(key=key1)
```

```python
#6、将所有ts文件合并为一个mp4视频
import os

if __name__ == '__main__':
    os.system(f'copy /b e:\\python\\爬虫\\5、多线程\\video2\\* e:\\movie.mp4')#将e:\\python\\爬虫\\5、多线程\\video2\\该目录下的所有视频合并为e盘下的movie.mp4
```

#### 6. Selenium

##### 6.1 基础介绍

```
优点：
1、使用更加方便，不用处理信息解密步骤以及框架嵌套等
selenium的缺点：
1、如果网站加载过慢会报错，需要借助time.sleep()函数来延迟程序执行
2、浏览器有受自动化测试工具控制的标识，有些网站会以此为标准做反爬


环境搭建
1、下载浏览器驱动：http://chromedriver.storage.googleapis.com/index.html  对应的浏览器版本对不上就找最近的一个
2、把解压缩的浏览器驱动放到python解释器下，pycharm右键运行，下面第一行有解释器所在目录
```

###### 6.1.1 案例：抓取拉勾网职位信息

```python
from selenium.webdriver import Chrome
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
import time
web = Chrome()#1、创建浏览器对象
web.get('https://www.lagou.com/')#2、打开浏览器
time.sleep(1)
el = web.find_element(By.XPATH,'//*[@id="changeCityBox"]/ul/li[1]/a')#新语法，找到按钮的xpath
el.click()#鼠标单击事件
time.sleep(1)
web.find_element(By.XPATH,'//*[@id="search_input"]').send_keys("python",Keys.ENTER)#在文本框输入python并回车
time.sleep(1)

jobs = web.find_elements(By.CLASS_NAME,'item__10RTO')#检索类名为item__10RTO的块
for job in jobs:
    job_name = job.find_element(By.TAG_NAME,'a').text#遍历工作名称
    pay = job.find_element(By.CLASS_NAME,'money__3Lkgq').text#遍历工作薪资
    company = job.find_element(By.CLASS_NAME,'company-name__2-SjF').text#遍历公司名称
    print(f'岗位名称：{job_name}，工资为：{pay}，公司名称：{company}')
```

###### 6.1.2 窗口切换

```python
from selenium.webdriver import Chrome
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
import time
web = Chrome()


#窗口切换
web.get('https://www.lagou.com/')
time.sleep(1)
web.find_element(By.XPATH,'//*[@id="changeCityBox"]/ul/li[3]/a').click()
time.sleep(1)
web.find_element(By.XPATH,'//*[@id="search_input"]').send_keys('python',Keys.ENTER)
time.sleep(1)
web.find_element(By.XPATH,'//*[@id="jobList"]/div[1]/div[2]/div[1]/div[1]/div[1]/a').click()
#在selenium眼中，新窗口是不会切换过来的
web.switch_to.window(web.window_handles[-1])#切换语句

------------------------------------------------------------------------------------------------------------

#iframe之间的切换
web.get('http://www.meijuwang.cc/w/zuozuomuyugongye-1-1.html')
time.sleep(1)
iframe = web.find_element(By.XPATH,'//*[@id="playleft"]/iframe')#获取iframe定位
web.switch_to.frame(iframe)#将窗口视角切换到iframe
time.sleep(1)
#web.switch_to.default_content()#切换回默认的主界面
tx = web.find_element(By.XPATH,'//*[@id="mvideo"]/div[6]/div[1]/a').text
```

#### 6.2 案例：爬取fofa使用eyoucms的网站的前10页网址

```python
import json
from selenium.webdriver import Chrome
import re
import time

def get_link(i):
    web = Chrome()
    web.get('https://fofa.info/')
    time.sleep(3)
    # listCookies = web.get_cookies()  #是由cookie组成的列表。单个的cookie是字典组成的，所有get_cookies()返回值是由字典组成的列表。
    # jsonCookies = json.dumps(listCookies)  #dumps是将dict转化成str格式
    # f1 = open('1.json','w',encoding='utf-8')
    # f1.write(jsonCookies)
    # f1.close()
    f = open('1.json', 'r', encoding='utf-8')
    dictCookies = json.loads(f.read())     #loads是将str转化成dict格式
    f.close()
    for cookie in dictCookies:
        web.add_cookie(cookie)


    url = "https://fofa.info/result?qbase64="
    base64 = "YXBwPSJleW91Y21zIg=="             #此处修改为查找语句的base64编码格式
    web.get(f'{url}{base64}&page={i}&page_size=20')
    source = web.page_source
    obj = re.compile('href="(?P<link>.*?)" target="_blank">')
    result = obj.finditer(source)
    for i in result:
        print(i.group('link'))
    web.close()


if __name__ == '__main__':
    for j in range(1,11):
        get_link(i=j)

```

#### . Scrapy

```
- scrapy框架的基本使用：
	- 安装环境：
 		 - windows：
    		pip install scrapy
	- 创建工程：
		scrapy startproject baidu(工程名称)
	- 进入工程文件、创建爬虫文件
  		scrapy genspider spider(爬虫文件名称) www.baidu.com（网址链接）
	- 执行工程：
  		scrapy crawl spider(爬虫文件名称)
```

##### 7.1 setting.py文件设置

​		设置ROBOTSTXT_OBEY = False`，默认为True遵循robots.txt协议会影响数据爬取

​		增加`LOG_LEVEL = ‘ERROR’`，显示指定类型的错误信息

​		设置UA伪装，将注释删除并增加自己的UA

![image-20220224145426711](C:\Users\huyubing\AppData\Roaming\Typora\typora-user-images\image-20220224145426711.png)

```python
import scrapy

class SpiderSpider(scrapy.Spider):
    #爬虫源文件的名称
    name = 'spider'
    #允许的域名：用来限定起始URL列表中哪些URL可以进行请求发送
    #allowed_domains = ['www.baidu.com']正常使用可以注释
    #起始的URL列表：该列表中的URL都回被scrapy自动进行请求发送
    start_urls = ['http://www.baidu.com/']

    #用作于数据解析：response参数代表响应数据包
    def parse(self, response):
        print(response)
```

执行以上内容的工程文件会得到

`<200 http://www.baidu.com/>`

##### 7.2 数据解析

```python
import scrapy

class WjhpSpider(scrapy.Spider):
    name = 'wjhp'
    #allowed_domains = ['www.xxx.com']
    start_urls = ['https://baijiahao.baidu.com/s?id=1725625149536159436&wfr=spider&for=pc']
    def parse(self, response):
       sjhp = response.xpath('//*[@id="ssr-content"]/div[2]/div[1]/div/div[1]/text()')
       print(sjhp)
```

返回的结果是，slelector对象，可以调用extract()来获取data里的字符串内容，返回的是列表

`[<Selector xpath='//*[@id="ssr-content"]/div[2]/div[1]/div/div[1]/text()' data='乌克兰总统发推：世界必须迫使俄罗斯实现和平'>]`

##### 7.3 持久化存储

```
- 基于终端指令：
	- 要求：只可以将parse方法的返回值存储到本地的文本文件中
	- 注意：文件后缀名只可以是json、csv、xml
	- 指令：scrapy crawl xxx -0 filepath
	- 缺点：局限性强
- 基于管道：
	-
```



###### 	7.3.1 基于终端指令

在数据解析的parse函数下去掉print增加以下代码

```
list_all = []
sjhp = response.xpath('//*[@id="ssr-content"]/div[2]/div[1]/div/div[1]/text()').extract()
dic = {
    'sjhp':sjhp
}
list_all.append(dic)
return list_all
```

使用-o指令保存在本地

`scrapy crawl wjhp -o ./sjhp.csv`
