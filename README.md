### 多说评论框
_posts文章默认开启评论框，而简版页面默认关闭。
_posts文章可以在开头设置`duoshuo: false`来关闭。
简版页面可以在开头设置`duoshuo: true`来开启。

### MathJax数学公式
需要在页面开头添加`math: true`来开启

在需要用到公式的地方用`\\ \\`或`$$ $$`括起来

例如：

	行内公式：
	\\E=mc^2\\

	行间公式：
	$$E=mc^2$$

### 创建文章/页面
定位到博客目录，可以运行以下命令

* 创建文章：`rake post title="Post Name"` 
* 创建简版页面：`rake life title="Page Name"`
* 创建页面： `rake page title="Page name"`

### 页面的使用
修改的都是`markdown文件`

* 普通页面  
layout项改为`blog`
* 简版页面  
layout项改为`life`
* 文章  
_posts文件夹下的markdown文件的layout项改为`post`，使用简版页面就改成`life`

### 生成静态博客
把你的博客推送到GitHub或者其它支持Jekyll的代码托管网站就可以了。  
具体可以到[Jekyll官网](http://jekyllrb.com/)或[GitHub Pages](https://pages.github.com/)查看详细教程。


