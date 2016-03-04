---
layout: post
title:  "jquery datatable 使用"
date:   2016-03-04 16:35:00
categories: datatable
---
jquery datatable 使用

统一定义：
注："pagingType": "input",需要用到自定义的插件

~~~ javascript
initTable=(function(tableId,params,afterInitFunction) {
    var configs = {
        "bProcessing": true, // 是否显示取数据时的那个等待提示
        "iDisplayLength": app_pageSize,
        "pagingType": "input",
        "language": {
            "url": "bootstrap/plugins/datatables/datatable.msg_cn.txt"
        },
        "bAutoWidth":false,
        "sPaginationType":"full_numbers",
        "ordering": false,
        "searching": false,
        "lengthChange": false,
        "bServerSide": true,//这个用来指明是通过服务端来取数据
        "fnServerData": function (sSource, aoData, fnCallback) {
            $.ajax({
                url: sSource,
                data: {"aoData": JSON.stringify(aoData)},
                type: 'post',
                dataType: 'json',
                async: false,
                success: function (result) {
                    if(result.state=="0"){
                        fnCallback(result.data);
                    }else if(result.state=="590"||result.state=="590"){
                        layer.msg(result.msg);
                        if(result.state="590"){
                            window.location = rootPath + "/login.shtml";
                        }
                    }else{
                        layer.msg("数据加载错误");
                        $("#" + tableId + "_processing").css("display", "none");
                    }
                },
                error: function (msg) {
                    layer.msg("数据加载错误");
                    $("#" + tableId + "_processing").css("display", "none");
                }
            });
        }
    };
    var conf = $.extend(configs, params);
    return $('#'+tableId).DataTable(conf);
});
~~~

调用 initTable：

~~~ javascript
var goodsTable =initTable("goodsTable", {
    "aoColumns": [
        {
            "sTitle": "<input id='selectAll' type='checkbox'/>",
            "mDataProp": null,
            "sWidth": "20px",
            "sDefaultContent": "",
            "bSortable": false,
            "mRender": function (data, type, full) {
                return "<input type='checkbox' rowType='checkbox' value='" + full.productId + "' ></input> "
            }
        },
        {
            "mData": "name", 'sClass': 'left',
            "mRender": function (data, type, full) {
                return "<img src='" + full.logo + "' class='tinyImg' alt='" + full.name + "'/>" + data;
            }
        }],
    "sAjaxSource": rootPath + "/goods/goods/goods.json",
    "fnServerParams": function (aoData) {
        aoData.push(
            { "name": "thirdCateId", "value": $("#third_category_id").val() }
        );
    },
    "fnRowCallback": function (nRow, aData, iDisplayIndex) {
        if (aData.status == "0") {
            $(nRow).addClass("isOffSail");
        }
        return nRow;
    }
});
~~~

fnRowCallback：行回调，可用于给表格的每一行加样式等，见上图。

createdRow：单元格处理，可用于给单元格加样式等，如下：
~~~ javascript
"createdRow": function ( row, data, index ) {
    if ( data['isRecommend'] =="0" ) {
        $('td', row).eq(8).addClass("un-recommend-goods");
    }else if( data['isRecommend'] =="1" ){
        $('td', row).eq(8).addClass("recommend-goods");
    }
},
~~~

当需要在表格渲染完做一些事情时，可用如下方法：                  

~~~ javascript
goodsTable.on( 'draw', function () {
    var pageInfo =goodsTable.page.info();
    $("#totalRecords").html("记录数："+pageInfo.recordsTotal);
});
~~~

给表格行/单元格添加事件：

~~~ javascript
$('#transferDetailTable tbody').on('click', 'tr', function (e) {
    var ifCanAudit = $("#transferSubmit").attr("disabled");
    if(ifCanAudit!="disabled"){
        showTransferWindow($(this).find("td:eq(0)").find("input:eq(0)").val());
    }
});
~~~

自定义分页插件：

见笔记 jquery.datatable自定义分页插件

<a href="{{site.baseurl}}/index.html">Go back</a>


