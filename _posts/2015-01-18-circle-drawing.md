---
layout: post
title: 圆饼展示图
category: note
---
突然收到一个需求，要做一个圆饼状的展示图，于是脑洞大开，尝试下不同的做法...





###1. 利用css3的clip属性：


####1) 只有半圆的饼

    <style>
	    .circle{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;background:#f1f1f1;}
	    .mask,.fill{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;}
	    .mask{clip:rect(0,200px,200px,100px);}
	    .fill{clip:rect(0,100px,200px,0);background:#4f90d2;}
	</style>
    <div class="circle">
		<div class="mask">
			<div class="fill" style="-webkit-transform:rotate(90deg);"></div>
		</div>
	</div>


<style type="text/css">
	*{margin:0;padding:0;}
	.circle-wrap{position:relative;top:0;left:0;width:200px;height:200px;margin-bottom:15px;}
	.circle-1{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;background:#f1f1f1;}
	.mask-1,.fill-1{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;}
	.mask-1{clip:rect(0,200px,200px,100px);}
	.fill-1{clip:rect(0,100px,200px,0);background:#4f90d2;-webkit-transform:rotate(90deg);-ms-transform:rotate(90deg);-o-transform:rotate(90deg);transform:rotate(90deg);}
</style>
<div class="circle-wrap">
	<div class="circle-1">
		<div class="mask-1">
			<div class="fill-1">
			</div>
		</div>
	</div>
</div>


主要思想是，遮罩层只有右半圆，真正填充的只有左半圆，所以当左半圆旋转到右半圆时，就可以看到了。但是，这样也只有半圆的填充噢，如果要填充整个圆的话，就要两个半圆，其实相当于复制两份，然后其中一份垂直翻转180度。


####2) 完整的饼

    <style>
	    .circle{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;background:#f1f1f1;}
	    .mask-left,.fill-left{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;clip:rect(0,100px,200px,0);}
	    .mask-right,.fill-right{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;clip:rect(0,200px,200px,100px);}
	    .fill{background:#4f90d2;}
	    .fill-left{clip:rect(0 200px 200px 100px);}
	    .fill-right{clip:rect(0 100px 200px 0);}
	</style>
	<div class="circle">
		<div class="mask-left">
			<div class="fill fill-left"></div>
		</div>
		<div class="mask-right ">
			<div class="fill fill-right"></div>
		</div>
	</div>

<style type="text/css">
	.circle-2{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;background:#f1f1f1;}
	.mask-2-left,.fill-2-left{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;clip:rect(0,100px,200px,0);}
	.mask-2-right,.fill-2-right{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;clip:rect(0,200px,200px,100px);}
	.fill-2{background:#4f90d2;}
	.fill-2-left{clip:rect(0 200px 200px 100px);}
	.fill-2-right{clip:rect(0 100px 200px 0);}
	.decorate{width:160px;height:160px;position:absolute;top:20px;left:20px;background:#fff;border-radius:50%;text-align:center;vertical-align:middle;line-height:160px;}
	.decorate-inner{color:#4f90d2;line-height:160px;}
	.decorate-inner input{width:50px;display:inline;}
</style>
<div class="circle-wrap">
	<div class="circle-2">
		<div class="mask-2-left">
			<div class="fill-2 fill-2-left"></div>
		</div>
		<div class="mask-2-right ">
			<div class="fill-2 fill-2-right"></div>
		</div>
		<div class="decorate">
			<div class="decorate-inner"><input id="rotate_txt" width="80" onblur="circleRotate(document.getElementById('rotate_txt').value)" title="失去焦点即可看到效果"> %</div>
		</div>
	</div>
</div>
<script type="text/javascript">
	function circleRotate (deg) {
		var left_fill = document.querySelector(".fill-2-left"),
			right_fill = document.querySelector(".fill-2-right");
		deg = deg * 360 * 0.01;
		if(deg <= 180){
			left_fill.style.cssText = "transform:rotate(0deg);transition:all .6s linear;";
			right_fill.style.cssText = "transform:rotate(" + deg + "deg);transition:all .6s linear .6s;";
		}else{
			right_fill.style.cssText = "transform:rotate(180deg);transition:all .6s linear;";
			deg -= 180;
			left_fill.style.cssText = "transform:rotate(" + deg + "deg);transition:all .6s linear .6s;";
		}
	}
</script>

(输入数值然后失去焦点，便可看到效果。)


###2. canvas版(arc画弧)：

canvas画圆主要用arc方法

    context.arc(x,y,r,sAngle,eAngle,counterclockwise);


    参数	描述
    x	圆的中心的 x 坐标。
    y	圆的中心的 y 坐标。
    r	圆的半径。
    sAngle	起始角，以弧度计。（弧的圆形的三点钟位置是 0 度）。
    eAngle	结束角，以弧度计。
    counterclockwise	可选。规定应该逆时针还是顺时针绘图。False = 顺时针，true = 逆时针。


很明显，跟高中学的三角函数同理，想画多少角度就多少，完全木有悬念。

核心代码：
    
    ctx.beginPath();
    ctx.moveTo(x,y); 
    ctx.lineTo(x,r);  //画出12点钟的线，从圆心出发
    ctx.arc(x,y,r,sAngle,eAngle);  //然后画一定角度的弧
    ctx.lineWidth = lineWidth;
    ctx.strokeStyle = "#11bbaa";
    ctx.stroke();
    ctx.closePath(); //最后闭合
    ctx.fillStyle = "#11bbaa";
    ctx.fill();  //填充


<style type="text/css">
	#myCanvas{width:200px;height:200px;}
	.mb-15{margin-bottom:15px;}
</style>
<div class="mb-15">
	<canvas id="myCanvas" width="200" height="200">
		sorry, your browser can not support canvas.
	</canvas>
	输入：<input id="angel" >deg <a href="javascript:drawArc(100,100,90,-0.5*Math.PI,(document.getElementById('angel').value/180-0.5)*Math.PI, 1);">click</a>
</div>
<script>
	var canvas = document.getElementById('myCanvas'), ctx;
	if(canvas){
		ctx = canvas.getContext("2d");
	}
	function drawArc (x,y,r,sAngle,eAngle,lineWidth) {
		ctx.beginPath();
		ctx.moveTo(x,y); 
		ctx.lineTo(x,r); 
		ctx.arc(x,y,r,sAngle,eAngle);
		ctx.lineWidth = lineWidth;
		ctx.strokeStyle = "#11bbaa";
		ctx.stroke();
		ctx.closePath();
		ctx.fillStyle = "#11bbaa";
		ctx.fill();
	}
	drawArc(100,100,90,-0.5*Math.PI,(90/180-0.5)*Math.PI, 1);
</script>



###3. svg版：

SVG 意为可缩放矢量图形（Scalable Vector Graphics），而且让人惊喜的是老古董IE6居然也兼容它，哇哦不得了了。

圆饼图由于角度等不确定的因素，所以不能直接使用&lt;circle&gt;标签来画，所以就尝试用&lt;path&gt;标签来画，具体参数如下：

&lt;path&gt; 标签用来定义路径。
下面的命令可用于路径数据：

    M = moveto
    L = lineto
    H = horizontal lineto
    V = vertical lineto
    C = curveto
    S = smooth curveto
    Q = quadratic Belzier curve
    T = smooth quadratic Belzier curveto
    A = elliptical Arc
    Z = closepath



这次画圆饼，主要应用到A来画弧形（椭圆or圆形）。弧形命令A是另一个创建SVG曲线的命令:

    A rx ry x-axis-rotation large-arc-flag sweep-flag x y

其中，rx和ry分别代表x轴半径和y轴半径，这里是圆形所以二者相等，值为半径大小。燃后，x-axis-rotation表示x轴旋转角度，这里就不用旋转，所以设为0。特别注意的是，large-arc-flag（角度大小） 和sweep-flag（弧线方向）这两个参数。large-arc-flag决定弧线是大于还是小于180度，0表示小角度弧，1表示大角度弧。sweep-flag表示弧线的方向，0表示从起点到终点沿逆时针画弧，1表示从起点到终点沿顺时针画弧。

具体下面有个图片说明，看！

<img src="/img/demo/SVGArcs_Flags.png" alt="SVGArcs_Flags">

    <?xml version="1.0" standalone="no"?>
    <svg width="325px" height="325px" version="1.1" xmlns="http://www.w3.org/2000/svg">
      <path d="M80 80
               A 45 45, 0, 0, 0, 125 125
               L 125 80 Z" fill="green"/>
      <path d="M230 80
               A 45 45, 0, 1, 0, 275 125
               L 275 80 Z" fill="red"/>
      <path d="M80 230
               A 45 45, 0, 0, 1, 125 275
               L 125 230 Z" fill="purple"/>
      <path d="M230 230
               A 45 45, 0, 1, 1, 275 275
               L 275 230 Z" fill="blue"/>
    </svg>

配上代码就更清晰了，这个图出自<a href="https://developer.mozilla.org/zh-CN/docs/Web/SVG/Tutorial/Paths" target="_blank">文章</a>，此文章讲述更多的svg path路径的详细参数，欢迎详阅。


好了，最后两个参数则表示弧线终点的坐标，这里需要一定的数学知识。（现在终于发现原来高考学的数学是有用的- -

啦啦啦，身为学渣的我只想到以下的解决公式，欢迎学霸给出更好的答案....

    x = r(1-sinα)
    y = r(1-cosα)

于是svg版的圆饼也粗来拉~散花~~

	var path = "", 
		x_axis_rotation = "0",  //x-axis-rotation表示x轴旋转角度，这里设为0
		large_arc_flag, 
		sweep_flag = "0",//sweep-flag表示弧线的方向，0表示逆时针，1表示顺时针
		end_x,
		end_y;

	//角度大小，0表示小于180，1表示大于180
	if(angle <= 180){
		large_arc_flag = "0";
	}else{
		large_arc_flag = "1";
	}

	//计算终点坐标
	end_x = r * ( 1 - Math.sin(angle*Math.PI/180));
	end_y = r * ( 1 - Math.cos(angle*Math.PI/180));

	//move to (x,y)
	path += "M" + x + " " + y + " "; 
	//line to (x,0)
	path += "L" + x + " " + "0 "; 
	//draw the elliptical Arc 画弧形
	path += "A" + x + " " + y + " " + x_axis_rotation + " " + large_arc_flag + " " + sweep_flag + " " + end_x + " " + end_y + " ";
	//close the path
	path += "Z"; 
	stage.innerHTML = '<path d="' + path + '" style="fill:#4f90d2;" id="cake"></path>';


<div class="mb-15">
	<svg width="200" height="200" version="1.1"
	xmlns="http://www.w3.org/2000/svg" id="stage"></svg>

	输入角度：<input type="text" id="angle_txt"><a href="javascript:drawCake(100,100,100,document.getElementById('angle_txt').value);">Click</a>
</div>
<script type="text/javascript">
	var stage = document.getElementById("stage");

	function drawCake (r, x, y, angle) {

		var path = "", 
			x_axis_rotation = "0",  //x-axis-rotation表示x轴旋转角度，这里设为0
			large_arc_flag, 
			sweep_flag = "0",//sweep-flag表示弧线的方向，0表示逆时针，1表示顺时针
			end_x,
			end_y;

	    //整圆
	    if(angle % 360 == 0){
	    	stage.innerHTML = '<circle cx="' + x + '" cy="' + y + '" r="' + r + '" ' + '" fill="#4f90d2"></circle>';
	    	return;
	    }else{
	    	angle = angle % 360; 
	    }

		//角度大小，0表示小于180，1表示大于180
		if(angle <= 180){
			large_arc_flag = "0";
		}else{
			large_arc_flag = "1";
		}

		//计算终点坐标
		end_x = r * ( 1 - Math.sin(angle*Math.PI/180));
		end_y = r * ( 1 - Math.cos(angle*Math.PI/180));

		//move to (x,y)
		path += "M" + x + " " + y + " "; 
		//line to (x,0)
		path += "L" + x + " " + "0 "; 
		//draw the elliptical Arc 画弧形
		path += "A" + x + " " + y + " " + x_axis_rotation + " " + large_arc_flag + " " + sweep_flag + " " + end_x + " " + end_y + " ";
		//close the path
		path += "Z"; 
		stage.innerHTML = '<path d="' + path + '" style="fill:#4f90d2;" id="cake"></path>';
	}


###总结

....

</script>




