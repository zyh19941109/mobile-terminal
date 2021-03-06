﻿## 移动端

### viewport 视口(可视区窗口)

		默认不设置viewport一般可视区宽度在移动端是980
		
		width  可视区的宽度 (number||device-width)
		
		user-scalable 是否允许用户缩放 (yes||no) iOS10无效
		
		initial-scale 初始缩放比例
		
		minimum-scale 最小缩放比例
		
		maximum-scale 最大缩放比例

### 设备像素比(device pixel ratio)

		设备像素比简称为dpr，其定义了物理像素和设备独立像素的对应关系。它的值可以按下面的公式计算得到：
		
			设备像素比 ＝ 物理像素 / 设备独立像素
			
		在JavaScript中，可以通过window.devicePixelRatio获取到当前设备的dpr

### CSS单位rem

		在W3C规范中是这样描述rem的:
		
		font size of the root element.
		
		简单的理解，rem就是相对于根元素<html>的font-size来做计算。
		而我们的方案中使用rem单位，是能轻易的根据<html>的font-size计算出元素的盒模型大小。

### meta设置

```html
	<!DOCTYPE html>
	<html lang="en">
	<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<meta name="viewport" content="width=device-width,user-scalable=no">
	<!--QQ强制竖屏显示-->
	<meta name="x5-orientation" content="portrait" />
	<!--QQ全屏显示-->
	<meta name="x5-fullscreen" content="true" />
	<!--UC强制竖屏显示-->
	<meta name="screen-orientation" content="portrait">
	<!--UC全屏显示-->
	<meta name="full-screen" content="yes">
	<!--禁止识别电话号码和邮箱地址-->
	<meta name="format-detection" content="telephone=no, email=no" />
	<!--页面适合在pc和mobile上进行浏览-->
	<meta name="applicable-device" content="pc, mobile">
	<!--针对Apple是否启动WebApp功能-->
	<meta name="apple-mobile-web-app-capable" content="yes">
	<!--Apple顶部导航栏颜色-->
	<meta name="apple-mobile-web-app-status-bar-style" content="black">
	</head>
	<body>
		<!--
			href="tel:"
			href="mailto:"
		-->
		<p>13888888888</p>
		<a href="tel:18888888888">请拨打电话18888888888</a>
		<a href="mailto:zyh@qq.com">请发送邮件</a>
	</body>
	</html>
```

### 默认样式设置

```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="UTF-8">
    <title>Document</title>
    <meta name="viewport" content="width=device-width,user-scalable=no">
    <style type="text/css">
    /*默认字体设置*/
    body {
    	font-family: Helvetica;
    }
    body * {
    	/*禁止文字缩放*/
    	-webkit-text-size-adjust: 100%;
    	/*选中文字设置*/
    	-webkit-user-select: none; 
    }
    /*清除点击阴影*/
    a,input,button {
    	-webkit-tap-highlight-color: rgba(0, 0, 0, 0);
    	/*-webkit-tap-highlight-color: transparent*/
    }
    /*消除圆角按钮（针对iOS系统）*/
    input,button {
    	-webkit-appearance: none;
    	border-radius: 0;
    }
    </style>
    </head>
    <body>
    	<a href="http://www.baidu.com">百度一下</a>
    	<input type="button" value="按钮">
    </body>
    </html>
```

### 动态获取像素比（取自--饿了么移动端）

```javascript
	!function(e, t) {
		function i() {
			var t = n.getBoundingClientRect().width;
			t / d > 540 && (t = 540 * d);
			var i = t / 10;
			n.style.fontSize = i + "px", p.rem = e.rem = i
		}
		var a, r = e.document,
			n = r.documentElement,
			o = r.querySelector('meta[name="viewport"]'),
			l = r.querySelector('meta[name="flexible"]'),
			m = r.querySelector('meta[name="flexible-in-x5"]'),
			s = !0,
			d = 0,
			c = 0,
			p = t.flexible || (t.flexible = {});
		if (o) {
			console.warn("将根据已有的meta标签来设置缩放比例");
			var u = o.getAttribute("content").match(/initial\-scale=([\d\.]+)/);
			u && (c = parseFloat(u[1]), d = parseInt(1 / c))
		} else if (l) {
			var f = l.getAttribute("content");
			if (f) {
				var v = f.match(/initial\-dpr=([\d\.]+)/),
					h = f.match(/maximum\-dpr=([\d\.]+)/);
				v && (d = parseFloat(v[1]), c = parseFloat((1 / d).toFixed(2))), h && (d = parseFloat(h[1]), c = parseFloat((1 / d).toFixed(2)))
			}
		}
		if (m && (s = "false" !== m.getAttribute("content")), !d && !c) {
			var x = (e.navigator.appVersion.match(/android/gi), e.chrome),
				g = e.navigator.appVersion.match(/iphone/gi),
				b = e.devicePixelRatio,
				w = /TBS\/\d+/.test(e.navigator.userAgent),
				y = !1;
			try {
				y = "true" === localStorage.getItem("IN_FLEXIBLE_WHITE_LIST")
			} catch (e) {
				y = !1
			}
			d = g || x || w && s && y ? b >= 3 && (!d || d >= 3) ? 3 : b >= 2 && (!d || d >= 2) ? 2 : 1 : 1, c = 1 / d
		}
		if (n.setAttribute("data-dpr", d), !o) if (o = r.createElement("meta"), o.setAttribute("name", "viewport"), o.setAttribute("content", "initial-scale=" + c + ", maximum-scale=" + c + ", minimum-scale=" + c + ", user-scalable=no"), n.firstElementChild) n.firstElementChild.appendChild(o);
		else {
			var E = r.createElement("div");
			E.appendChild(o), r.write(E.innerHTML)
		}
		e.addEventListener("resize", function() {
			clearTimeout(a), a = setTimeout(i, 300)
		}, !1), e.addEventListener("pageshow", function(e) {
			e.persisted && (clearTimeout(a), a = setTimeout(i, 300))
		}, !1), "complete" === r.readyState ? r.body.style.fontSize = 12 * d + "px" : r.addEventListener("DOMContentLoaded", function(e) {
			r.body.style.fontSize = 12 * d + "px"
		}, !1), i(), p.dpr = e.dpr = d, p.refreshRem = i, p.rem2px = function(e) {
			var t = parseFloat(e) * this.rem;
			return "string" == typeof e && e.match(/rem$/) && (t += "px"), t
		}, p.px2rem = function(e) {
			var t = parseFloat(e) / this.rem;
			return "string" == typeof e && e.match(/px$/) && (t += "rem"), t
		}
	}(window, window.lib || (window.lib = {}))
```

### 像素比简单设置

		//获取像素比
		var PixelRatio = 1 / window.devicePixelRatio;
		//写入meta
		document.write('<meta name="viewport" content="width=device-width,initial-scale='+PixelRatio+',minimum-scale='+PixelRatio+',maximum-scale='+PixelRatio+',user-scalable=no" />');
		//获取html元素
		var html = document.getElementsByTagName('html')[0];
		//获取html（屏幕）的宽度
		var pageWidth = html.getBoundingClientRect().width;
		//获取html的字号
		html.style.fontSize = pageWidth / 10 + 'px';

### 弹性盒模型

> 主轴和侧轴的样式都需要加在元素的父级，而元素的位置设置只需要加在自身即可（别忘了父级要加display: flex;）

		主轴方向设置
		
			/*新版弹性盒模型*/
			display: flex;
			
			/*设置主轴方向为水平方向*/
			/*flex-direction: row;*/
			
			/*设置主轴方向为垂直方向*/
			/*flex-direction: column;*/
			
			
			/*老版弹性盒模型*/
			display: -webkit-box;
			
			/*设置主轴方向为水平方向*/
			-webkit-box-orient: horizontal;
			
			/*设置主轴方向为垂直方向*/
			/*-webkit-box-orient: vertical;*/
		
		主轴上元素排列方向
		
			/*新版弹性盒模型*/
			/*display: flex;*/
			
			/*设置主轴方向为水平,元素排列为反序*/
			/*flex-direction: row-reverse;*/
			
			/*设置主轴方向为垂直,元素排列为反序*/
			/*flex-direction: column-reverse;*/
			
			
			/*老版弹性盒模型*/
			display: -webkit-box;
			
			/*元素在主轴上排列为反序*/
			/*-webkit-box-direction: reverse;*/
			
			/*元素在主轴上排列为正序*/
			/*-webkit-box-direction: normal;*/
		
		主轴上富裕空间管理
		
			/*新版弹性盒模型*/
			display: flex;
			
			/*元素在主轴开始位置 ，富裕空间在主轴的结束位置*/
			/*justify-content: flex-start;*/
			
			/*元素在主轴结束位置，富裕空间在主轴开始位置*/
			/*justify-content: flex-end;*/
			
			/*元素在主轴中间,富裕空间在主轴两侧*/
			/*justify-content: center;*/
			
			/*富裕空间平均分配在每两个元素之间*/
			/*justify-content: space-between;*/
			
			/*富裕空间平均分配在每个元素两侧*/
			/*justify-content: space-around;*/
			
			
			/*老版弹性盒模型*/
			display: -webkit-box;
			
			/*元素在主轴的开始位置,富裕空间在主轴的结束位置*/
			/*-webkit-box-pack: start;*/
			
			/*元素在主轴结束位置，富裕空间在主轴开始位置*/
			/*-webkit-box-pack: end;*/
			
			/*元素在主轴中间,富裕空间在主轴两侧*/
			/*-webkit-box-pack: center;*/
			
			/*富裕空间平均分配在每两个元素之间*/
			/*-webkit-box-pack: justify;*/
		
		侧轴上富裕空间管理
		
			/*新版弹性盒模型*/
			/*display: flex;*/
			
			/*元素在侧轴开始位置，富裕空间在侧轴结束位置*/
			/*align-items: flex-start;*/
			
			/*元素在侧轴结束位置，富裕空间在侧轴开始位置*/
			/*align-items: flex-end;*/
			
			/*元素在侧轴中间位置，富裕空间在侧轴两侧*/
			/*align-items: center;*/
			
			/*根据侧轴方向上文字的基线对齐*/
			/*align-items: baseline;*/
			
			
			/*老版弹性盒模型*/
			/*display: -webkit-box;*/
			
			/*元素在侧轴开始位置，富裕空间在侧轴结束位置*/
			/*-webkit-box-align: start;*/
			
			/*元素在侧轴结束位置，富裕空间在侧轴开始位置*/
			/*-webkit-box-align: end;*/
			
			/*元素在侧轴中间位置，富裕空间在侧轴两侧*/
			/*-webkit-box-align: center;*/
		
		元素弹性空间
		
			/*新版*/
			/*flex-grow: 1;*/
			
			/*老版*/
			-webkit-box-flex:1;
		
		元素具体位置设置
		
			/* 数值越小越靠前，可以接受0或者负值 */
			/*order:5;*/
			
			/* 数值越小越靠前， 最小值默认处理为1*/
			/*-webkit-box-ordinal-group:-2;*/

### 移动端事件

		手指按下：
			ontouchstart
			
		手指移动：
			ontouchmove
			
		手指抬起：
			ontouchend
			
		注意：
			在移动端开发的时候，浏览器的模拟器时好时坏，
			一般不用on的方式绑定事件函数，要用事件绑定的方式(addEventListener)
		
		移动端click屏幕产生200-300ms的延迟响应
		
			移动设备上的web网页是有300ms延迟的，往往会造成按钮点击延迟甚至是点击失效。
		
		以下是历史原因：
		
			2007年苹果发布首款iphone上IOS系统搭载的safari为了将适用于PC端上大屏幕的网页能比较好的展示在手机端上，
			使用了双击缩放(double tap to zoom)的方案，比如你在手机上用浏览器打开一个PC上的网页，
			你可能在看到页面内容虽然可以撑满整个屏幕，但是字体、图片都很小看不清，此时可以快速双击屏幕上的某一部分，
			你就能看清该部分放大后的内容，再次双击后能回到原始状态。
			
			双击缩放是指用手指在屏幕上快速点击两次，iOS自带的 Safari浏览器会将网页缩放至原始比例。
			
			原因就出在浏览器需要如何判断快速点击上，当用户在屏幕上单击某一个元素时候，例如跳转链接<a href="#"></a>，
			此处浏览器会先捕获该次单击，但浏览器不能决定用户是单纯要点击链接还是要双击该部分区域进行缩放操作，
			所以，捕获第一次单击后，浏览器会先Hold一段时间t，如果在t时间区间里用户未进行下一次点击，
			则浏览器会做单击跳转链接的处理，如果t时间里用户进行了第二次单击操作，则浏览器会禁止跳转，
			转而进行对该部分区域页面的缩放操作。那么这个时间区间t有多少呢？在IOS safari下，大概为300毫秒。
			这就是延迟的由来。造成的后果用户纯粹单击页面，页面需要过一段时间才响应，给用户慢体验感觉，
			对于web开发者来说是，页面js捕获click事件的回调函数处理，需要300ms后才生效，也就间接导致影响其他业务逻辑的处理。

### 触摸事件的响应顺序

		1、ontouchstart 
		2、ontouchmove 
		3、ontouchend 
		4、onclick
		
		解决300ms延迟的问题，也可以通过绑定ontouchstart事件，加快对事件的响应

### 移动端的点透

		当上层元素发生点击的时候，下层元素也有点击（焦点）特性，
		在300ms之后，如果上层元素消失或者隐藏，目标点就会“漂移”到下层元素身上，就会触发点击行为。
			
		解决：
		
			1.下层不要使用有点击（焦点）特性的元素
			
			2.阻止pc事件

### 移动端阻止PC事件优点

		document.addEventListener('touchstart',function(ev){
			ev.preventDefault();
		});
		
		
		1.IOS10下设置meta禁止用户缩放是不可行的。（使用阻止pc事件就可以在IOS10下禁止用户缩放）
			
		2.解决IOS10下溢出隐藏的问题。
		
		3.禁止系统默认的滚动条、阻止橡皮筋效果
		
		4.禁止长按选中文字、选中图片、系统默认菜单
		
		5.解决点透问题
		
		6.也阻止了焦点元素的焦点行为(要正常使用：ev.stopPropagation()阻止冒泡)

### 移动端事件对象

		当给某个元素加上了事件绑定函数之后，事件函数默认的第一个参数就是事件对象
		
		事件对象：
			当用户在浏览器下触发了某个行为，事件对象会记录用户操作时一些细节信息。
		
		touches 当前位于*屏幕*上的所有手指的一个列表
		
		targetTouches 位于当前DOM元素上的手指的一个列表
		
		changedTouches  涉及当前事件的手指的一个列表
		
		div.addEventListener('touchmove',start);
		function start(ev){
			//this.innerHTML = ev.touches.length;
			//this.innerHTML = ev.targetTouches.length;
			this.innerHTML = ev.changedTouches.length;
		}

### 移动端input无法获取焦点的问题

在移动端开发中，我们有时有针对性的写一些特殊的重置，比如：

		* {
			-webkit-touch-callout: none;
			//阻止长按图片之后呼出菜单提示复制的行为
			
			-webkit-text-size-adjust: none;
			//禁用Webkit内核浏览器的文字大小调整功能
			
			-webkit-tap-highlight-color: rgba(0, 0, 0, 0);
			//避免点击a标签或者注册了click事件的元素时产生高亮
			
			-webkit-user-select: none;
			//禁止用户进行复制、选择
		}
		
		其中，-webkit-user-select: none;会产生一些问题，这是webkit内核浏览器下的一个bug
		阻止了用户的选择内容行为，会导致一些“内容可编辑”标签无法正常使用，比如input、textarea
		
		如果网站不需要阻止用户的选择内容的行为就可以使用如下样式：
		
			* {
				-webkit-user-select: text;
				-user-select: text;
			}
			
			另一种方式：
			*: not(input, textarea) {
				-webkit-touch-callout: none;
				-webkit-user-select: none;
			}
			
		user-select可能会导致与contenteditable = "true"元素的问题，以便更好的使用，所以下面的CSS也要加上
		
			HTML5全局 contenteditable属性
				contenteditable 属性规定是否可编辑元素的内容
		
			[contenteditable="true"] , input, textarea {
				-webkit-user-select: auto !important;
				-khtml-user-select: auto !important;
				-moz-user-select: auto !important;
				-ms-user-select: auto !important;
				-o-user-select: auto !important;
				user-select: auto !important;
			}

### 表单输入框placeholder的颜色值

		input::-webkit-input-placeholder{color:#ccc;}
		input:focus::-webkit-input-placeholder{color:#ddd;}

### 模拟按钮hover效果

		直接在body上添加ontouchstart，同样可激活移动端css的active效果
		移动端触摸按钮的效果，可明示用户有些事情正要发生，是一个比较好体验，但是移动设备中并没有鼠标指针，
		使用css的hover并不能满足我们的需求，还好国外有个激活移动端css的active效果

> 第一种

```html
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="utf-8">
	<meta content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no" name="viewport">
	<meta content="yes" name="apple-mobile-web-app-capable">
	<meta content="black" name="apple-mobile-web-app-status-bar-style">
	<meta content="telephone=no" name="format-detection">
	<meta content="email=no" name="format-detection">
	<style type="text/css">
		a{-webkit-tap-highlight-color: rgba(0,0,0,0);}
		.btn-blue{display:block;height:42px;line-height:42px;text-align:center;border-radius:4px;font-size:18px;color:#FFFFFF;background-color: #4185F3;}
		.btn-blue:active{background-color: #357AE8;}
	</style>
	</head>
	<body ontouchstart>
		<div class="btn-blue">按钮</div>
	</body>
	</html>
```

> 第二种

```html
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="utf-8">
	<meta content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no" name="viewport">
	<meta content="yes" name="apple-mobile-web-app-capable">
	<meta content="black" name="apple-mobile-web-app-status-bar-style">
	<meta content="telephone=no" name="format-detection">
	<meta content="email=no" name="format-detection">
	<style type="text/css">
		a{-webkit-tap-highlight-color: rgba(0,0,0,0);}
		.btn-blue{display:block;height:42px;line-height:42px;text-align:center;border-radius:4px;font-size:18px;color:#FFFFFF;background-color: #4185F3;}
		.btn-blue:active{background-color: #357AE8;}
	</style>
	</head>
	<body>
		<div class="btn-blue">按钮</div>
	<script type="text/javascript">
	document.addEventListener("touchstart", function(){}, true)
	</script>
	</body>
	</html>
```

> 兼容性ios5+、部分android 4+、winphone 8

> 第三种

> 要做到全兼容的办法，可通过绑定ontouchstart和ontouchend来控制按钮的类名

```html
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="utf-8">
	<meta content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no" name="viewport">
	<meta content="yes" name="apple-mobile-web-app-capable">
	<meta content="black" name="apple-mobile-web-app-status-bar-style">
	<meta content="telephone=no" name="format-detection">
	<meta content="email=no" name="format-detection">
	<style type="text/css">
		a{-webkit-tap-highlight-color: rgba(0,0,0,0);}
		.btn-blue{display:block;height:42px;line-height:42px;text-align:center;border-radius:4px;font-size:18px;color:#FFFFFF;background-color: #4185F3;}
		.btn-blue-on{background-color: #357AE8;}
	</style>
	</head>
	<body>
		<div class="btn-blue">按钮</div>
	<script type="text/javascript">
	var btnBlue = document.querySelector(".btn-blue");
	btnBlue.ontouchstart = function(){
		this.className = "btn-blue btn-blue-on"
	}
	btnBlue.ontouchend = function(){
		this.className = "btn-blue"
	}
	</script>
	</body>
	</html>
```

### 禁止ios长按时不触发系统的菜单，禁止ios&android长按时下载图片

		.css{-webkit-touch-callout: none}

### 禁止ios和android用户选中文字

		.css{-webkit-user-select:none}

### 打电话发短信写邮件

		打电话
		<a href="tel:10086">打电话给:10086</a>
		
		发短信
		<a href="sms:10086">发短信给:10086</a>
		
		写邮件
		<a href="mailto:zyh@qq.com">zyh@qq.com</a>

### 实现0.5px的线条

		网络上有很多方法，如设置viewport，box-shawdow，border-image，background-image，transform:scale等，
		本文只介绍一种觉得比较好用的方法，一来兼容性好，二来不依赖图片。
		
		transform:scale(x,y)
		
		通过css支持定义border或者height为.5px大的线条，在android设备中的无法显示出来，这里有个小技巧，
		设置线条为1px，然后通过transform:scale(x,y)来缩放线条为原来的一半，可显示0.5px的线条。

```html
	<!DOCTYPE html>
	<html>
		<head>
		<meta charset="utf-8">
		<meta content="width=device-width,initial-scale=1,maximum-scale=1.0,user-scalable=no" name="viewport">
		<meta content="yes" name="apple-mobile-web-app-capable">
		<meta content="black" name="apple-mobile-web-app-status-bar-style">
		<meta content="telephone=no" name="format-detection">
		<meta content="email=no" name="format-detection">
		<title>点5测试 - scale</title>
		<style type="text/css">
		.line {
		    height: 50px;
		    line-height: 50px;
		    background-color: #CCC;
		    border-bottom:1px solid red
		} 
		.scale {
		    position: relative;
		    height: 50px;
		    line-height: 50px;
		    background-color: #CCC
		}
		.scale:after {
		    position: absolute;
		    content: '';
		    width: 100%;
		    left: 0;
		    bottom: 0;
		    height: 1px;
		    background-color: red;
		    -webkit-transform: scale(1,.5);
		    transform: scale(1,.5);
		    -webkit-transform-origin: center bottom;
		    transform-origin: center bottom
		}
		</style>
		</head>
		<body>
			<div class="line">1px</div>
			<br/><br/>    
			<div class="scale">0.5px</div>
		</body>
	</html>
```

### 实现0.5px的圆角边框

		原理：先定义1px的圆角边框，然后拉伸内容的宽度和高度为父级的2倍(边框厚度不变)，
		然后再使用transform:scale(0.5)缩放为原来的0.5倍

```html
	<!DOCTYPE html>
	<html>
		<head>
		<meta charset="utf-8">
		<meta content="width=device-width,initial-scale=1,maximum-scale=1.0,user-scalable=no" name="viewport">
		<meta content="yes" name="apple-mobile-web-app-capable">
		<meta content="black" name="apple-mobile-web-app-status-bar-style">
		<meta content="telephone=no" name="format-detection">
		<meta content="email=no" name="format-detection">
		<title>点5测试 - border-radius</title>
		<style type="text/css">
		body{padding: 50px;-webkit-touch-callout:none;}
		[class*="btn"]{margin: 0 auto;}
		.btn-1 {
		    width: 200px;
		    height: 42px;
		    -webkit-border-radius: 5px;
		    border-radius: 5px;
		    text-align: center;
		    line-height: 42px;
		    background-color: #EDEDED;
		    border: 1px solid red;
		}
		.btn {
		    position: relative;
		    width: 200px;
		    height: 42px;
		    -webkit-border-radius: 5px;
		    border-radius: 5px;
		    text-align: center;
		    line-height: 42px;
		    background-color: #EDEDED;
		}
		.btn:before {
		    content: '';
		    position: absolute;
		    top: -50%;
		    bottom: -50%;
		    left: -50%;
		    right: -50%;
		    -webkit-transform: scale(0.5);
		    transform: scale(0.5);
		    border-style: solid;
		    border-width: 1px;
		    border-color: red;
		    -webkit-border-radius: 10px;
		    border-radius: 10px;
		}
		</style>
		</script>
		</head>
		<body>
			<div class="btn-1">1px border</div>
			<br/><br/>
			<div class="btn">.5px border</div>
		</body>
	</html>
```

### CSS实现隐藏滚动条同时又可以滚动

		移动端页面为了更接近原生的体验，是否可以隐藏滚动条，同时又保证页面可以滚动？
		
		使用 overflow:hidden 隐藏滚动条，但存在的问题是：页面或元素失去了滚动的特性。
		由于只需要兼容移动浏览器（Chrome 和 Safari），于是想到了自定义滚动条的伪对象选择器
		::-webkit-scrollbar。
		
		应用如下 CSS 可以隐藏滚动条：
			//.element就是指出现滚动条的元素
			.element::-webkit-scrollbar {display:none}
		
		如果要兼容PC其他浏览器（IE、Firefox等），国外一位才人John Kurlak也研究出了一种办法。
		在容器外面再嵌套一层 overflow:hidden内部内容再限制尺寸和外部嵌套层一样，就变相隐藏了。
		
		<div class="outer-container">
		    <div class="inner-container">
		        <div class="content">
		        	......
		        </div>
		    </div>
		</div>
		.outer-container,.content {
		    width: 200px; height: 200px;
		}
		.outer-container {
		    position: relative;
		    overflow: hidden;
		}
		.inner-container {
		    position: absolute; 
		    left: 0;
		    overflow-x: hidden;
		    overflow-y: scroll;
		}
		 
		 /* for Chrome */
		.inner-container::-webkit-scrollbar {
		    display: none;
		}

### Rem布局原理

		其实rem布局的本质是等比缩放，一般是基于宽度
		
		假设我们将屏幕宽度平均分成100份，每一份的宽度用x表示，x = 屏幕宽度 / 100，
		如果将x作为单位，x前面的数值就代表屏幕宽度的百分比
		
		p {width: 50x} /* 屏幕宽度的50% */
		
		如果想要页面元素随着屏幕宽度等比变化，我们需要上面的x单位，不幸的是css中并没有这样的单位，
		幸运的是在css中有rem，通过rem这个桥梁，可以实现神奇的x
		
		通过上面对rem的介绍，可以发现，如果子元素设置rem单位的属性，
		通过更改html元素的字体大小，就可以让子元素实际大小发生变化
		
		html {font-size: 16px}
		p {width: 2rem} /* 32px*/
		
		html {font-size: 32px}
		p {width: 2rem} /*64px*/
		
		如果让html元素字体的大小，等于屏幕宽度的1/100，那1rem和1x就等价了
		
		html {fons-size: width / 100}
		p {width: 50rem} /* 50rem = 50x = 屏幕宽度的50% */
		
		如何让html字体大小一直等于屏幕宽度的百分之一呢？ 可以通过js来设置，一般需要在页面dom ready、resize和屏幕旋转中设置
		
		document.documentElement.style.fontSize = document.documentElement.clientWidth / 100 + 'px';
		
		那么如何把UE图中的获取的像素单位的值，转换为已rem为单位的值呢？公式是元素宽度 / UE图宽度 * 100
		
		假设UE图尺寸是640px，UE图中的一个元素宽度是100px，根据公式100/640*100 = 15.625
		
		p {width: 15.625rem}

### 比Rem更好的方案

		上面提到想让页面元素随着页面宽度变化，需要一个新的单位x，x等于屏幕宽度的百分之一，css3带来了rem的同时，也带来了vw和vh
		
			vw —— 视口宽度的 1/100
			vh —— 视口高度的 1/100
			
		聪明的你也许一经发现，这不就是单位x吗，没错根据定义可以发现1vw=1x，有了vw我们完全可以绕过rem这个中介了，
		下面两种方案是等价的，可以看到vw比rem更简单
		
			/* rem方案 */
			html {fons-size: width / 100}
			p {width: 15.625rem}
			
			/* vw方案 */
			p {width: 15.625vw}
			vw还可以和rem方案结合，这样计算html字体大小就不需要用js了
			
			html {fons-size: 1vw} /* 1vw = width / 100 */
			p {width: 15.625rem}
			虽然vw各种优点，但是vw也有缺点，首先vw的兼容性不如rem好，使用之前要看下
		
		兼容性	IOS	安卓
		rem	4.1+	2.1+
		vw	6.1+	4.4+
		另外，在使用弹性布局时，一般会限制最大宽度，比如在pc端查看我们的页面，此时vw就无法力不从心了，
		因为除了width有max-width，其他单位都没有，而rem可以通过控制html根元素的font-size最大值，而轻松解决这个问题

### H5禁止手机虚拟键盘弹出

		在移动端，input会默认触发手机的虚拟键盘，如何阻止手机虚拟键盘弹起呢？
		目前我试过有两个方案，一个是给input添加readonly属性，
		另一个就是在input事件处理方法前面添加一句document.activeElement.blur()
		
		readonly
		
			使用readonly方式来阻止虚拟键盘弹出应该是最简单最优雅的方式了。
			readonly属性规定输入字段为只读。只读字段是不能修改的。
			不过，用户仍然可以使用 tab键切换到该字段，还可以选中或拷贝其文本。
			
			值得一提的是它的取值，只要声明了readonly属性，不管取什么值都可以，
			比如readonly=""、readonly="readonly"、readonly="abc"都是一样的
			
			优点：简单
			缺点：在iOS的Safari中无效（未做更多情况测试）
		
		document.activeElement.blur()
		
			这是个什么玩意儿？document.activeElement是一个Web API接口。
			MDN上的解释是：它返回当前页面中获得焦点的元素，也就是说，如果此时用户按下了键盘上某个键，
			会在该元素上触发键盘事件，该属性是只读的。
		
			document.activeElement属性始终会引用DOM中当前获得了焦点的元素。
			元素获得焦点的方式有用户输入(通常是按Tab键)、在代码中调用focus()方法和页面加载。
			
			它里面有很多方法，在浏览器控制台查看，可以看到有很多方法
			
			MDN上还展示了一个有意思的示例，看这里 http://jsfiddle.net/w9gFj/
			
			那么document.activeElement.blur()为什么可以阻止虚拟键盘弹出呢？
			原因是：当你点击input的时候，document.activeElement获得了DOM中被聚焦的元素，
			也就是你点击的input，而调用.blur()方法，blur我相信大家都知道吧，就是取消聚焦。
			获得被聚焦的元素然后强制blur以达到没有聚焦的样子
			
			优点：支持Android、iOS
			缺点：需要添加额外的JS代码
		
			这句代码加在什么地方？加入有如下HTML
			
				<div class="calendar">
				    <div>
				        <input type="text" id="datePicker" placeholder="点击选择日期"/>
				    </div>
				</div>
				
			那么这句JS加在事件处理方法中
		
				$("#datePicker").focus(function(){
				    document.activeElement.blur();
				});
				
		就现在来说，用document.activeElement.blur()确实是在绕弯子，直接使用readonly是最佳方案。
		但是document.activeElement很强大，可以做很多事。

### 只有一个头部和内容不确定的高度，怎样实现头部固定和内容滚动？

		只是头部绝对定位，内容区不使用绝对定位来解决
		
			body{
				height:100%;
				overflow:hidden;
			}
			header{
				position:absolute;
				top:0;
				left:0;
				right:0;
				height:80px;
			}
			content{
				padding:80px 0 20px;
				height:100%;
				overflow-x:hidden;
				overflow-y:scroll;
				box-sizing:border-box;
			}
			
		分析：
			
			body设置固定显示区域（类似划个固定的区域框选子元素），header可以用绝对定位，内容区高度是不确定的，
			所以用上padding来设置内容的显示区域（左右下padding可适当添加），但是给内容加上height:100%的同时，
			一定要加上box-sizing:border-box，要不然内容区的高度就不是100%了

### 多行文本溢出

		单行文本溢出，对title类的使用非常多，而多行文本类，在详情介绍则用的比较多。
		
		/*单行文本溢出*/
		.inaline{
			overflow: hidden;
			white-space: nowrap;
			text-overflow: ellipsis;
		}
		
		/*多行文本溢出*/
		.intwoline{
			display: -webkit-box !important;
			overflow: hidden;
			text-overflow: ellipsis;
			word-break: break-all;
			-webkit-box-orient:vertical;
			-webkit-line-clamp:4;
		}
		属性-webkit-line-clamp就可以控制几行溢出，4就是第四行进行截断加...

### 移动端遇上表单元素底部被顶起

		@media (max-height:400px){
			element{
				display: none;
			}
		}
		
		分辨率高度小于等于设置值的时候识别
		
		一般设置为400px，但也不排除有些键盘高低不一致的情况，可根据情况适当修改

### 当input遇见了全屏背景

		在移动端开发过程中，有极少数使用全屏背景的情况。当input遇见了全屏背景，一切便不再美好。
		
		解决办法：
		
			let viewH = document.documentElement.clientHeight;
			document.getElementsByTagName('html')[0].style.cssText += `height:${viewH}px`;
			document.body.style.cssText += `height:${viewH}px`;

> 补充可视区宽高小知识

		可视区宽度
		
			document.body.clientWidth
			document.documentElement.clientWidth
		
		可视区高度
		
			document.documentElement.clientHeight
		
		仔细一看可视区高度怎么和可视区宽度少了一个呢？
		
		其实不难理解，body本身块元素，宽度自然撑满可视区宽度
		
		而高度并非如此，body自身的高度则根据子元素的高度来撑
		
		所以document.body.clientHeight指的就是body自身的宽度

### 移动网页调试神器vConsole以及Eruda使用技巧

		做移动端Web开发的一大痛点就是，在真机运行下无法查看console.log日志和其他信息如网络请求、显示本地存储等信息
		
		如果网页是运行在手机浏览器中还算好，可以把网址在电脑上打开查看console信息
		
		但是如果是做APP的内嵌H5页面，那就只能靠开发阶段在浏览器模拟环境中尽量没有Bug
		
		但是，一旦H5上线后报错那就比较麻烦了，而且还依赖APP环境才能跑的网页，更加难以查找问题
		
		如果让移动端也拥有类似Chrome DevTools工具那岂不是很愉快么？
		
		vConsole便是这样一款很棒的移动端DevTools工具，由大厂企鹅出品。

[vConsole github地址](https://github.com/Tencent/vConsole)

> Eruda

		Eruda是谁？
		
		Eruda 是一个专为手机网页前端设计的调试面板，类似 DevTools 的迷你版
		
		其主要功能包括：
		
		捕获 console 日志、检查元素状态、显示性能指标、捕获XHR请求、显示本地存储和 Cookie 信息、浏览器特性检测等等。

[集颜值技能于一身的Erdua](https://github.com/liriliri/eruda)

[使用技巧](https://github.com/liriliri/eruda/blob/master/doc/README_CN.md)

> Erdua的基本使用方法推荐使用CDN和可配置参数的形式，在页面引入如下代码：

```javascript
	;(function () {
	    var src = '//cdn.bootcss.com/eruda/1.2.4/eruda.min.js';
	    if (!/eruda=true/.test(window.location) && localStorage.getItem('active-eruda') !== 'true') return;
	    document.write('<scr' + 'ipt src="' + src + '"></scr' + 'ipt>');
	    document.write('<scr' + 'ipt>eruda.init();</scr' + 'ipt>');
	})();
	eruda.init();//里面可以传入要初始化哪些面板，默认加载所有
```

		这样使用方式没有错，但是如果Eruda要跟着发布到线上的话，那我们要删除这段代码？
		
		因为这样会不管你需不需要调试Eruda都会立即加载，在页面出现Eruda的图标
		
		我的目标是，Eruda可以保留在页面里，无论什么环境，只要我们想呼唤它出现的时候它才出现
		
		不需要它的时候它只是一段不会生效的普通代码


> 办法

		首先把上述引入代码的src放到if里，同时把localStorage改为sessionStorage
		
		active-eruda参数也可以改个更短的名字，改后的代码如下：

```javascript
	;(function () {
	    if (!/eruda=true/.test(window.location) || sessionStorage.getItem('eruda') !== 'true') return;
	    var src = '//cdn.bootcss.com/eruda/1.4.3/eruda.min.js';
	    document.write('<scr' + 'ipt src="' + src + '"></scr' + 'ipt>');
	    document.write('<scr' + 'ipt>eruda.init();</scr' + 'ipt>');
	})();
```

		这段代码的意思是如果URL中有参数eruda=true或者sessionStorage中eruda的值为true才加载Eruda
		
		这样的好处是，我们需要调试的时候可以在网页URL后面加个参数即可，不需要调试的它不会加载
		
		然而，这在开发阶段可以这样做比较好，但是在线上环境可能要加URL参数比较麻烦
		
		于是我想到了在页面中点击某个元素10次再加载Eruda，点击10次或者更多次，然后在sessionStorage中写入eruda=true
		
		然后刷新当前页。相反，再点击10次关闭Eruda。用这样比较隐藏的方式开启或关闭Eruda
		
		这样线上环境也可以自由开启或关闭Eruda了

> 例子：假如有这样的一个页面，里有一个标题文字

		<h2>——规则详情——</h2>
		<div>
		.....
		</div>
		那么我们可以在h2标签上绑定一个click事件，加入方法名叫showEruda

```javascript
	<h2 onclick="showEruda">——规则详情——</h2>
	<div>
	.....
	</div>
	<script>
	var count = 0;
	function showEruda () {
	    if (count >= 10) {    
	        var eruda = sessionStorage.getItem('eruda');
	        if (!eruda || eruda === 'false'){
	            sessionStorage.setItem('eruda', 'true')
	        } else {
	            sessionStorage.setItem('eruda', 'false')
	        }
	        location.reload()
	    }
	    count++
	}
	</script>
```

[原文链接地址，请戳我](https://blog.dunizb.com/2017/10/16/erdua/)

### -webkit-overflow-scrolling: touch

		在ios设备中，利用 overflow 来模拟滚动会出现卡顿的情况
		
		可以通过设置 -webkit-overflow-scrolling: touch; 来解决
		
		原因是设置后ios会为其创建一个 UIScrollView ，利用硬件来加速渲染

### 1. 监听屏幕旋转变化接口: orientationchange

**定义：**

这个 `API` 可以检测手机是否横屏的情况。

**使用：**

```javascript
  //检测屏幕旋转角度
  screenOrientation() {
    let orientation = screen.orientation || screen.mozOrientation || screen.msOrientation;
    window.addEventListener("onorientationchange" in window ? "orientationchange" : "resize", _ => {
      this.angle = orientation.angle;
    });
  },
```

| orientation.angle 值 | 屏幕方向 |
| --- | --- |
| 0 | 竖屏 |
| 90 | 向左横屏 |
| -90/270 | 向右横屏 |
| 180 | 倒屏 |

> 通过这个 `API` 我们在横屏和竖屏的时候可以添加一些动作或者是样式的改变。

如果只是样式的改变也可以通过媒体查询来监听：

```css
  /* 竖屏 */
  @media screen and (orientation: portrait) {
    // css code
  }
      
  /* 横屏 */
  @media screen and (orientation: landscape) {
    // css code
  }
```
### 2. 电池状态：navigator.getBattery()

**定义：**

这个 `API` 可以检测手机电池状态的情况。

> 这个 `api` 返回的是一个 `promise` 对象，会给出一个 `BatteryManager` 对象，对象中包含了以下信息：

| key | 描述 | 备注 |
| --- | --- | --- |
| charging | 是否在充电 | 可读属性 |
| chargingTime | 若在充电，还需充电时间 | 可读属性 |
| dischargingTime | 剩余电量 | 可读属性 |
| level | 剩余电量百分数 | 可读属性 |
| onchargingchange | 监听充电状态的改变 | 可监听事件 |
| onchargingtimechange | 监听充电时间的改变 | 可监听事件 |
| ondischargingtimechange | 监听电池可用时间的改变 | 可监听事件 |
| onlevelchange | 监听剩余电量百分数的改变 | 可监听事件 |

**使用：**

```javascript
  //检测是否充电以及电量
  getBatteryInfo() {
    if (navigator.getBattery) {
      navigator.getBattery().then(battery => {
        // 判断是否在充电
        this.batteryInfo = battery.charging ? `在充电 : 剩余 ${battery.level * 100}%` : `没充电 : 剩余 ${battery.level * 100}%`;
        // 电池充电状态改变事件
        battery.addEventListener('chargingchange', _ => {
          this.batteryInfo = battery.charging ? `在充电 : 剩余 ${battery.level * 100}%` : `没充电 : 剩余 ${battery.level * 100}%`;
        });
      });
    } else {
      this.batteryInfo = '不支持电池状态接口';
    }
  },
```
### 3. 让你的手机震动: window.navigator.vibrate(200)

**定义：**

这个 `API` 可以让你的手机按你的想法震动。

**使用：**

```javascript
  //触发手机振动
  vibrateFun()  {
    if (navigator.vibrate) {
      navigator.vibrate([500, 500, 500]);
    } else {
      this.vibrateInfo = "您的设备不支持震动";
    }
  },
```
### 4. 联网状态：navigator.onLine

**定义：**

这个 `API` 可以告诉你设备的网络状态是否连接。

**使用：**

```javascript
  mounted() {
    window.addEventListener('online', this.updateOnlineStatus, true);
    window.addEventListener('offline', this.updateOnlineStatus, true);
  },
  methods: {
    //检测网络信号
    updateOnlineStatus() {
      this.onLineInfo = navigator.onLine ? "online" : "offline";
    },
  },
```
> 注意：`navigator.onLine` 只会在设备未连接到局域网或路由器时返回 `false`，其他情况下均返回 `true`。也就是说，设备连接上路由器后，即使这个路由器没联通网络， `navigator.onLine`仍然返回 `true`。
### 5. 浏览器活跃窗口监听: window.onblur & window.onfocus

**定义：**

这两个 `API` 分别表示窗口失去焦点和窗口处于活跃状态。

> 浏览其他窗口、浏览器最小化、点击其他程序等，`window.onblur` 事件就会触发;  回到该窗口，`window.onfocus` 事件就会触发。

**使用：**

```javascript
  mounted() {
    window.addEventListener('blur', this.doFlashTitle, true);
    window.addEventListener('focus', _ => {
      clearInterval(this.timer);
      document.title = '微信网页版';
    }, true);
  },
  methods: {
    //浏览器活跃窗口监听
    doFlashTitle()  {
      this.timer  =  setInterval(_ =>  {
        if (!this.flashFlag)  {
          document.title = "微信网页版";
        } else {
          document.title = `微信（${this.infoNum}）`;
        }
        this.flashFlag = !this.flashFlag
      },  500)
    },
  },
```
### 6. 全屏 API（Fullscreen API）

**定义：**

这个 `API` 可以使你所打开的页面全屏展示。

**使用：**

> Element.requestFullscreen() 方法用于发出异步请求使元素进入全屏模式。

调用此 `API` 并不能保证元素一定能够进入全屏模式。如果元素被允许进入全屏幕模式，document 对象会收到一个 `fullscreenchange` 事件，通知调用者当前元素已经进入全屏模式。如果全屏请求不被许可，则会收到一个 `fullscreenerror` 事件。
当进入/退出全屏模式时,会触发 `fullscreenchange` 事件。你可以在监听这个事件，做你想做的事。

```javascript
  //打开、关闭全屏 
  fullScreenFun()  {
    let fullscreenEnabled = document.fullscreenEnabled ||
    document.mozFullScreenEnabled ||
    document.webkitFullscreenEnabled ||
    document.msFullscreenEnabled;
    if (fullscreenEnabled) {
      let de = document.documentElement;
      if (this.fullScreenInfo === '打开全屏') {
        if (de.requestFullscreen) {
          de.requestFullscreen();
        } else if (de.mozRequestFullScreen) {
          de.mozRequestFullScreen();
        } else if (de.webkitRequestFullScreen) {
          de.webkitRequestFullScreen();
        }
        this.fullScreenInfo = '退出全屏'
      } else {
        if (document.exitFullscreen) {
          document.exitFullscreen();
        } else if (document.mozCancelFullScreen) {
          document.mozCancelFullScreen();
        } else if (document.webkitCancelFullScreen) {
          document.webkitCancelFullScreen();
        }
        this.fullScreenInfo = '打开全屏'
      }
    } else {
      this.fullScreenInfo = '浏览器当前不能全屏';
    }
  },
```

### 1. 媒体查询

`CSS3`媒体查询可以让我们针对不同的媒体类型定义不同的样式，当重置浏览器窗口大小的过程中，页面也会根据浏览器的宽度和高度重新渲染页面。
#### 何选择屏幕大小分割点

如何确定媒体查询的分割点也是一个开发中会遇到的问题，下面是市场上的移动设备和电脑屏幕分辨率的分布情况，可以发现不同品牌和型号的设备屏幕分辨率一般都不一样。
[The Ultimate Guide To iPhone Resolutions，可点击](https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions)

[全球屏幕分辨率统计2018年4月 - 2019年4月，可点击](http://gs.statcounter.com/screen-resolution-stats#monthly-201804-201904-bar)

如果我们选择`600px`,`900px`,`1200px`,`1800px`作为分割点，可以适配到常见的14个机型。
当然这只是其中的一种分割方案，不一定满足项目中的实际需求，我们可以先用跨度大的分割点进行分割，如果出现不适配的情况可以再根据实际情况增加新的分割点。

#### 移动优先 OR PC优先

不管是移动优先还是PC优先，都是依据当随着屏幕宽度增大或减小的时候，后面的样式会覆盖前面的样式。因此，移动端优先首先使用的是`min-width`，PC端优先使用的`max-width`。

### 2.百分比布局

通过百分比单位，可以使得浏览器中组件的宽和高随着浏览器的高度的变化而变化，从而实现响应式的效果。Bootstrap里面的栅格系统就是利用百分比来定义元素的宽高，`CSS3`支持最大最小高，可以将百分比和`max(min)`一起结合使用来定义元素在不同设备下的宽高。

但是我们必须要弄清楚css中子元素的百分比到底是相对谁的百分比。直接上结论吧：

子元素的`height`或`width`中使用百分比，是相对于子元素的直接父元素，`width`相对于父元素的`width`，`height`相对于父元素的`height`；子元素的`top`和`bottom`如果设置百分比，则相对于直接非`static`定位(默认定位)的父元素的高度，同样子元素的`left`和`right`如果设置百分比，则相对于直接非`static`定位(默认定位的)父元素的宽度；子元素的`padding`如果设置百分比，不论是垂直方向或者是水平方向，都相对于直接父亲元素的`width`，而与父元素的`height`无关。跟`padding`一样，`margin`也是如此，子元素的`margin`如果设置成百分比，不论是垂直方向还是水平方向，都相对于直接父元素的`width`；`border-radius`不一样，如果设置`border-radius`为百分比，则是相对于自身的宽度，除了`border-radius`外，还有比如`translate`、`background-size`等都是相对于自身的；

### 3.rem布局

`REM`是`CSS3`新增的单位，并且移动端的支持度很高，Android2.x+,ios5+都支持。`rem`单位都是相对于根元素html的`font-size`来决定大小的,根元素的`font-size`相当于提供了一个基准，当页面的size发生变化时，只需要改变`font-size`的值，那么以`rem`为固定单位的元素的大小也会发生响应的变化。 因此，如果通过`rem`来实现响应式的布局，只需要根据视图容器的大小，动态的改变`font-size`即可（而`em`是相对于父元素的）

### 4.视口单位

`css3`中引入了一个新的单位`vw/vh`，与视图窗口有关，`vw`表示相对于视图窗口的宽度，`vh`表示相对于视图窗口高度，除了`vw`和`vh`外，还有`vmin`和`vmax`两个相关的单位。各个单位具体的含义如下：

| 单位 | 含义 |
| --- | --- |
| vw | 相对于视窗的宽度，1vw 等于视口宽度的1%，即视窗宽度是100vw |
| vh | 相对于视窗的高度，1vh 等于视口高度的1%，即视窗高度是100vh |
| vmin | vw和vh中的较小值 |
| vmax | vw和vh中的较大值 |

**参考文章：**

[八叉树：前端响应式布局原理与方案](https://juejin.im/post/5caaa230e51d452b672f9703)

[张鑫旭：设备像素比devicePixelRatio简单介绍](https://www.zhangxinxu.com/wordpress/2012/08/window-devicepixelratio/)

[颜海镜：Rem布局的原理解析](https://yanhaijing.com/css/2017/09/29/principle-of-rem-layout/)

### 安装postcss插件

#### postcss-import

[`postcss-import`](https://github.com/postcss/postcss-import)相关配置可以[点击这里](https://github.com/postcss/postcss-import)。目前使用的是默认配置。只在`postcss.config.js`文件中引入了该插件。

`postcss-import`主要功有是解决`@import`引入路径问题。使用这个插件，可以让你很轻易的使用本地文件、`node_modules`或者`web_modules`的文件。这个插件配合[`postcss-url`](https://github.com/postcss/postcss-url)让你引入文件变得更轻松。

#### postcss-url

[`postcss-url`](https://github.com/postcss/postcss-url)相关配置可以点击[这里](https://github.com/postcss/postcss-url)。该插件主要用来处理文件，比如图片文件、字体文件等引用路径的处理。

在Vue项目中，[`vue-loader`](https://github.com/vuejs/vue-loader)已具有类似的功能，只需要配置中将`vue-loader`配置进去。

#### autoprefixer

[`autoprefixer`](https://github.com/postcss/autoprefixer)插件是用来自动处理浏览器前缀的一个插件。如果你配置了[`postcss-cssnext`](https://github.com/MoOx/postcss-cssnext)，其中就已具备了`autoprefixer`的功能。在配置的时候，未显示的配置相关参数的话，表示使用的是[Browserslist](https://github.com/ai/browserslist)指定的列表参数，你也可以像这样来指定`last 2 versions` 或者 `> 5%`。

### 其他插件
- [postcss-aspect-ratio-mini](https://github.com/yisibl/postcss-aspect-ratio-mini)
- [postcss-px-to-viewport](https://github.com/evrone/postcss-px-to-viewport)
- [postcss-write-svg](https://github.com/jonathantneal/postcss-write-svg)
- [postcss-cssnext](https://github.com/MoOx/postcss-cssnext)
- [cssnano](https://github.com/ben-eb/cssnano)
- ~~[postcss-viewport-units](https://github.com/springuper/postcss-viewport-units)~~ 2018-11-02：经过一段时间项目的验证，大漠团队已经开始不引用Polyfill

要使用这几个插件，先要进行安装：

```javascript
//依赖太多，故分开显示
npm i postcss-import postcss-url autoprefixer cssnano cssnano-preset-advanced -D
npm i postcss-aspect-ratio-mini postcss-px-to-viewport postcss-write-svg postcss-cssnext -D
```
#### 接下来在`postcss.config.js`文件对新安装的postcss插件进行配置

```javascript
  module.exports  =  {
    plugins:  {
      "postcss-import":  {},
      "postcss-url":  {},
      "postcss-aspect-ratio-mini":  {},
      "postcss-write-svg":  {  utf8:  false  },
      "postcss-cssnext":  {},//由于cssnext和cssnano都具有autoprefixer,事实上只需要一个，所以把默认的autoprefixer删除掉，然后把cssnano中的autoprefixer设置为false
      "postcss-px-to-viewport":  {
        viewportWidth:  750,  // 视窗的宽度，对应的是我们设计稿的宽度，一般是750
        viewportHeight:  1334,  // 视窗的高度，根据750设备的宽度来指定，一般指定1334，也可以不配置
        unitPrecision:  3,  // 指定`px`转换为视窗单位值的小数位数（很多时候无法整除）
        viewportUnit:  'vw',  // 指定需要转换成的视窗单位，建议使用vw
        selectorBlackList:  ['.ignore',  '.hairlines'],  // 指定不转换为视窗单位的类，可以自定义，可以无限添加,建议定义一至两个通用的类名
        minPixelValue:  1,  // 小于或等于`1px`不转换为视窗单位，你也可以设置为你想要的值
        mediaQuery:  false  // 允许在媒体查询中转换`px`
      },
      "cssnano":  {
        preset:  "advanced",
        autoprefixer:  false,
        "postcss-zindex":  false
      },
      // "autoprefixer": { browsers: ["> 1%", "ie > 8", 'last 5 version'] },
      //移动端项目开启此插件 将px转为rem
      // "postcss-px2rem":{
      // remUnit:'75'
      // }
    },
  };
```

#### postcss-write-svg


[`postcss-write-svg`](https://github.com/jonathantneal/postcss-write-svg)插件主要用来处理移动端`1px`的解决方案。该插件主要使用的是`border-image`和`background`来做`1px`的相关处理。比如：

```css
  @svg 1px-border {
      height: 2px;
      @rect {
          fill: var(--color, black);
          width: 100%;
          height: 50%;
      }
  }
  .example {
      border: 1px solid transparent;
      border-image: svg(1px-border param(--color #00b1ff)) 2 2 stretch;
  }
```

编译出来的CSS:

```css
  .example {
      border: 1px solid transparent;
      border-image: url("data:image/svg+xml;charset=utf-8,%3Csvg xmlns='http://www.w3.org/2000/svg' height='2px'%3E%3Crect fill='%2300b1ff' width='100%25' height='50%25'/%3E%3C/svg%3E") 2 2 stretch;
  } 
```

上面演示的是使用`border-image`方式，除此之外还可以使用`background-image`来实现。比如：

```css
  @svg square {
      @rect {
          fill: var(--color, black);
          width: 100%;
          height: 100%;
      }
  }
  #example {
      background: white svg(square param(--color #00b1ff));
  }
```

编译出来就是：

```css
  #example {
      background: white url("data:image/svg+xml;charset=utf-8,%3Csvg xmlns='http://www.w3.org/2000/svg'%3E%3Crect fill='%2300b1ff' width='100%25' height='100%25'/%3E%3C/svg%3E");
  } 

```

> **特别声明：** 由于有一些低端机对`border-image`支持度不够友好，个人建议你使用`background-image`的这个方案。

### 相关链接
> 知识付费模式开启，需要购买会员才可观看呦（2019-05-22购买，支付宝订单号：190522163630182750）

[如何在Vue项目中使用vw实现移动端适配](https://www.w3cplus.com/mobile/vw-layout-in-vue.html)

### 阿里红利真的吃完了

![gougu.jpg](https://uufe-web.oss-cn-beijing.aliyuncs.com/PicLib/uunote/images/gougu_1558579159402.jpg)

![ruanyifeng.png](https://uufe-web.oss-cn-beijing.aliyuncs.com/PicLib/uunote/images/ruanyifeng_1558579181115.png)

![xuqiucengci.jpg](https://uufe-web.oss-cn-beijing.aliyuncs.com/PicLib/uunote/images/xuqiucengci_1558579197535.jpg)

> 阿里红利吃完了？还是化身为神探究孔孟庄了？自行体会！

### 圣诞彩蛋事件

![caidan.jpg](https://uufe-web.oss-cn-beijing.aliyuncs.com/PicLib/uunote/images/caidan_1558579502232.jpg)

### 响应式开发中合理选定CSS媒体查询分割点

```css
	/**
		使用sass minxin实现的针对不同屏幕的函数
		参数:
		phone-only:手机屏幕
		tablet-portrait-up:平板竖屏及更大屏幕
		tablet-portait-only:仅限平板竖屏
		tablet-landscape-up:平板横屏及更大屏幕
		tablet-landscape-only 仅限平板横屏
		desktop-up:PC及更大屏幕
		desktop-only:仅限PC
		big-desktop-up 大号PC
	**/
	@mixin for-size($range) {
		$phone-upper-boundary: 600px;
		$tablet-portrait-upper-boundary: 900px;
		$tablet-landscape-upper-boundary: 1200px;
		$desktop-upper-boundary: 1800px;

		@if $range == phone-only {
			@media (max-width: $phone-upper-boundary - 1px) { @content; }
		} @else if $range == tablet-portrait-up {
			@media (min-width: $phone-upper-boundary) { @content; }
		} @else if $range == tablet-portait-only {
			@media (min-width: $phone-upper-boundary) and (max-width: $tablet-portrait-upper-boundary - 1px) { @content; }
		} @else if $range == tablet-landscape-up {
			@media (min-width: $tablet-landscape-upper-boundary) { @content; }
		} @else if $range == tablet-landscape-only {
			@media (min-width: $tablet-portrait-upper-boundary) and (max-width: $tablet-landscape-upper-boundary - 1px) { @content; }
		} @else if $range == desktop-up {
			@media (min-width: $tablet-landscape-upper-boundary) { @content; }
		} @else if $range == desktop-only {
			@media (min-width: $tablet-landscape-upper-boundary) and (max-width: $desktop-upper-boundary - 1px) { @content; }
		} @else if $range == big-desktop-up {
			@media (min-width: $desktop-upper-boundary) { @content; }
		}
	}

	.test {
		padding: 10px;
		
		@include for-size(desktop-up) {
			padding: 20px;
		}
	}
```

[响应式开发中合理选定CSS媒体查询分割点](https://segmentfault.com/a/1190000007567739)
