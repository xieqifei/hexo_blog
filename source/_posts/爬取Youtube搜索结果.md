---
title: 爬取Youtube搜索结果
top: false
cover: false
toc: true
mathjax: false
date: 2021-02-06 14:46:38
password:
summary:
tags: 
 - 爬虫
 - Python
categories: 爬虫
---

# 1：绪

最近在做一个音乐下载软件，通过搜索音乐名称，下载youtube上信息匹配度最高的视频，提取其中的音频，来获取音乐。开源软件youtube-dl已经完成了视频下载和音频提取的全部工作，我要做的就是，获取目标视频的id或者视频链接，然后调用youtube-dl下载。

# 2：接口

谷歌官方其实已经提供了获取YouTube搜索结果的api，直接调用即可。

```python
#调用谷歌api获取youtube搜索结果，返回匹配度最高的视频链接
def get_video_url_in_youtube_form_api(appKey,music_name):
    print("从google api获取数据")
    url = "https://youtube.googleapis.com/youtube/v3/search?q="+music_name+"&key="+appKey
    resp = requests.get(url)
    resp_json = resp.json()
    if resp.status_code ==200:
        return "https://www.youtube.com/watch?v="+resp_json['items'][0]['id']['videoId']
    else:
        return 500
```

但是，谷歌api是有调用上限的，也就是10000个单位，每次搜索将消耗100个单位。那么我们总共可以通过api获取搜索结果的次数就是100个。这对于个人来说，实际上已经足够了，但是，如果要向他人提供服务，就不够了。

那么就只能上手爬虫了。

# 3：爬取搜索结果

```python
#利用爬虫获取youtube搜索结果
def get_video_url_in_youtube_from_crawler(keyword):
    url="https://m.youtube.com/results?search_query="+keyword
    print("通过爬虫获取数据")
    resp = requests.get(url)
    if resp.status_code==200:
        result_json = re.findall(r'ytInitialData = (.*);</script>', resp.text)[0]
        result_obj = json.loads(result_json)
        try:
            video_url = "https://www.youtube.com/watch?v="+result_obj['contents']['twoColumnSearchResultsRenderer']['primaryContents']['sectionListRenderer']['contents'][0]['itemSectionRenderer']['contents'][0]['videoRenderer']['videoId']
        except KeyError:
            return 500
        return video_url
    else:
        return 500

```

通过url和搜索值，构造搜索链接。获取返回的html代码。对html格式化后，可以发现，有一串包含搜索结果的json数据，藏在一个js代码中间，通过正则，将它提取出来。

谷歌是有反爬虫的机制的，就是说，如果频繁通过上述程序请求数据，获取到的json不一定能获得想要的内容。这里需要使用Exception来避免报错，假如爬取失败，应当再调用api。如果有代理隧道，那么爬虫就更好了。