# Ace Admin_bootstrap_addtabs

#### 介绍
Bootstrap ACE模板实现sidebar菜单联动tabs页签（你值得拥有）——JS拓展

说在前面的话：
        本博文主要使用ace admin实现Tab页+iframe的组合。实现的目标是：\
            1、不改动ace admin的模板框架（Bootstrap v3.3.6）；\
            2、sidebar实现蓝色图标点击选中效果；\
            3、sidebar标题与tab页的联动，tab页与sidebar的联动可实现；\
            4、tab标签页的动态滚动（左滚、右滚，左右滚动一屏）;\
            5、右键菜单（关闭左侧、右侧、其他，全部关闭，刷新），全屏

一、效果展示
 
![result](https://images.gitee.com/uploads/images/2019/0422/170709_39577614_954087.gif "0.gif")

![right menu](https://images.gitee.com/uploads/images/2019/0422/170735_9026b7fa_954087.png "4.png")

![loarbar](https://images.gitee.com/uploads/images/2019/0422/170754_9e11420a_954087.png "5.png")

二、代码部分

新增JS文件：bootstrap.addtabs.js，bootstrap.js（压缩后的文件名为bootstrap.min.js）

新增CSS文件：bootstrap.addtabs.css

index.html引入对应JS、CSS文件

    
```
<!--[if lte IE 9]>
<link rel="stylesheet" href="assets/css/ace-part2.min.css" class="ace-main-stylesheet" />
<![endif]-->
<link rel="stylesheet" href="assets/css/ace-skins.min.css" />
<link rel="stylesheet" href="assets/css/ace-rtl.min.css" />
<!-- 这里引入bootstrap.addtabs.css -->
<link rel="stylesheet" type="text/css" href="assets/css/bootstrap.addtabs.css"/>
```


    
```
<script type="text/javascript">
if('ontouchstart' in document.documentElement) document.write("<script src='assets/js/jquery.mobile.custom.min.js'>"+"<"+"/script>");
</script>
<!-- 这里原先引入的是压缩后的bootstrap.min.js，修改了源码，引入未压缩的 -->
<script src="assets/js/bootstrap.js"></script>
<!-- 引入bootstrap.addtabs.js -->
<script type="text/javascript" src="assets/js/bootstrap.addtabs.js" ></script>
```


index.html页面结构未变，更改页面元素的样式，其中sidebar部分修改微小，大致如下\
`<ul class="submenu">` 下的\
`<a href="tables.html">` 改为\
`<a class="list-group-item" data-addtab="jqgrid" data-url="mailbox.txt">` 
    
```
    <li class="">
    	<a href="#" class="dropdown-toggle">
		<i class="menu-icon fa fa-list"></i>
		<span class="menu-text"> Tables </span>
 
		<b class="arrow fa fa-angle-down"></b>
	</a>
     
    	<b class="arrow"></b>
     
    	<ul class="submenu">
    		<li class="">
    			<a class="list-group-item" data-addtab="simdyn" data-url="mailbox.txt">
    				<i class="menu-icon fa fa-caret-right"></i>
    				Simple &amp; Dynamic
    			</a>
     
    			<b class="arrow"></b>
    		</li>
     
    		<li class="">
    			<a class="list-group-item" data-addtab="jqgrid" data-url="mailbox.txt">
    				<i class="menu-icon fa fa-caret-right"></i>
    				jqGrid plugin
    			</a>
     
    			<b class="arrow"></b>
    		</li>
    	</ul>
    </li>
```


`<div class="main-content-inner">`标签的代码替换成

    
```
<div class="main-content">
        <div class="main-content-inner content-wrapper" id="content-wrapper">
            <!-- nav tabs -->
            <div class="content-tabs">
                <button class="roll-nav roll-left tabLeft" role="rollleft">
                    <a href="javascript:void(0);" >
    	            <i class="glyphicon glyphicon-fast-backward"></i>
            	</a>
                </button>
    	    <nav class="page-tabs menuTabs tab-ui-menu" id="tab-menu">
    	        <ul class="page-tabs-content nav nav-tabs" style="margin-left: 0px;">
    		    <li role="presentation" id="tab_tab_home1" aria-url="example/ajax/mailbox.txt" aria-ajax="false" class="menu_tab active">
    		        <a href="#tab_home1" aria-controls="tab_home1" role="tab" data-toggle="tab">欢迎页</a>
    			<i class="close-tab glyphicon glyphicon-remove" style="display: none;"></i>
    		    </li>
    		</ul>
    	    </nav>
    	    <button class="roll-nav roll-right tabRight" role="roleright">
    	        <a href="javascript:void(0);">
    		    <i class="glyphicon glyphicon-fast-forward"></i>
    		</a>
    	    </button>
    	    <button class="roll-nav roll-right fullscreen" role = "fullscreen">
    		<i class="glyphicon glyphicon-fullscreen"></i>
    	    </button>
            </div>
            <!-- Tab panes -->
            <div class="tab-content">
                <div class="tab-pane content active" id="tab_home1" role="tabpanel">
                    I'm a homepage.
                </div>
            </div>
        </div>
    </div>
```


配合sidebar点击事件即可产生效果展示中效果
 

    
```
// 给nav-list列表中有data-addtab属性的a标签绑定点击事件
    $(settings.monitor).on('click', '[data-addtab]', function() {
     
    	// sidebar蓝色图标选中效果代码
    	$('.nav-list').find('.active').removeClass('active');
    	$(this).parents("li").addClass('active').siblings('li').removeClass('active');
    	$(this).closest("li").addClass('active').siblings().removeClass('active');
     
    	_click($(this));
    	_scrollToTab(true, (this));
    });
```


三、滚动代码（向左边滚动）

    
```
_scrollTabLeft = function() {
    	var marginLeftVal = Math.abs(parseInt($('.page-tabs-content').css('margin-left')));
    	var tabOuterWidth = _calSumWidth($(".content-tabs").children().not(".menuTabs"));
    	var visibleWidth = $(".content-tabs").outerWidth(true) - tabOuterWidth;
    	var scrollVal = 0;
    	if($(".page-tabs-content").width() < visibleWidth) {
    		return false;
    	} else {
    		var tabElement = $(".menu_tab:first");
    		var offsetVal = 0;
    		while((offsetVal + $(tabElement).outerWidth(true)) <= marginLeftVal) {
    			offsetVal += $(tabElement).outerWidth(true);
    			tabElement = $(tabElement).next();
    		}
    		offsetVal = 0;
    		if(_calSumWidth($(tabElement).prevAll()) > visibleWidth) {
    			while((offsetVal + $(tabElement).outerWidth(true)) < (visibleWidth) && tabElement.length > 0) {
    				offsetVal += $(tabElement).outerWidth(true);
    				tabElement = $(tabElement).prev();
    			}
    			scrollVal = _calSumWidth($(tabElement).prevAll());
    		}
    	}
    	$('.page-tabs-content').animate({
    		marginLeft: 0 - scrollVal + 'px'
    	}, "fast");
    };
```


四、右键代码（部分）

    
```
//关闭右侧
    $('ul.rightMenu a[data-right=remove-right]').on('click', function() {
    	var tab_id = $(this).parent('ul').attr("aria-controls");
    	$('#tab_' + tab_id).nextUntil().each(function() {
    		var id = $(this).attr('id');
    		if(id && id != 'tab_' + tab_id) {
    			$.addtabs.close({
    				"id": $(this).children('a').attr('aria-controls')
    			});
    		}
    	});
    	_scrollToTab(false, $('#tab_' + tab_id))
    	// $.addtabs.drop();
    });
     
    //关闭全部
    $('ul.rightMenu a[data-right=remove-all]').on('click', function() {
    	var tab_id = $(this).parent('ul').attr("aria-controls");
    	$.addtabs.closeAll(null);
    });
     
    $.addtabs.closeAll = function(target) {
    	if(typeof target == 'string') {
    		target = $('body').find(target);
    	} else {
    		target = $('body').find(settings.target)
    	}
     
    	$.each(target.find('li[id]'), function() {
    		var id = $(this).children('a').attr('aria-controls');
    		$("#tab_" + id).remove();
    		$("#" + id).remove();
    	});
    };
```


五、Ace Admin 简介

Ace Admin官网 http://ace.jeka.by/

Ace Admin Git  https://github.com/bopoda/ace

（1）目录结构：简洁明了
![framework](https://images.gitee.com/uploads/images/2019/0422/170821_fe5f0343_954087.png "3.png")

（2）index.html入口页面
![index.html](https://images.gitee.com/uploads/images/2019/0422/165939_4c4f490c_954087.png "2.png")

（3）主要布局\
![layout](https://images.gitee.com/uploads/images/2019/0422/165917_bfa5519e_954087.png "1.png")

1）Navbar导航栏\
2）Sidebar侧边栏\
3）Breadcrumbs面包屑（在“主要内容”中）\
4）Page conten页面内容（在“主要内容”中）\
5）Settings box设置框（在“页面内容”内）\
6) Footer 

六、相关源码：
（3.65元——微信付费）（一年365天，评论留言--邮箱地址）\

![微信付款--weixin pay](https://images.gitee.com/uploads/images/2019/0422/165845_2eb5feea_954087.png "00.png")
