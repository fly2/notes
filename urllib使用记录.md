## urllib使用记录

### 运行代码后出现HTTPError: The HTTP server returned a redirect error that would lead to an infinite loop.

The last 30x error message was:

Moved Permanently

这是由于代码出现死循环导致，考虑是否for或其他逻辑循环是否正确。

