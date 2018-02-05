# 开放接口


```
    release:    https://api.sonma.net
    beta:       http://api-beta.sonma.net
```

---
**重要**

数据中包含敏感信息请务必使用https。

---

# 获取Token

调用API时需要使用token或者请求头进行鉴权,token存在泄漏风险,外部调用务必使用https

## 接口地址

> GET /v1/auth/access_token


## 请求参数

| 参数名称    |   是否必须      |    说明 |
| ----------- |:---------------:| -----:|
| scope    |    是         |      权限范围: * 表示账户下所有打印机 ,[xxx,xxxx,...]表示指定打印机 |
| exp      |    否         |      过期时间,10位Unix时间戳,最长时间为1年,不传该参数生成的token,30分钟内无请求则被吊销 |

## 返回示例
```
    {
      "code": 0,
      "token": "eyJhbGciOiJIUzI1NiJ9.eyJ1aWQiOiIzNDAwNjkiLCJzY29wZSI6WyIqIl0sImlzcyI6ImFwaS5zb25tYS5uZXQiLCJleHAiOjE0OTc1MTkzNDJ9.PwlIwY9IzqYEM4NTnKofLz9TbmEfHmxbjmrOnOA9ciA"
    }
```
or
```
    {
      "code":40030,
      "message":"过期时间不能早于Thu Jun 15 07:37:45 UTC 2017"
    }
```


# 吊销token

当token泄漏时,吊销该token

## 接口地址

> DELETE /auth/access_token/{token}

---
**重要**

必须使用请求头鉴权

---

## 返回示例
```
    {
        "code": 0,
        "message": "success"
    }
```
# 烧写图片

向打印机中写入图片文件

---
**重要**

当图片需要频繁使用时需要缓存到打印机中,使用别名打印

---

## 接口地址

> POST /v1/print/image

## 请求参数

| 参数名称    |   是否必须     |     说明 |
| ----------- |:---------------:| -----:|
| token    |    否      |         鉴权方式自选 |
| sn         |  是      |         打印机串号 |
| file       |  是      |         图片文件 |
| name       |  否      |         图片名称(不带后缀名),如果未传该参数则取 file part 中filename作为图片名 |
| width      |  否      |         指定图片宽度(单位像素,最大800) |
| height     |  否      |         指定图片高度(单位像素,最大800) |
| threshold  |  否      |         图片黑白处理阀值(0~255),数值越大图片越黑 |
| scale      |  否      |         缩放比例,默认为1 |

## 返回示例
```
    {
        "code": 0,
        "message": "logo.BMP"
    }
```

## 图片测试模板
```
    <?xml version="1.0" encoding="UTF-8"?>
    <page width="100" height="100" >
        <image x="0" y="0" width="100" height="100" src="logo.BMP"/>
    </page>
```
---

烧写后打印机中图片别名为 <图片名>.BMP

---


# 获取打印机状态(在线)

## 接口地址

> GET /printer/{sn}/status

## 请求参数

| 参数名称    |   是否必须     |     说明 |
| ----------- |:---------------:| -----:|
| token    |    否        |       鉴权方式自选 |

## 返回示例
```
    {
        "code": 0,
        "online": true
    }
```
# 获取打印机状态(详细)

## 接口地址

> GET /printer/{sn}


## 请求参数

| 参数名称    |   是否必须     |     说明 |
| ----------- |:---------------:| -----:|
| token     |   否        |       鉴权方式自选 |

## 返回示例
```
    {
        "sn": 1002123456,
        "name": "测试打印机",
        "type": "TSCDA200",
        "online": false,
        "status": 0,
        "queue": 0
    }
```
## 打印机状态码(status)说明

| status    |     说明 |
| ----------- |:---------------:|
| 00     |     Normal |
| 01     |     Head opened |
| 02     |     Paper Jam |
| 03     |     Paper Jam and head opened |
| 04     |     Out of paper |
| 05     |     Out of paper and head opened |
| 08     |     Out of ribbon |
| 09     |     Out of ribbon and head opened |
| 0A     |     Out of ribbon and paper jam |
| 0B     |     Out of ribbon, paper jam and head opened |
| 0C     |     Out of ribbon and out of paper |
| 0D     |     Out of ribbon, out of paper and head opened |
| 10     |     Pause |
| 20     |     Printing |
| 80     |     Other error |


# 打印

## 接口地址

> POST /v1/print

## 请求参数

| 参数名称   |    是否必须    |      说明 |
| -------- |:------------:|:------------:|
| content   |   是        |       可以是模板数据混合的形式(xml)或者json数据(需要传template) |
| token     |   否        |       鉴权方式自选 |
| sn        |   否        |       打印机唯一编号 |
| template  |   否        |       可以是模板编号、URL(仅支持菜鸟模板链接)或者模板内容(xml) |

---

调试模板的时候可以通过template上传模板内容,无需频繁修改模板使用id

---

## 返回示例
```
    {
      "code": 0,
      "message": "success"
    }
```
or
```
    {
        "code": 202,
        "message": "打印机离线,已加入打印队列"
    }
```

# 清空打印队列

## 接口地址

> DELETE /printer/{sn}/queue

## 请求参数

| 参数名称   |    是否必须    |      说明 |
| -------- |:------------:|:------------:|
| sn      |     否        |       打印机唯一编号 |

## 返回示例
```
    {
      "code": 0,
      "message": "success"
    }
```
# 返回码说明

| 返回码    |   说明           |                                   http status code |
| -------- |:-----------------------:|:------------------------------------------:|
| 0        |   调用成功                 |                          200 |
| 202      |   打印机离线               |                         202 |
| 40000    |   客户端错误               |                          400 |
| 40001    |   参数类型错误             |                           400 |
| 40002    |   请求过期                |                           400 |
| 40300    |   鉴权失败                |                           403 |
| 40400    |   资源不存在              |                            404 |
| 40900    |   资源已存在              |                            409 |
| 50000    |   服务端错误              |                            500 |
| 50100    |   功能未实现              |                            501 |