<!--
    需要填充的占位符：
    
    README.md
    
        Quant 中文系列文档：文档中文名
        {nameEn}：文档英文名
        {urlEn}：文档原始链接
        quant-docs：域名前缀
        飞龙：负责人名称
        wizardforcel：负责人 Github 用户名
        562826179：负责人 QQ
        quant-docs-zh：ApacheCN 的 Github 仓库名称
        quant-docs-zh：DockerHub 仓库名称
        quant-docs-zh：PYPI 包名称
        quant-docs-zh：NPM 包名称
    
    CNAME
    
        quant-docs：域名前缀

    index.html
    
        Quant 中文系列文档：文档中文名
        #333：显示颜色
        quant-docs-zh：ApacheCN 的 Github 仓库名称

    asset/docsify-opendoccn-footer.js
    
        quant-docs-zh：ApacheCN 的 Github 仓库名称
-->

# Quant 中文系列文档

> 协议：[CC BY-NC-SA 4.0](http://creativecommons.org/licenses/by-nc-sa/4.0/)
> 
> 阶段：机翻（1）
> 
> 真相一旦入眼，你就再也无法视而不见。——《黑客帝国》

* [在线阅读](https://quant-docs.flygon.net)

## 下载

### Docker

```
docker pull apachecn0/quant-docs-zh
docker run -tid -p <port>:80 apachecn0/quant-docs-zh
# 访问 http://localhost:{port} 查看文档
```

### NPM

```
npm install -g quant-docs-zh
quant-docs-zh <port>
# 访问 http://localhost:{port} 查看文档
```

## 赞助我

![](https://img-blog.csdnimg.cn/20200112005920729.png)
