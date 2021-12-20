## python爬虫学习（一）request、re、csv豆瓣top250案例
 
思路：通过requests获取html前端代码，通过re进行正则匹配，最后存储进csv中

首先导入requests、re、csv

之后再定义一个爬取的方法函数，通过观察url请求发现参数有start和filter,发现传递的参数中以25作为基准，所以在这里定义一个方法用来传递参数

复制代码
def get_data(num):
    param = {
        "start": num,
        "filter": ""
    }

    url = "https://movie.douban.com/top250"
    header = {
        "User-Agent": "XXXXXXXXXXXXXXXXX"
    }
    res = requests.get(url = url,headers = header,params = param)
    page_content = res.text

    #解析数据
    obj = re.compile(r'<li>.*?<div class="item">.*?<span class="title">(?P<name>.*?)'
                     r'</span>.*?<p class="">.*?<br>(?P<year>.*?)'
                     r'&nbsp.*?<span class="rating_num" property="v:average">(?P<rate>.*?)'
                     r'</span>.*?<span>(?P<com>.*?)</span>',re.S)
    res = obj.finditer(page_content)
    f = open("doubantop250.csv",mode="a+",newline="")
    csv_writer = csv.writer(f)
    for i in res:
        #将数据存储字典中
        dic = i.groupdict()
        #单独处理year因为含有空格
        dic['year'] = dic['year'].strip()
        #print(i.group("name"))
        #print(i.group("year").strip())
        #print(i.group("rate"))
        #print(i.group("com"))
        print(dic.values())
        #将字典里的内容写入csv文件中
        csv_writer.writerow(dic.values())

    f.close()
复制代码
对于csv文件来说写入数据会出现一个空行，此时需要加上

newline=""
将数据存储进字典中需要通过

groupdict
对于文件的读写如果要累计添加内容需要用a+或a、w会覆盖

最后再执行循环遍历10次获取250个排行榜数据存储进csv文件中

def start():
    for test in range(10):
        get_data(test)
if __name__ == "__main__":
    start()
