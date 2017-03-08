#HTML
##创建HTML文档

- base 用做HTML文档中的相对URL解析基础的URL
- meta 添加对HTML文档所含数据的说明
- meta->http-equiv 文档的默认样式表或周期性地刷新页面内容
- async和defer 控制脚本的执行时机和执行方式
```html
<!DOCTYPE HTML>
<html>
  <head>
    <base href="http://titan/listings/"></base>
    <meta name="robots" content="noindex">
 <!-- noindex:不要索引本页；noarchive:不要将本页存档或缓存；nofollow:不要顺着本页中的链接继续搜索下去 -->
 
  </head>
  <body>
    <p>Hello Html!</p>
    <a href="page2.html">Page 2</a>
  </body>
</html>
```
- CSS
>指定样式使用的媒体media属性
```html
<style media="screen" type="text/css">
```
