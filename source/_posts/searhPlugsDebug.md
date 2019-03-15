---
title: 关于使用hexo框架站内搜索的一些坑
categories:
  - 技术
img: /img/successQuery.png
abbrlink: 55765
date: 2019-02-13 14:25:50
---

## 搜索功能不能使用，提示content.json文件找不到

一般出现这个错误的原因有两个：
### 1. 没有安装hexo-generator-json-content插件
**解决方法：**
进入<u>**博客的根目录**</u>，使用以下命令安装插件：
```
npm i hexo-generator-json-content -S
```
**注意**：这个插件一定要在博客的根目录中安装

### 2. _config.yml配置不正确
**解决方法：**
在博客根目录的_config.yml或者主题目录下的_config.yml添加相对应的配置：
```
## 搜索
jsonContent:
  searchLocal: true
  searchGoogle: false
  posts:
    title: true
    text: true
    content: true
    categories: false
    tags: false
```
**请注意：**
博主看过很多网上的帖子和博客都说一定要把以上的 jsonContent 配置在根目录的 `_config.yml`。然而亲测并非如此，写在主题目录的 `_config.yml`一样生效。但是，以上的 jsonContent 的配置仅供参考，实际中要对应博客中的search.js文件配置，否则浏览器依然会报错。

### 3. 博客的目录结构问题
这个问题可能会出现在 `search.js`文件中，博主在debug的时候首先发现浏览器报错找不到 `content.json`文件，但是如果按照前两点的方法解决之后在我们使用命令 `hexo g`之后，在 public 目录下会自动生成 `content.json`文件。那么原因很有可能是执行 `search.js`中出的错误，紧接着浏览源码的时候留意到在加载 `content.json`中的路径不正确:
```
function loadData(success) {
    if (!searchData) {
        var xhr = new XMLHttpRequest();
        xhr.open('GET', '/content.json', true);
        
        ...

```
**解决方法：**
1. 由于博主的博客是部署在子文件xxx/blog/下的，所以需将其改为：
```
function loadData(success) {
    if (!searchData) {
        var xhr = new XMLHttpRequest();
        xhr.open('GET', '/blog/content.json', true);
        
        ...
```
这样 `content.js`就可以顺利加载出来了。我们也会发现在浏览器预览的时候输入搜索条件也能正确显示搜索结果。
![images](/blog/img/successQuery.png)
但此时如果点击搜索结果并不能正确的进入搜索结果页面。原因是最后拼接路径没有修改过来，因此继续在 `search.js`文件中修改对应的 path 路径：
```
function render(data) {
    var html = '';
    if (data.length) {
        html = data.map(function(post) {
            return tpl(searchTpl, {
                title: filter(post.title, 'title'),
                path: 'blog/' + post.path,
                content: filter(post.text, 'content')
        ...
```
至此，本地搜索功能可以正常使用了。

2. 第二种解决的方案就是要把博客部署在根路径下，不要放在任何子文件夹下，只要和 `search.js`的路径对的上就没有问题。

