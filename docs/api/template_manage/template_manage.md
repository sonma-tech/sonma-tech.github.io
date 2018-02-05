# 修改模板

## 接口地址

> POST /template

## 请求参数

| 参数名称   |    是否必须   |       说明 |
| ----------- |:---------------:| -----:|
| id          |       是        |       模板编号 |
| template    |       否        |       模板内容,默认无 |
| remark      |       否        |       打印信息，默认打印信息为空 |

## 返回示例
```  
    {
        "code": 0,
        "message": "success"
    }
``` 
# 获取模板

> GET /template

## 请求参数

| 参数名称   |    是否必须   |       说明 |
| ----------- |:---------------:| -----:|
| id           |      是         |      模板编号 |

## 返回示例
```
    <?xml version="1.0" encoding="UTF-8"?>
    <page width="40" height="70" font-size="14" gap="2">
        <text x="4" y="3.5" font-size="16">品牌:<%=brand_name%></text>
        <text x="4" y="7" font-size="16">品类:<%=cat_info.name%></text>
        <text x="4" y="10.5" font-size="16">款号:<%=item_ref%></text>
        <text x="4" y="14" font-size="16">颜色:<%=color_name%></text>
        <text x="4" y="17.5" font-size="16">尺码:<%=size_name%></text>
        <text x="4" y="21" font-size="16">季节:<%=remark%></text>
        <text x="4" y="24.5" font-size="16">执行标准:<%=year_name%></text>
        <text x="4" y="28" font-size="16">安全类别:<%=season_name%></text>
        <text x="4" y="31.5" font-size="16">等级:合格</text>
        <text x="4" y="35" font-size="16">产地:详见内签</text>
        <text x="4" y="38.5" font-size="16">材质:详见内签</text>
        <barcode x="0" y="45.5" width="40" height="10" align="center" scale="2"><%=goods_sn%></barcode>
        <text x="4" y="60" font-size="18">零售价:<%=price_1%></text>
    </page>
```   
# 创建模板

## 接口地址

> POST /template

## 请求参数

| 参数名称    |   是否必须      |    说明 |
| ----------- |:---------------:| -----:|
| template    |       是        |       模板内容 |
| remark      |       否        |       打印信息，默认打印信息为空 |

## 返回示例
```
    {
        "code": 0,
        "message": "success",
        "id": 2065
    }
```