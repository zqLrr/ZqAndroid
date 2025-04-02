# Http详解

## 请求头

## Content-Type

是请求头中实际发送的数据类型。

MediaType,即是Internet Media Type，互联网媒体类型；也叫做MIME类型，在Http协议消息头中，使用Content-Type来表示具体请求中的媒体类型信息。

常见的媒体格式类型如下：

- text/html ： HTML格式

- text/plain ：纯文本格式   

- text/xml ： XML格式

- image/gif ：gif图片格式  

- image/jpeg ：jpg图片格式 

- image/png：png图片格式

  以application开头的媒体格式类型：

- application/xhtml+xml ：XHTML格式

- application/xml   ： XML数据格式

- application/atom+xml ：Atom XML聚合格式  

- application/json  ： JSON数据格式

- application/pdf    ：pdf格式 

- application/msword ： Word文档格式

- application/octet-stream ： 二进制流数据（如常见的文件下载）

- application/x-www-form-urlencoded ： <form encType="">中默认的encType，form表单数据被编码为key/value格式发送到服务器（表单默认的提交数据的格式）

  另外一种常见的媒体格式是上传文件之时使用的：

- multipart/form-data ： 需要在表单中进行文件上传时，就需要使用该格式

## 请求报文

http1.1 由请求行、请求头和请求实体三部分组成。

类型的使用地方

* ENTITY_TYPE_STRING

  | 类名#方法名                               |
  | ----------------------------------------- |
  | FeedReadTimeReportActivity#initIntent（） |

* ENTITY_TYPE_FORM_URLENCODED

  有三十多处

* ENTITY_TYPE_BYTE_STREAM

  | 类名#方法名                                   |
  | --------------------------------------------- |
  | FileUploadParam#createPostRequestBundle()     |
  | UploadFileSendParam#createPostRequestBundle() |

* ENTITY_TYPE_BYTE_ARRAY

  | 类名#方法名                                   |
  | --------------------------------------------- |
  | UploadAppInfosParam#createPostRequestBundle() |
  | HttpSender#initClient                         |

* ENTITY_TYPE_APPLICATION_JSON

  | 类名#方法名                                   |
  | --------------------------------------------- |
  | VideoMachineRequest#createPostRequestBundle() |

* ENTITY_TYPE_MULTIPART

  | 类名#方法名             |
  | ----------------------- |
  | FileSender#initClient() |



### HTTP和WebSocket的区别

| 特性             | **HTTP**                              | **WebSocket**                        |
| ---------------- | ------------------------------------- | ------------------------------------ |
| **连接类型**     | 短连接，单次请求-响应                 | 长连接，全双工通信                   |
| **通信模式**     | 请求/响应模式                         | 双向全双工通信                       |
| **实时性**       | 非实时，延迟较高                      | 实时，低延迟                         |
| **协议开销**     | 较大，每次请求都有请求头和响应头      | 较小，一旦连接建立，后续消息头开销低 |
| **适用场景**     | 静态资源请求，RESTful API，页面加载等 | 实时消息推送，在线聊天，在线游戏等   |
| **连接生命周期** | 每次请求后连接关闭                    | 连接持续存在，直到主动关闭连接       |

