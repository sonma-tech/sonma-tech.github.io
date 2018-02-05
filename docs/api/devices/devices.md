# 固件升级

## 接口地址

> POST /firmware

## 请求地址

| 参数名称 |      是否必须     |     说明 |
| ----------- |:---------------:| -----:|
| version     |       是        |       打印机版本信息 |
| sn          |       是        |       打印机唯一编号 |
| user        |       是        |       使用者 |
| model       |       是        |       固件模板 |

## 返回示例
    
    TSCDA200.v1.1.2.user1.bin
    
    
# 设备列表

## 接口地址

> GET /org/{id}/devices

## 请求参数

| 参数名称   |    是否必须     |     说明 |
| ----------- |:---------------:| -----:|
| id          |       是        |       公司ID |
| pre_pag     |       否        |       每页展示的打印机数量,默认20页 |
| page        |       否        |       起始页，默认第0页 |

## 返回示例

    {
        "code": 0,
        "message": "success",
        "list": [
            {
                "sn": 1004217093,
                "name": "GPZH816",
                "type": "GPZH816",
                "online": false,
                "status": 0
            },
            {
                "sn": 2000708988,
                "name": "GPZH816",
                "type": "GPZH816",
                "mfg": "Aug 18, 2017 4:14:29 PM",
                "remark": "测试打印机",
                "online": false,
                "status": 0
            },
            {
                "sn": 1002123456,
                "name": "测试打印机",
                "type": "TSCDA200",
                "mfg": "May 24, 2017 6:32:44 PM",
                "remark": "测试打印机",
                "online": false,
                "status": 0
            }
        ],
        "total": 45,
        "pageNum": 0,
        "pageSize": 3
    }