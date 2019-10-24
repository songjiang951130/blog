# 安装

```
//下载主题 next
git clone https://github.com/theme-next/hexo-theme-next themes/next
//下载无后端的评论系统 valine
npm install leancloud-storage --save
npm install valine --save
npm install hexo-leancloud-counter-security --save
npm install babel-runtime --save
//订阅配置
npm install hexo-generator-feed --save
//本地搜索安装
npm install hexo-generator-search --save
//站点地图
npm install hexo-generator-sitemap --save
npm install hexo-generator-tag --save
npm install hexo-generator-category --save
//移除默认的markdown
npm uninstall --save hexo-renderer-marked
npm install hexo-renderer-markdown-it-plus
```
# 运行
```
hexo server
```

# 部署
```
hexo clean
hexo generate
hexo deploy
```

# [查看google索引效果](https://search.google.com/search-console?resource_id=https%3A%2F%2Fsongjiang951130.github.io%2F)