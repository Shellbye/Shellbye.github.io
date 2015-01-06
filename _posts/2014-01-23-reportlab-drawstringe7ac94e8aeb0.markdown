---
author: shellbye
comments: true
date: 2014-01-23 07:42:45+00:00
layout: post
slug: reportlab-drawstring%e7%ac%94%e8%ae%b0
title: ReportLab drawString笔记
wordpress_id: 785
categories:
- 技术世界
tags:
- Django
- ReportLab
---

之前项目赶工，试着使用了一下ReportLab的PDF动态生成，浅尝辄止了，今天看Django的[教程](http://www.djangobook.com/en/2.0/chapter13.html)，里面提到了一下，有点空了，就多加了几行代码，有如下小小发现：



    
    def hello_pdf(request):
        # Create the HttpResponse object with the appropriate PDF headers.
        response = HttpResponse(mimetype='application/pdf')
        response['Content-Disposition'] = 'attachment; filename=hello4.pdf'
    
        # Create the PDF object, using the response object as its "file."
        p = canvas.Canvas(response)
    
        # Draw things on the PDF. Here's where the PDF generation happens.
        # See the ReportLab documentation for the full list of functionality.
        p.drawString(550, 800, "(550, 800).")
        p.drawString(300, 400, "(300, 400).")
        p.drawString(100, 100, "(100,100).")
        p.drawString(100, 50, "(100,50).")
        p.drawString(0, 0, "(0,0).")
    
        # Close the PDF object cleanly, and we're done.
        p.showPage()
        p.save()
        return response


经过这样一些简单的设置，发现drawString 是以当前页面的左下角为原点，页面为第一象限绘制图形，并且绘制的图形大小大约为x = 600, y = 800左右，并且单位可选。

详细资料，参考reportlab官方[文档](http://www.reportlab.com/docs/reportlab-userguide.pdf)
