# -Python 네이버 뉴스기사 작성일, 제목 , url크롤링(BeautifulSoup) 출처:https://arehoow.tistory.com/9

import requests
from bs4 import BeautifulSoup
import time

url = 'https://search.naver.com/search.naver?where=news&query=%EC%95%84%EB%8F%99%ED%95%99%EB%8C%80&sm=tab_opt&sort=0&photo=0&field=0&pd=3&ds=2021.01.09&de=2021.02.02&docid=&related=0&mynews=0&office_type=0&office_section_code=0&news_office_checked=&nso=so%3Ar%2Cp%3Afrom20210109to20210202&is_sug_officeid=0'

headers = {'user-agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36',
      'accept':'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9'}

response = requests.get(url, headers = headers)

soup = BeautifulSoup(response.text,'lxml')

soup.find_all('a', attrs = {'class':'news_tit'}) ... 기사 제목 가져오기

[title['title'] for title in soup.find_all('a', attrs = {'class':'news_tit'})] ... 제목만 가져오기

[url['href'] for url in soup.find_all('a', attrs = {'class' :'news_tit'})] ... url가져오기

soup.find_all('span', attrs={'class':'info'}) ... 날짜가져오기

dates = [date.get_text() for date in soup.find_all('span', attrs={'class':'info'})]


import re

date_list = []
for date in dates:
    if re.search(r'\d+.\d+.\d+', date) != None:
        date_list.append(date)
date_list ... 날짜만 가져오기(기사 작성일 정제)


데이터 프레임 만들기
start = 1
result_df = pd.DataFrame()

while True :
    try:
        url = 'https://search.naver.com/search.naver?&where=news&query=%EC%95%84%EB%8F%99%ED%95%99%EB%8C%80&sm=tab_pge&sort=0&photo=0&field=0&reporter_article=&pd=3&ds=2021.01.01&de=2021.01.09&docid=&nso=so:r,p:from20210101to20210109,a:all&mynews=0&cluster_rank=91&start={}&refresh_start=0'.format(start)
        headers = {'user-agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36',
          'accept':'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9'}
        response= requests.get(url, headers=headers)
        soup = BeautifulSoup(response.text, 'lxml')
        news_title = [title['title'] for title in soup.find_all('a',attrs={'class': 'news_tit'})]
        news_url = [url['href'] for url in soup.find_all('a', attrs= {'class' :'news_tit'})]
        dates = [date.get_text() for date in soup.find_all('span', attrs={'class':'info'})]
        news_date = []
        for date in dates:
            if re.search(r'\d+.\d+.\d+' , date) != None:
                news_date.append(date)
        df = pd.DataFrame({'기사작성일': news_date, '기사제목': news_title, '기사주소': news_url})
        result_df = pd.concat([result_df, df], ignore_index = True)
        
    except:
            print(start)
            break
