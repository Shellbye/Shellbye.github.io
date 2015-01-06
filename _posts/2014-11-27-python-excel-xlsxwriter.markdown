---
author: shellbye
comments: true
date: 2014-11-27 07:12:47+00:00
layout: post
slug: python-excel-xlsxwriter
title: python excel xlsxwriter
wordpress_id: 1126
categories:
- 技术世界
tags:
- excel
- Python
---

使用python批量的处理excel中的数据具有很大的应用场景，我准备研究下几个python操作的excel的第三方库，分别写点笔记，今天的是[xlsxwriter](https://xlsxwriter.readthedocs.org).

（注：xlsxwriter在自己的官网也写了，它对excel的操作是write only的，无法读，而且不能修改已有的excel文件。而且它还有个缺点就是只能生成.xlsx文件，无法生成.xls文件）

源码excel文件在[这里](https://github.com/Shellbye/Core_Python_Programming/tree/master/excel)。


    
    # -*- coding:utf-8 -*-
    __author__ = 'shellbye'
    import xlrd
    import xlsxwriter
    # https://xlsxwriter.readthedocs.org/
    
    
    def extract_info_from_excel(file_url):
        # 打开excel
        data = xlrd.open_workbook(file_url)
        table = data.sheet_by_index(0)
        for i in range(26):
            for j in range(2):
                print "i:" + str(i) + " j:" + str(j) + " =" + table.cell(j, i).value
    
    
    def save_info_into_excel(file_url):
        # 获取数据数据
        workbook = xlsxwriter.Workbook(file_url)
        worksheet = workbook.add_worksheet()
        title = (u'序号', u'学号', u'性别')
        header_format = workbook.add_format({
            'bold': True,
            'align': 'center',
            'valign': 'vcenter',
            'fg_color': '#D7E4BC',
            'border': 1
        })
        worksheet.set_column('A:C', 16)  # set column from A to I width to 16
        worksheet.set_row(0, 20)  # set row height to 20 and some format
        worksheet.freeze_panes(1, 0)
        worksheet.write_row('A1', title, header_format)
        # row and column number start from 0
        # integer
        worksheet.write(1, 0, 1)
        # string
        worksheet.write(1, 1, "shellbye")
        # select
        worksheet.write(1, 2, "boy")
        worksheet.data_validation(
            1, 2, 1, 2,
            {
                'validate': 'list',
                'source': ["boy", "girl"]
            }
        )
        workbook.close()
        return True
    
    
    if __name__ == "__main__":
        excel_file = "./stu_info.xls"
        new_excel_file = "new_stu_info.xlsx"
        # extract_info_from_excel(excel_file)
        save_info_into_excel(new_excel_file)
