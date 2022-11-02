# 网页技术与标准
| 目的 | 补充描述 | 方式 |
| :--------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 文档呈现语言 |  | ▪ [HTML](http://baike.baidu.com/view/692.htm)▪ [XHTML](http://baike.baidu.com/view/15906.htm)▪ [XML](http://baike.baidu.com/view/63.htm)▪ [XForms](http://baike.baidu.com/view/755399.htm)▪ [DHTML](http://baike.baidu.com/view/8389.htm) |
| 样式格式描述语言 |  | ▪ [层叠样式表](http://baike.baidu.com/view/728238.htm)▪ [XSL](http://baike.baidu.com/view/45861.htm) |
| **动态网页**技术 | 服务端渲染网页 | ▪ [CGI](http://baike.baidu.com/view/32614.htm)▪ [FastCGI](http://baike.baidu.com/view/641394.htm)▪ [ASP](http://baike.baidu.com/view/2616.htm)▪ [ASP.NET](http://baike.baidu.com/view/6752.htm)▪ [ColdFusion](http://baike.baidu.com/view/276562.htm)▪ [JSP](http://baike.baidu.com/view/3387.htm)▪ [PHP](http://baike.baidu.com/view/99.htm) |
| **客户端交互**技术 |  | ▪ [ActiveX](http://baike.baidu.com/view/28141.htm)▪ [Java Applet](http://baike.baidu.com/searchword/?word=Java Applet&pic=1&sug=1&enc=utf8)▪ [JavaFX](http://baike.baidu.com/view/951173.htm)▪ [AJAX](http://baike.baidu.com/view/1641.htm)▪ [Silverlight](http://baike.baidu.com/view/942429.htm)▪ [ActionScript](http://baike.baidu.com/view/9376.htm)▪ [Flex](http://baike.baidu.com/view/623340.htm)▪ [AIR](http://baike.baidu.com/view/4449.htm) |
| 客户端脚本语言 |  | ▪ [JavaScript](http://baike.baidu.com/view/16168.htm)▪ [JScript](http://baike.baidu.com/view/40829.htm)▪ [VBScript](http://baike.baidu.com/view/24920.htm)▪ [ECMAScript](http://baike.baidu.com/view/810176.htm) |
| **标识定位**语言 |  | ▪ [URL](http://baike.baidu.com/view/1496.htm)▪ [URI](http://baike.baidu.com/view/160675.htm)▪ [XPath](http://baike.baidu.com/view/307399.htm)▪ [URL重写](http://baike.baidu.com/view/1002788.htm) |
| 文档纲要语言 |  | ▪ [DTD](http://baike.baidu.com/view/147436.htm)▪ [XML Schema](http://baike.baidu.com/view/1032417.htm) |

# CGI

- [协议翻译机](https://zhuanlan.zhihu.com/p/25013398)
- 

[公共网关接口](https://baike.baidu.com/item/公共网关接口/10911997?fromModule=lemma_inlink)（Common Gateway Interface，CGI）是Web 服务器运行时外部程序的**规范**，按CGI 编写的程序可以**扩展服务器功能**。

CGI 应用程序能与[浏览器](https://baike.baidu.com/item/浏览器/213911?fromModule=lemma_inlink)进行交互，还可通过数据API与[数据库服务器](https://baike.baidu.com/item/数据库服务器/613818?fromModule=lemma_inlink)等外部数据源进行[通信](https://baike.baidu.com/item/通信/300982?fromModule=lemma_inlink)，从数据库服务器中获取数据。格式化为HTML文档后，发送给浏览器，也可以将从浏览器获得的数据放到数据库中。几乎所有[服务器](https://baike.baidu.com/item/服务器/100571?fromModule=lemma_inlink)都支持CGI，可用任何语言编写CGI，包括流行的C、C ++、Java、VB 和Delphi 等。

| 项目     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| 性质     | Web 服务器运行时外部程序的**规范**                           |
| 作用     | 按CGI 编写的程序可以**扩展服务器功能**                       |
| 使用方式 | - CGI 应用程序能与[浏览器](https://baike.baidu.com/item/浏览器/213911?fromModule=lemma_inlink)进行交互<br />- 通过数据API与[数据库服务器](https://baike.baidu.com/item/数据库服务器/613818?fromModule=lemma_inlink)等外部数据源进行[通信](https://baike.baidu.com/item/通信/300982?fromModule=lemma_inlink)，从数据库服务器中获取数据<br />- 格式化为HTML文档后，发送给浏览器，也可以将从浏览器获得的数据放到数据库中 |
| 支持情况 | - 几乎所有[服务器](https://baike.baidu.com/item/服务器/100571?fromModule=lemma_inlink)都支持CGI<br />- 可用**任何语言**编写CGI，包括流行的C、C ++、Java、VB 和Delphi 等 |



## 标准CGI

- 使用**命令行参数**或**环境变量**表示服务器的详细请求
- 服务器与浏览器通信采用**标准IO**方式



## 间接CGI

- 间接CGI又称缓冲CGI，在**CGI程序**和**CGI接口**之间插入一个**缓冲程序**
  - 缓冲程序与CGI接口间用**标准IO**进行通信 

