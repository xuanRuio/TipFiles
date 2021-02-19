# Spider

---

```python
import requests
from lxml import etree
import threading
import queue

# 获取网页源数据，返回element object
def get_page(url)：
	headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36"
    }
    try:
        respone = requests.get(url=url,headers=headers).text
        element_obj = etree.HTML(respone)
    except:
        return None
    return element_obj
# 进行网页数据解析，返回数据任务队列q
def params_page(ele_obj)
	titles = ele_obj.xpath('')
    links = ele_obj.xpath('')
    ...
    if links:
        # 构造任务队列
        q = queue.Queue()
    	data = zip(titles,links,...)
        # 压数据进队
        for i in data:
            q.put(i)
        return q
    return None
# 进行数据存储
def store_data(qobj):
    while not qobj.empty():
        data = qobj.get()
        print(f'正在下载:{item[0]}')
        with open(path, 'wb+') as obf:
            obf.write(requests.get(url=data[1], headers={"User-Agent": "Mozilla/5.0 (Windows 								NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) 								Chrome/86.0.4240.183Safari/537.36"}).content)
        print(f'任务下载完成:{item[0]}')
if __name__ == "__main__":
    url = ''
    elobj = get_page()
    if elobj:
    	qobj = params_page
        if qobj:
    		threads = []
            # 构造多线程 20个
    		for th in range(20):
        		t = threading.Thread(target=stroge_video, args=(li,))
        		t.start()
        		threads.append(t)
    		for thread in threads:
        		thread.join()
         else:
             print("网页数据解析失败")
    else:
        print("网站请求错误")
```

---

### 网页乱码问题

```python
print(res.encoding)
print(res.headers['content-type'])
print(res.apparent_encoding)
print(requests.utils.get_encodings_from_content(res.text))
```

