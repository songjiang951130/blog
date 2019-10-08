# 安装

```
//下载主题 next
git clone https://github.com/theme-next/hexo-theme-next themes/next
//下载无后端的评论系统 valine
npm install leancloud-storage --save
npm install valine --save
npm install hexo-leancloud-counter-security --save
npm install babel-runtime --save
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