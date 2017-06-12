## urllib使用记录

### urllib.request.urlopen的返回结果问题

```python
#当使用urllib.request.urlopen(url)时，返回的是字符串，可以直接用decode('utf-8')进行解码
page=urllib.request.urlopen(url,timeout=10)
html = page.read() #read()方法用于读取URL上的数据
html=html.decode('utf-8')

#当使用urllib.request.urlopen(request)时，返回为为gzip类型（压缩包），需要使用先解压后才能解码
import zlib
#import gzip
#from io import BytesIO
request = urllib.request.Request(url,headers=header)
page=urllib.request.urlopen(request,timeout=10)#urllib.urlopen()方法用于打开一个URL地址
html = zlib.decompress(page.read(), 16+zlib.MAX_WBITS)
#buff = BytesIO(page.read()) # 把content转为文件对象
#html = gzip.GzipFile(fileobj=buff)
html=html.decode('utf-8')


```

### 运行代码后出现HTTPError: The HTTP server returned a redirect error that would lead to an infinite loop.

The last 30x error message was:

Moved Permanently

这是由于代码出现死循环导致，考虑是否for或其他逻辑循环是否正确。

