---
title: 使用hexo+github搭建个人博客过程中的一些坑
date: 2020-10-12 15:43:11
index_img: /img/blog_img.jpg
categories:
- [技术文章,工具使用]
tags: [工具使用]
---

# 安装 hexo 并选择主题
大概步骤就是:
```
1. $ npm install -g hexo-cli
2. $ hexo init <folder>
   $ cd <folder>
3. $ npm install
```

不懂的按着 [官方文档](https://hexo.io/zh-cn/docs/) 的步骤做不会出现什么问题.
唯一有点问题的是第2步下载主题的时候很慢而且没有进度条,可能以为卡住了,所以我直接在github 上找了一个好看的主题([fluid](https://github.com/fluid-dev/hexo-theme-fluid)) 然后下载发行版解压到themes文件夹下(注意文件夹名字要和_config.yml文件中的`themes:xxx对应`)
强烈推荐 [fluid](https://hexo.fluid-dev.com/docs/guide/#%E7%BD%91%E9%A1%B5%E7%BB%9F%E8%AE%A1) 这个主题! 还配有文档!

# 自定义配置
这一步每个人都不相同,主要就是修改 `_config.yml` 文件的内容,对应着=https://hexo.io/zh-cn/docs/configuration 来改就行.
还有对于主题内容的配置则修改 `/themes/_config.yml` ,这个参照主题文档=> https://hexo.fluid-dev.com/docs/guide/#%E7%BD%91%E9%A1%B5%E7%BB%9F%E8%AE%A1 来修改

# 部署
需要修改_config.yml文件中的两个地方:
```yaml
url: http://xxxx.github.io/xxxx.github.io # 开启page后显示的内容
root: /ttntt.github.io # 对应项目名字,不然会404

#配置使用 hexo d 命令执行条件
deploy:
  type: git
  repo: https://github.com/xxxx/xxxx.github.io.git
  branch: master
  message: "Site updated: {{ now('YYYY-MM-DD HH:mm:ss') }}"
```
然后运行即可:
```shell script
hexo clean
hexo g (generate--生成静态文件)
hexo d (deploy--文件生成后立即部署网站)
```

这里有几个坑我踩过了

### 项目名称不要随便取,必须是: [用户名.github.io]
如果是gitee则,是 `用户名.gitee.io`
因为以前用过 Docsify 写博客,对名字没有要求,所以这我没在意,试了很多次才发现这个问题所在

还有就是其实错误跟你先开启page 或者先 hexo d 没有关系.只是上传后为了清除缓存,可以再开启异常page.很多时候没有效果可能都是因为缓存.


###  博客引入图片404
在_posts下的博客直接引入本地图片时会报404,解决方案很多,主要有两种:
- 第一种:
使用阿里云的OSS存储图片,然后用CDN访问图片,直接写网上的图片地址即可.
当然这种挺方便的,但需要一定的费用,个人觉得没白嫖的香.
- 第二种:
修改_config文件的配置(大概在第45行左右),配置如下:
```yaml
post_asset_folder: true #启动 Asset 文件夹 设置后可以直接在博客中引入本地图片
marked: #配置这些后即可直接在.md文件直接引入图片
  prependRoot: true
  postAsset: true
```
也许还需要下载一个东西:
```shell script
npm install https://github.com/CodeFalling/hexo-asset-image --save
```
然后即可在博客文件中,以习惯的方式引入本地文件了,注意下本地文件夹的地址(一般放在source/images下,使用`![](/images/xxx.png)`引入)就行.

新增博文操作步骤：
需要创建单独的页面：```hexo new page <title>```
只是一篇博文：```hexo new post <title>```

