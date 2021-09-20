# Blog
快速使用`github`+`markdown`+`githubpages`快速搭建个人博客
## 说明
* 项目需要编码的地方只有`index.html`
* 其他文章内容，均使用`markdown`编写,和平常写github的markdown没有太大区别
### 实现
* 项目使用`marked`,将Markdown转为html，重新绘制项目内的A标签，指向为锚点，触发内部方法，从而回调传给外部
```
//定义全局方法
window.renderGithubBlogContent = (url, title) => {
    markdown2html(url, this.markedOptions).then(
        html => {
            //触发renderContent的回调
            this.blogOptions.renderContent(url, html, title)
        }
    )
}
//此处将项目内的<a>标签重新渲染了，改为锚点和触发renderGithubBlogContent方法
let renderer = new marked.Renderer()
renderer.link = (href, title, text) => {
    if (href.startsWith("http")) {
        return `<a href="${href}" target="_blank">${text}</a>`
    } else {
        return `<a href="#${href}" onclick="renderGithubBlogContent('${href}','${text}')">${text}</a>`
    }
}
```
## 项目
[dezhishen/github-blog-sdk](https://github.com/dezhishen/github-blog-sdk)
## 使用说明
* 创建index.html
> 需要自己布局好目录位置和内容主体
```
    <el-container>
        <el-aside>
            <div id="summary"></div>
        </el-aside>
        <el-main>
            <div id="content"></div>
        </el-main>
    </el-container>
```
* 创建SUMMARY.md
> 目录,使用markdown中`[]()`
* 引入js
```
<script src="https://cdn.jsdelivr.net/gh/dezhiShen/github-blog-sdk@master/dist/index.js"></script>
```
* 文件加载后初始化`sdk`,且调用`initSdk()`方法
```
// new GithubBlogSdk(blogOptions,markedOptions)
let sdk = new GithubBlogSdk({
    renderContent: (url, html, title) => {
        document.getElementById("content").innerHTML=html
    },
    renderSummary: (url, html) => {
        document.getElementById("summary").innerHTML=html
    }

},{})
sdk.initSdk()
```
* ps 可以结合vue等框架和组件构建你的主页,注意回调传入的直接是**html**
## options说明
### blogOptions
* index

首页路径,默认`./README.md`
* summary

目录路径,默认值`./SUMMARY.md`
* renderContent
> 渲染内容页的回调方法,回调参数 (url, html),`url`:地址,`html`:渲染的网页内容
```
const markdown2html = function (url, options) {
    if (url) {
        return fetch(url).then(res => {
            return marked(res.data, options)
        })
    }
}
...

// 
// 回调入口
renderContent = (url) => {
    return this.loadConntent(url).then(
        //这里回调
        html => { this.blogOptions.renderContent(url, html) }
    )
}
```

* renderSummary
> 渲染目录的回调方法,回调参数 (url, html),`url`:地址,`html`:渲染的网页内容
```
renderSummary = (url = this.blogOptions.summary) => {
    return this.loadSummary(url, this.markedOptions).then(text => {
        this.blogOptions.renderSummary(url, text)
    })
}

```
### markedOptions

使用[marked](https://github.com/markedjs/marked)
```
window.renderGithubBlogContent = (url, title) => {
    markdown2html(url, this.markedOptions).then(
        html => {
            this.blogOptions.renderContent(url, html, title)
        }
    )
}

let renderer = new marked.Renderer()
renderer.link = (href, title, text) => {
    //此处将项目内的<a>标签重新渲染了，改为锚点和触发renderGithubBlogContent方法
    //
    if (href.startsWith("http")) {
        return `<a href="${href}" target="_blank">${text}</a>`
    } else {
        return `<a href="#${href}" onclick="renderGithubBlogContent('${href}','${text}')">${text}</a>`
    }
}
markedOptions = {
    renderer: renderer,
    gfm: true,
    tables: true,
    breaks: false,
    pedantic: false,
    sanitize: false,
    smartLists: true,
    smartypants: false,
}
```