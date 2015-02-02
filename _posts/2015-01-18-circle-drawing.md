---
layout: post
title: 圆饼展示图
category: note
---
突然收到一个需求，要做一个圆饼状的展示图，于是脑洞大开，尝试下不同的做法...




####利用css3的clip属性：

```

    <style type="text/css">
	    .circle{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;background:#f1f1f1;}
	    .mask,.fill{position:absolute;top:0;left:0;width:200px;height:200px;border-radius:50%;clip:rect(0 200px 200px 100px);}
	    .fill{background:#4f90d2;clip:rect(0,100px,200px,0);}
	</style>
    <div class="circle">
		<div class="mask">
			<div class="fill">
			</div>
		</div>
	</div>


```

####canvas版：

####svg版：
