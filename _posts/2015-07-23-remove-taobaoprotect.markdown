---
layout: post
title:  "如何卸载 “阿里巴巴反钓鱼安全服务”（TBSecSvc，TaobaoProtect）"
date:   2015-07-23 10:00:00
categories: TBSecSvc TaobaoProtect 淘宝 阿里 阿里巴巴 阿里巴巴反钓鱼 阿里巴巴反钓鱼安全服务
---

- 结束`TaobaoProtect.exe`进程
- 打开一个命令行窗口，运行：
{% highlight batch %}
sc stop TBSecSvc
sc delete TBSecSvc
{% endhighlight %}
- 删除以下文件夹：`C:\Users\<username>\AppData\Roaming\TaobaoProtect` （如果有些文件删不掉，杀掉调用的程序再删除）
- 世界清净了