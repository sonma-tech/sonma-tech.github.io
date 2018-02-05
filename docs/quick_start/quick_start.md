# 快速开始

## 对接流程

> 获取 AccessKey、SecretKey --> 接口调试 --> 模板对接

---
**重要**

`AccessKey` 和 `SecretKey` 是您访问胜马云API的密钥，具有该账户完全的权限，请您妥善保管，泄漏后需及时重置。

---

## 公共参数

公共参数是指每个接口调用都必须提交的参数,特殊说明除外。非 Token 鉴权方式在请求头中附带鉴权头。

### 公共请求头

| 参数名称      |     是否必须     | 说明   |
| ----------- |:---------------:| -----:|
| Authorization  |      是      |    鉴权字符串，计算方式 :`Base64 <base64>`[base64] (:ref:`HMAC-SHA1 <hmac-sha1>` + 空格 + : + `AccessKey` + : + :ref:`Signature <signature>`) ，详见本文 签名机制 :ref:`sign` 部分 |
| Content-Type   |      否      |    请求内容的MIME类型,默认为 `application/x-www-form-urlencoded`,上传文件时 `multipart/form-data`。 |
| Timestamp      |      是      |    请求创建的时间戳(10位)。格林威治时间1970年01月01日00时00分00秒起至现在的总秒数 |

---
**注解**

使用Token 鉴权时无需请求头。。

---

### 公共返回头

暂无

## 签名机制

胜马云会对每个请求进行鉴权，向胜马云发出的请求都需要在请求中包含鉴权（`Authorization`）信息。胜马云会使用 `AccessKey` 和 `SecretKey` 进行对称加密的方法来校验请求的发送者身份。

用户需要在HTTP请求头中增加 `Authorization` 来包含签名信息。

### 签名的计算规则

签名计算伪代码
``
    #鉴权字符串
    Authorization = Base64(HMAC-SHA1 + 空格 + AccessKey+ : + Signature)
    #签名
    Signature = HexEncode(HmacSHA1(StringToSign,SecretKey))
    #待签字符串
    StringToSign  = Timestamp + '\n' + HashedCanonicalQueryString
    #规范请求字符串哈希
    HashedCanonicalQueryString = HexEncode(Hash(CanonicalQueryString)
``    
规范查询字符串的创建(`CanonicalQueryString`)

   1. 按字符代码以升序（ASCII 顺序）对参数名称进行排序。例如，以大写字母 F（ASCII 代码 70）开头的参数名称排在以小写字母 b（ASCII 代码 98）开头的参数名称之前。
   2. 根据以下规则对每个参数名称和值使用 UTF-8 字符集进行 URI 编码:
        * 请勿对 `RFC 3986 <http://tools.ietf.org/html/rfc3986>`_ 定义的任何非预留字符进行 URI 编码，这些字符包括：A-Z、a-z、0-9、连字符 (-)、下划线 (_)、句点 (.) 和波浪符 ( ~ )。
        * 使用 %XY 对所有其他字符进行百分比编码，其中“X”和“Y”为十六进制字符（0-9 和大写字母 A-F）。例如，空格字符必须编码为 %20（不像某些编码方案那样使用“+”），扩展 UTF-8 字符必须采用格式 %XY%ZA%BC。
   3. 以排序后的列表中第一个参数名称开头，构造规范查询字符串。
   4. 对于每个参数，追加 URI 编码的参数名称，后跟字符 `=` （ASCII 代码 61），再接 URI 编码的参数值。对没有值的参数使用空字符串。
   5. 在每个参数值后追加字符 `&` （ASCII 代码 38），列表中最后一个值除外。
   
---
**注解**

   一般支持URL编码的库（比如Java中的java.net.URLEncoder）都是按照“application/x-www-form-urlencoded”的MIME类型的规则进行编码的。实现时可以直接使用这类方式进行编码，把编码后的字符串中加号（+）替换成%20、星号（*）替换成%2A、%7E替换回波浪号（~），即可得到上述规则描述的编码字符串。
      `JavaScript RFC3986 <https://af-design.com/2008/03/14/rfc-3986-compliant-uri-encoding-in-javascript/>`_,PHP可直接使用 `rawurlencode`
      
---

### 相关函数说明

* `Base64` 表示 Base64 Encode 函数

* `Hash` 表示生成消息摘要的函数，使用 `SHA-1`。

* `HexEncode` 表示以小写字母形式返回摘要的 `base-16` 编码的函数。例如， `HexEncode("m")` 返回值 **6d** 而不是 **6D** 。输入的每一个字节都必须表示为两个十六进制字符。

* `HmacSHA1(data, key)` 表示以二进制格式返回输出的 `HMAC-SHA1` 函数。
    * 确保以正确的顺序为您要使用的编程语言指定 `HMAC` 参数。在此示例中，密钥是第一个参数，内容是第二个参数，但您使用的函数可能以不同顺序指定密钥和内容。
    * 使用摘要来派生密钥。大多数语言都有用来计算二进制格式哈希（通常称为摘要）或十六进制编码哈希（称为十六进制摘要）的函数。派生密钥需要您 **使用摘要**。
    
### 请求示例
```
    POST /v1/print/ HTTP/1.1
    Host: api.sonma.net
    Cache-Control: no-cache
    Content-Type: application/x-www-form-urlencoded
    
    content=~~~ !!!+++*&^%$#@?/_&sn=123456789
```    
1. 创建待签字符串
    1. 以请求时间戳开头，后跟换行符。该值必须与您在请求头中(`Timestamp`)使用的值匹配。 ::

        1497508720\n

    2. 追加规范查询字符串的哈希。该值后面不跟换行符。

        1. 示例请求参数如下 ::

            content=~~~ !!!+++*&^%$#@?/_&sn=123456789

        2. URI编码排序后查询字符串如下(`CanonicalQueryString`) ::

            content=~~~%20%21%21%21%2B%2B%2B%2A%26%5E%25%24%23%40%3F%2F_&sn=123456789

        3. 规范查询字符串哈希(`HashedCanonicalQueryString`) ::

            CanonicalQueryString =
            HashedCanonicalQueryString = HexEncode(Hash("content=~~~%20%21%21%21%2B%2B%2B%2A%26%5E%25%24%23%40%3F%2F_&sn=123456789") = "bce2029159576daffb8574ae670697bbbb186281"

    3. 示例待签字符串(`StringToSign`) ::

        1497508720\nbce2029159576daffb8574ae670697bbbb186281

2. 计算签名(`Signature`), `AccessKey`: 123456789, `SecretKey`: 123456789 ::

```
    SecretKey = "123456789"
    StringToSign = "1497508720\nbce2029159576daffb8574ae670697bbbb186281"
    Signature = HexEncode(HmacSHA1(StringToSign,SecretKey))
              = "e750db371d068d16b36422a6f36bd177daf1c2aa"
```
3. 生成签名字符串 ::

```
    Authorization = Base64("HMAC-SHA1 123456789:e750db371d068d16b36422a6f36bd177daf1c2aa")
                  = "SE1BQy1TSEExIDEyMzQ1Njc4OTplNzUwZGIzNzFkMDY4ZDE2YjM2NDIyYTZmMzZiZDE3N2RhZjFjMmFh"
```
4. 完整的请求报文 ::

```
    POST /v1/print/ HTTP/1.1
    Host: api.sonma.net
    Authorization: SE1BQy1TSEExIDEyMzQ1Njc4OTplNzUwZGIzNzFkMDY4ZDE2YjM2NDIyYTZmMzZiZDE3N2RhZjFjMmFh
    Timestamp: 1497508720
    Cache-Control: no-cache
    Content-Type: application/x-www-form-urlencoded

    content=~~~%20%21%21%21%2B%2B%2B%2A%26%5E%25%24%23%40%3F%2F_&sn=123456789
```
---
**重要**

当请求参数为空时,待签字符串(`StringToSign`)中, `Timestamp` 后也要加上 `\n`

---

# [DEMO](https://github.com/sonma-tech/demo)

# 特别说明

## 使用场景

推荐开发者将 AccessKey和一个SecretKey 放在服务端，由服务端生成令牌后颁发给客户端使用。

比如，客户端绑定打印机的时候：

业务服务器的服务端生成鉴权令牌（Token）

客户端程序（iOS、Android 以及 Web）拿到这个鉴权令牌之后就拥有该打印机的使用权限

## 错误案例

把 AK/SK 放在客户端 SDK 中来做签名生成令牌，随 App 被发布出去。

这样做会被人家反编译之后拿到 AK/SK，之后他们就可以对你的账号进行操作。

实际上 Web 端 js 也是可以做签名的，只是你不可能把明文的 AK/SK 放在 Web 端，这样做更加危险。

将 AK/SK 加密后存放在客户端，等用户启动应用的时候再将 AK/SK 解密出来放在内存中，关闭应用后这对 AK/SK 即消失。

这样的做法也是不科学的，因为你的 AK/SK 在 后台 随时可以更改，特别是在被泄漏之后建议使用一对新的 AK/SK。如果你写死在 应用 中将其发布，就只能通过发布新版本的 应用 来更新这对 AK/SK。

## 最后建议

**重要**

出于安全考虑，建议您根据自己的场景周期性地更换密钥。

---

# 测试账号

> AccessKey: 123456789, SecretKey: 123456789, 打印机唯一编号:123456789

# 术语

串号
打印机唯一编号