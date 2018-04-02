---
title: 【jquery】面向对象写法
date: 2015-7-14 16:42:20
tags:  jquery面向对象写法
reward: true
---

# 上代码 jquery对象写法

        $(function() {
        //可以初始化 属性值
        var pi;
        var Run = function (){
           pi= "3.1415";
          //可以赋值属性
        }
        //new 出一个新的jquery的对象
       $.run = new Run();
       //定义一个按钮
       Run.prototype.start = function (){
        alert("测试成功！");
       }
        });
	<body>
      <button  onclick="$.run.start()">Run</button>
	</body>
	</html>


面向对象写法可以减少以及避免id或者name冲突的概率，而且写起来很方便好看！