import requests
import re
from lxml import etree

class TxtInfo:

    def __init__(self,url):
        self.url = url
        self.headers = {
'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.5005.62 Safari/537.36'
}

    # 获取列表页
    def list_info(self,re_patt):
        '''
        获取列表页上面的详情页地址
        :param re_patt: 正则，匹配详情页地址的正则
        :return: 将详情页地址返回
        '''
        response = requests.get(url=self.url, headers=self.headers)
        html_str = response.text
        href_list = re.findall(re_patt, html_str)
        return href_list

    # 获取详情页
    def detail_info(self,href_list,file):
        '''
        将每个章节的文章进行书写
        :param href_list: 详情页地址
        :param file: 文件操作句柄
        :return: None
        '''
        for each_href in href_list:
            detail_url = 'https://www.biqudu.net/' + each_href
            html_str = requests.get(url=detail_url, headers=self.headers).text
            # 将字符串，转换为html_doc文档
            html_doc = etree.HTML(html_str)
            # 提取文本内容
            content_str = ''.join(html_doc.xpath("//div[@id='content']/text()"))
            file.write(content_str)

     # 保存详情页文章
    def save_txt(self,file_path):
        '''
        保存txt文件用的，将操作句柄抛出
        :param file_path: 文章的名字
        :return: 文件操作句柄
        '''
        file = open(file_path,'a+')
        return file
    # 进行业务

    def info(self):
        # 打开列表页
        href_list = self.list_info('<dd> <a style="" href=" ">【  .*  】</a ></dd>')
        # 获取文件的操作句柄
        file = self.save_txt('凡人修仙传.txt')

        # 打开详情页,# 保存文章信息
        self.detail_info(href_list,file)

        # 关闭操作句柄
        file.close()


if __name__ == '__main__':
    ti = TxtInfo('https://www.biqudu.net/3_3881/')
    ti.info()
