# 打印模板

模板是指依据(菜鸟打印标记语言规范)设计出来的模板。为了更加快速的进行模板设计与生成，可采用菜鸟官方提供的模板编辑器（菜鸟模板编辑器）。

# 模板内容

模板内容分为两种：静态内容和动态内容。

* 静态内容：在模板绘制的过程中，值就确定的内容我们称之为静态内容。在编辑器中进行模板设计的时候就可以将内容输入进去。
* 动态内容：在绘制模板的过程中，我们经常希望只放入一个占位符，其内容将在实际打印时才能确定，我们称之为动态内容。为了支持这种动态内容，
  在模板中可以通过编写ECMAScript(参考链接)脚本来达到生成动态内容的目的。同时对 `ECMAScript <http://www.ecmascript.org/>`_ 的解析采用了
  目前业界最先进的V8引擎，具有极高的性能，在打印服务生成打印文件前，会对ECMAScript的代码进行解析并替换为实际值。

# 动态内容的编写

动态内容可以嵌入类似js的代码，在模板中可以通过如下的方式嵌入动态代码:

* 动态语句: 以"<%"开始，以"%>"结束，如<% if(true) %>。
* 变量引用: 以"<%="开始，以"%>"结束，如"<%=data.address%>"，表示将会用data.address的实际值在模板中进行填充。允许使用自定义的变量。可参考：(链接)。
* 如果内容中要输出"<%"或者"<%="，需要添加"\"做转义，也即"<\%"。
* 内置变量：_data为内置变量,表示打印数据的内容,即打印接口中的content数据


动态代码示例如下，这个例子是将获得的打印数据（data）中的所有对象依次输出。
```
    <layout left="6" top="5" width="20" height="20" style="borderStyle:solid;">
        <text width="" value="动态数据显示" />
            <% for(i=0;i++;i<_data.arrayObject.lenth) {%>
                <text width="" value="<%=_data.arrayObject[i].value%>" style="fontSize:12"/>
            <%}%>
    </layout>
```

数据：

```
    [
        ["收件人"],
        ["发件人"],
        [ "收货地址"]
    ]
```

动态代码解析之后的静态代码为：
```
    <layout left="6" top="5" width="20" height="20" style="borderStyle:solid;">
        <text width="" value="动态数据显示" />
        <text width="" value="收件人" style="fontSize:12"/>
        <text width="" value="发件人" style="fontSize:12"/>
        <text width="" value="收货地址" style="fontSize:12"/>
    </layout>
```

---
**重要**

_开头的变量名都保留给系统使用，请不要在模板中定义_开头的变量名字。

---

# 标记语言规范

 打印机支持的单位有:px, mm,如无特殊说明,默认单位都为mm,(200dpi的打印机1mm = 8 px),坐标系以左上角为起点（0,0), page 为根节点.

## 标签说明

### page 根节点

| 属性   |   类型      |     说明 |
| ----------- |:---------------:| -----:|
| width  |  dimension   |  打印区域宽度 |
| height |  dimension   |  打印区域高度 |
| gap    |  dimension   |  纸张间隙(标签纸、面单纸需要设置) |
| offset |  dimension   |  打印完后走纸距离 |
| tear   |  boolean     |  撕纸模式 |
| shift-x|  dimension   |  x轴偏移量 |
| shift-y|  dimension   |  y轴偏移量 |



### layout 布局节点

| 属性   |   类型      |     说明 |
| ----------- |:---------------:| -----:|
| width |   dimension   |  宽度 |
| height|   dimension   |  高度 |
| left  |   dimension   |  与上一节点x轴相对位置 |
| top   |   dimension   |  与上一节点y轴相对位置 |


### line 直线

| 属性   |   类型      |     说明 |
| ----------- |:---------------:| -----:|
| startX |  dimension  |   起点 |
| startY |  dimension  |   起点 |
| endX   |  dimension  |   终点 |
| endY   |  dimension  |   终点 |


### 模板示例

[示例模板](https://api.sonma.net/template/2062)

```
    <?xml version="1.0" encoding="UTF-8"?>
    <% var contentHeight = _data.record_detail.length*5 %>

    <page xmlns="http://template.sonma.net/schema" height="<%=190+contentHeight%>" width="100">
        <image x="37.5" y="8" src="logo.BMP" />
        <text  x="0" y="35" width="100" height="5" font-size="12" align="center" font-name="simhei.TTF">欢迎光临 <%=_data.brand_name%></text>
        <bar x="3" y="44" width="94" height="1"/>
        <text x="3" y="48" width="100" height="4" font-size="10" font-name="simsun.TTF" align="left">柜    台：<%=_data.brand_name%></text>
        <text x="3" y="54" width="100" height="4" font-size="10" font-name="simsun.TTF" align="left">销售单号：<%=_data.record_code%></text>
        <text x="3" y="59" width="100" height="4" font-size="10" font-name="simsun.TTF" align="left">打印时间：<%=_context.formatStartTime('yyyy-MM-dd HH:mm:ss')%></text>
        <text x="3" y="64" width="100" height="4" font-size="10" font-name="simsun.TTF" align="left">联系电话：<%=_data.shop_tel%></text>
        <text x="3" y="69" width="100" height="4" font-size="10" font-name="simsun.TTF" align="left">销售日期：<%=_data.record_time%></text>
        <text x="3" y="74" width="100" height="4" font-size="10" font-name="simsun.TTF" align="left">收 银 员：<%=_data.user_name%></text>
        <line x="3" y="80" endx="97" endy="80"/>
        <text x="3" y="83" width="37.5" height="4" align="center" debug="false" font-name="simsun.TTF">商品</text>
        <text x="37.5" y="83" width="14.125" height="4" align="center" debug="false" font-name="simsun.TTF">规格</text>
        <text x="51.625" y="83" width="14.125" height="4" align="center" debug="false" font-name="simsun.TTF">数量</text>
        <text x="65.75" y="83" width="14.125" height="4" align="center" debug="false" font-name="simsun.TTF">单价</text>
        <text x="79.875" y="83" width="14.125" height="4" align="center" debug="false" font-name="simsun.TTF">金额</text>
        <% var y = 89 %>
        <% for(var i=0; i < _data.record_detail.length; i++)  {%>
        <text x="3" y="<%=y%>" width="37.5" height="4" align="left" debug="false" font-name="simsun.TTF"><%=_data.record_detail[i].goods_name  || "" %></text>
        <text x="37.5" y="<%=y%>" width="14.125" height="4" align="center" debug="false" font-name="simsun.TTF"><%=_data.record_detail[i].ggmc || "" %></text>
        <text x="51.625" y="<%=y%>" width="14.125" height="4" align="center" debug="false" font-name="simsun.TTF"><%=_data.record_detail[i].num || "" %></text>
        <text x="65.75" y="<%=y%>" width="14.125" height="4" align="center" debug="false" font-name="simsun.TTF"><%=_data.record_detail[i].price  || "" %></text>
        <text x="79.875" y="<%=y%>" width="14.125" height="4" align="center" debug="false" font-name="simsun.TTF"><%=_data.record_detail[i].money || "" %></text>
        <% y += 5 %>
        <%}%>


        <text x="3" y="<%=y%>" width="37.5" height="4" align="left" debug="false" font-name="simsun.TTF">合计：<%=_data.record_detail.length%></text>
        <text x="37.5" y="<%=y%>" width="14.125" height="4" align="center" debug="false" font-name="simsun.TTF"><%=_data.total_num%></text>
        <text x="79.875" y="<%=y%>" width="14.125" height="4" align="center" debug="false" font-name="simsun.TTF"><%=_data.total_money%></text>
        <%y+=10%>

        <line x="3" y="<%=y%>" endx="97" endy="<%=y%>"/>
        <%y+=5%>


        <text x="3" y="<%=y%>" width="20" height="4" font-name="simsun.TTF">收银方式</text>
        <%y+=5%>

        <text x="3" y="<%=y%>" width="100" height="5" font-size="12" font-name="simhei.TTF">实收：<%=_data.pay_money%>  找零：<%=_data.zlje | {}%></text>
        <%y+=9%>
        <line x="3" y="<%=y%>" endx="97" endy="<%=y%>"/>
        <%y+=2%>
        <qrcode x="74" y="<%=y%>" width="20" height="20" align="right" debug="false">http://mp.weixin.com/r/TEg0LHfEvSrCrTNP9x1e</qrcode>
        <text x="3" y="<%=y%>" width="100" height="4" font-name="simsun.TTF">会员卡号：<%=_data.vip_code%></text>
        <%y+=5%>
        <text x="3" y="<%=y%>" width="100" height="4" font-name="simsun.TTF">本单积分：<%=_data.integral || 0%></text>
        <%y+=5%>
        <text x="3" y="<%=y%>" width="100" height="4" font-name="simsun.TTF">会员积分：<%=_data.integral || 0%></text>
        <%y+=8%>

        <bar x="3" y="<%=y%>" width="94" height="1"/>
        <%y+=4%>
        <text x="3" y="<%=y%>" width="20" height="4" font-name="simhei.TTF">退换政策</text>
        <%y+=6%>
        <text x="5" y="<%=y%>" width="90" height="50" font-name="simsun.TTF" space="1" font-size="9">1.退换商品必须出示原始收银条，银行卡退款时须多加出示原始签购单及银行卡。
    2.商品在未经穿着、洗涤、损坏并保留吊牌和洗唛的前提下。在购买后1个月内可退换。
    3.非顾客原因造成质量问题的商品，3个月内可无条件退换。
    4.由于顾客自身原因而造成质量问题或已穿着、洗涤、损坏、修改（如长裤、皮带）的商品及贴身商品恕不退还。
    5.银行卡退款将在30至45个工作日退回原卡。根据银行规定银行卡不可退现金。谢谢惠顾！</text>
    </page>
```

