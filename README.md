# 111
111
import requests
from bs4 import BeautifulSoup
import pandas as pd

pd.set_option('display.max_columns', None)  #解决表格多列时中间省略显示问题
pd.set_option('display.max_rows', None)    #解决表格多行时中间省略显示问题

#获取网面源代码
def getHTMLText(url):
    try:
        headers = {'User-Agent':"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36"}
        r = requests.get(url, timeout=30, headers=headers,allow_redirects=False)
        print(r.status_code)
        r.raise_for_status()  # 如果状态不是200，引发HTTPError异常
        r.encoding = r.apparent_encoding
        #print(r.text)
        return r.text
    except:
        print("网页访问异常！")

#bs4提取信息
def parsePage(html):
    soup = BeautifulSoup(html, 'html.parser')
    tables = soup.select('table') #选择表格数据
    df_list = []
    for table in tables:
        df_list.append(pd.concat(pd.read_html(table.prettify()))) #pd.read_html()
    df = pd.concat(df_list)
    return df

#保存数据为CSV文件
def saveCSV(pages):
    for i in range(pages):
        df.to_csv("cas_data_{}.csv".format(i), encoding='utf_8_sig')

#构造网页请求的地址
def get_urls(pages):
    urls = ['https://www.chinabond.com.cn/cb/cn/xxpl/ywgg/tgywgg/20230129/161991420.shtml']
    for i in range(1,pages):
        page = i*100
        url = "https://www.chinabond.com.cn/cb/cn/xxpl/ywgg/tgywgg/20230129/161991420.shtml".format(page)
        urls.append(url)
    return urls

pages = 2    #输入需要获取的页数

#主函数，批量获取表格数据
urls = get_urls(pages)
for url in urls:
    print(url)
    html = getHTMLText(url)  #获得网页源代码
    df = parsePage(html)  #解析提取网页信息
    print(df)
    saveCSV(pages)  #保存到指定路径 
