# 我的blog站发布器

_config.yml: 
网站的 配置 文件。 您可以在此配置大部分的参数。详细配置说明：https://hexo.io/zh-cn/docs/configuration

package.json: 应用程序的信息。 EJS, Stylus 和 Markdown 渲染引擎 已默认安装，您可以自由移除。 如果您想，可以稍后卸载它们

```
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": ""
  },
  "dependencies": {
    "hexo": "^7.0.0",
    "hexo-generator-archive": "^2.0.0",
    "hexo-generator-category": "^2.0.0",
    "hexo-generator-index": "^3.0.0",
    "hexo-generator-tag": "^2.0.0",
    "hexo-renderer-ejs": "^2.0.0",
    "hexo-renderer-stylus": "^3.0.0",
    "hexo-renderer-marked": "^6.0.0",
    "hexo-server": "^3.0.0",
    "hexo-theme-landscape": "^1.0.0"
  }
}
```

scaffolds: 模版 文件夹。 当您新建文章时，Hexo 会根据 scaffold 来创建文件。

source:
资源文件夹。 是存放用户资源的地方。 除 _posts 文件夹之外，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。 Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。

themes: 主题 文件夹。 Hexo 会根据主题来生成静态页面。



