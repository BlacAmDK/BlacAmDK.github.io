# XPath提取元素

## 查询

使用xmllint或者XMLStarlet工具可以在terminal对html/xml文件进行操作, xmllint需要--html参数启用html解析器, 以下则以XMLStarlet为例进行说明:
由于在我的系统上xmlstarlet程序有名为xml的符号链接, 所以下文以xml代替

```
xml fo -H -R input.html 2>/dev/null | xml sel -t -v '//*[@class="odd"]' -c '(//*[text()="IP"])[1]/../following-sibling::td' -n
```

首先需要使用fo(format)子命令加上-H(HTML格式解析)与-R(尝试进行文件修复)对html文档进行处理, 并忽略所有警告信息
之后使用普通的sel(select)对元素进行查询

下面提供XPath的语法文档和XMLStarlet的官方文档链接:
[XMLStarlet Command Line XML Toolkit](https://xmlstar.sourceforge.net/docs.php)
[XPath Docs](https://developer.mozilla.org/en-US/docs/Web/XPath#documentation)


tags: #XPath
