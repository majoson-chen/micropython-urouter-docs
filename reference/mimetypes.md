# mimetypes
`mimetype` 被用于告知浏览器如何处理被发送的信息, 如果在响应之后浏览器没有做出正确的显示或处理, 尝试检查 `mimetype` 是否被正确的设置.

## functions:

### get_mime_type()
此函数用于尝试获取与文件后缀名配对的 `mime_type`

params:
- suf: str  
  文件后缀名(包括`.`, 例如: `.jpg`)

- default: str = "application/octet-stream"
  查询失败时用于代替的返回文本, 默认为 `application/octet-stream`, 此类型会被浏览器识别为下载动作.

**目前内置的可用项**:
```python
{
    ".txt": "text/plain",
    ".htm": "text/html",
    ".html": "text/html",
    ".css": "text/css",
    ".csv": "text/csv",
    ".js": "application/javascript",
    ".xml": "application/xml",
    ".xhtml": "application/xhtml+xml",
    ".json": "application/json",
    ".zip": "application/zip",
    ".pdf": "application/pdf",
    ".ts": "application/typescript",
    ".woff": "font/woff",
    ".woff2": "font/woff2",
    ".ttf": "font/ttf",
    ".otf": "font/otf",
    ".jpg": "image/jpeg",
    ".jpeg": "image/jpeg",
    ".png": "image/png",
    ".gif": "image/gif",
    ".svg": "image/svg+xml",
    ".ico": "image/x-icon"
    # others : "application/octet-stream"
}
```
在以后的开发中, 我们也许会尝试加入更加先进和齐全的获取方式用于加快处理速度和减轻内存负担.