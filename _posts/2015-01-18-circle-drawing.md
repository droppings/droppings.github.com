---
layout: post
title: 圆饼展示图
category: note
---
突然收到一个需求，要做一个圆饼状的展示图，于是脑洞大开，尝试下不同的做法...





###利用css3的clip属性：

####1. 只有半圆的饼

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
	.circle-wrap{position:relative;top:0;left:0;width:200px;height:200px;}
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

    主要思想是，遮罩层只有右半圆，真正填充只有左半圆，所以当左半圆旋转到右半圆时，就可以看到了。但是，这样也只有半圆的填充噢，如果要填充整个圆的话，就要两个半圆，其实相当于复制两份，然后其中一份垂直翻转180度。


####2. 完整的饼

    <style>
	    .circle{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;background:#f1f1f1;}
	    .mask-left,.fill-left{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;clip:rect(0,100px,200px,0);}
	    .mask-right,.fill-right{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;clip:rect(0,200px,200px,100px);}
	    .fill{background:#4f90d2;}
	    .fill-left{clip:rect(0 200px 200px 100px);/*  -webkit-animation:circleLeft 5s linear 0s infinite; */ }
	    .fill-right{clip:rect(0 100px 200px 0);/* -webkit-animation:circleRight 5s linear 0s infinite; */ }
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
	.decorate p{color:#4f90d2;}
	.decorate input{width:50px;display:inline;}
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
			<p><input id="rotate_txt" width="80" onblur="circleRotate(document.getElementById('rotate_txt').value)" title="失去焦点即可看到效果"> %</p>
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

###canvas版：

###svg版：
