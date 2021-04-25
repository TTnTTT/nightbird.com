---
title: 使用POI进行包含图片的数据的导出
date: 2020-12-19 19:04:04
categories:
- [技术文章,框架]
tags: 
- Apache POI
---

>以前老觉得POI很难很难，导入导出都是使用对的POI封装了的工具，像EasyExcel、ExcelKit这些，文档都写的很详细，简单调用几个方法就可以了，现在有时间还是想看一下真正的POI。

**源码地址：**[https://github.com/TTnTTT/poi-syudy](https://github.com/TTnTTT/poi-syudy)

# 效果图

![](/img/images/使用POI进行包含图片的数据的导出/poi导出效果图.png)

# 主要代码

主要步骤就是：

- 先创建一个webbook，对应一个Excel文件

- 在webbook中添加一个sheet,对应Excel文件中的sheet
- 在sheet中添加表头第n行
- 使用行创建出单元格，放数据这些操作都是针对单元格的
- 涉及到图片的话为了适应API就得先把图片转成 byteArray[] 然后放入对应的单元格

```
public HSSFWorkbook exportExcel(String[] title, List<Creativity> data) throws IOException {
        // 第一步，创建一个webbook，对应一个Excel文件
        HSSFWorkbook wb = new HSSFWorkbook();
        // 第二步，在webbook中添加一个sheet,对应Excel文件中的sheet
        HSSFSheet sheet = wb.createSheet("Sheet1");
        // 第三步，在sheet中添加表头第0行,注意老版本poi对Excel的行数列数有限制short
        HSSFRow row = sheet.createRow(0);
        row.setHeight((short)500);
        // 第四步，创建单元格，并设置值表头 设置表头居中
        HSSFCellStyle style = wb.createCellStyle();
        style.setAlignment(HorizontalAlignment.CENTER); // 创建一个居中格式
        style.setVerticalAlignment(VerticalAlignment.CENTER);
        //声明列对象
        HSSFCell cell = null;
        //创建标题
        for (int i = 0; i < title.length; i++) {
            sheet.setColumnWidth(i, 6000);//设置单个单元格宽度
            cell=row.createCell(i);
            cell.setCellValue(title[i]);

            //设置字体
            HSSFFont font = wb.createFont();
            font.setFontName("黑体");
            font.setFontHeightInPoints((short) 15);
            style.setFont(font);

            cell.setCellStyle(style);
        }

        //计算行数到哪了
        Integer count=1;
        //创建内容
        for (int i = 0; i < data.size(); i++) {
            Creativity creativity = data.get(i);
            List<CreativityPic> creativityPics = creativity.getCreativityPics();

            //用于后面的合并行数
            Integer startCount = count;

            for (int j = 0; j < creativityPics.size(); j++) {
                CreativityPic pic = creativityPics.get(j);
                //创建一行  count自增1
                row=sheet.createRow(count);
                synchronized (count){
                    count++;
                }
                row.setHeight((short) 2000);
                //创意编号
                cell=row.createCell(0);cell.setCellValue(creativity.getId());cell.setCellStyle(style);
                //创意名称
                cell=row.createCell(1);cell.setCellValue(creativity.getName());cell.setCellStyle(style);
                //创意描述
                cell=row.createCell(2);cell.setCellValue(creativity.getDescription());cell.setCellStyle(style);
                //创始人
                cell=row.createCell(3);cell.setCellValue(creativity.getUserId());cell.setCellStyle(style);
                //创建时间
                String time = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(creativity.getCrtTime());
                cell=row.createCell(4);cell.setCellValue(time);cell.setCellStyle(style);
                //图片标题
                cell=row.createCell(6);cell.setCellValue(pic.getTitle());cell.setCellStyle(style);
                //图片描述
                cell=row.createCell(7);cell.setCellValue(pic.getDescription());cell.setCellStyle(style);
                //图片
                String picUrl = pic.getUrl();
                String suff = picUrl.substring(picUrl.lastIndexOf(".")+1).toLowerCase();

                // 先把读进来的图片放到一个ByteArrayOutputStream中，以便产生ByteArray
                ByteArrayOutputStream byteArrayOut = imgToByteOutStream(picUrl);

                //确定图片坐标  参数(left，top，right，bottom,起始列,起始行,终止列,终止行) 前四个参数是控制图片在单元格的位置
                HSSFClientAnchor anchor = new HSSFClientAnchor(200, 30, 600, 250,
                        (short) 5, count-1, (short) 5, count-1);
                HSSFPatriarch patriarch = sheet.createDrawingPatriarch();//用于创建图片
                //创建图片
                patriarch.createPicture(anchor, wb.addPicture(byteArrayOut.toByteArray(),
                        suff.equals(Sys.PNG_PIC) ? HSSFWorkbook.PICTURE_TYPE_PNG : HSSFWorkbook.PICTURE_TYPE_JPEG));

            }
            //遍历完一个详情后需合并单元格  参数（起始行，终止行，起始列，终止列）
            CellRangeAddress cellRangeAddress = new CellRangeAddress(startCount, count-1, 0, 0);
            sheet.addMergedRegion(cellRangeAddress);
            cellRangeAddress = new CellRangeAddress(startCount, count-1, 1, 1);
            sheet.addMergedRegion(cellRangeAddress);
            cellRangeAddress = new CellRangeAddress(startCount, count-1, 2, 2);
            sheet.addMergedRegion(cellRangeAddress);
            cellRangeAddress = new CellRangeAddress(startCount, count-1, 3, 3);
            sheet.addMergedRegion(cellRangeAddress);
            cellRangeAddress = new CellRangeAddress(startCount, count-1, 4, 4);
            sheet.addMergedRegion(cellRangeAddress);
        }
        return wb;
    }
```

# 先学会使用了再去了解一下POI的一些知识

## 1.什么是Apache POI？

Apache POI是Apache软件基金会的开放源码函式库，POI提供API给Java程序对Microsoft Office格式档案读和写的功能。POI主要用来做数据库的导入导出，报表的导出还可以使用 iReport

## 2.POI的jar包导入

```xml
 <!-- poi的包 3.15版本后单元格类型获取方式有调整 -->
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi</artifactId>
            <version>3.9</version>
        </dependency>
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi-ooxml</artifactId>
            <version>3.9</version>
        </dependency>
```



## 3.POI的API

### 结构

- HSSF － 提供读写Microsoft Excel格式档案的功能。
- XSSF － 提供读写Microsoft Excel OOXML格式档案的功能。
- HWPF － 提供读写Microsoft Word格式档案的功能。
- HSLF － 提供读写Microsoft PowerPoint格式档案的功能。
- HDGF － 提供读写Microsoft Visio格式档案的功能。

### 对象

> 下面主要介绍HSSF和XSSF两种组件，简单的讲HSSF用来操作Office 2007版本前excel**.xls**文件，XSSF用来操作Office 2007版本后的excel**.xlsx**文件，注意二者的后缀是不一样的。

**HSSF**在`org.apache.poi.hssf.usermodel`包中。它实现了Workbook 接口，用于Excel文件中的.xls格式

> HSSF常用组件：
>
> HSSFWorkbook   excel的文档对象
>
> HSSFSheet        excel的表单
>
> HSSFRow          excel的行
>
> HSSFCell           excel的格子单元
>
> HSSFFont          excel字体
>
> HSSFDataFormat 日期格式
>
> HSSFHeader      sheet头
>
> HSSFFooter       sheet尾（只有打印的时候才能看到效果）
>
> 样式：
>
> HSSFCellStyle    cell样式
>
> 辅助操作包括：
>
> HSSFDateUtil      日期
>
> HSSFPrintSetup  打印
>
> HSSFErrorConstants 错误信息表



**XSSF**在`org.apache.xssf.usemodel`包，并实现Workbook接口，用于Excel文件中的.xlsx格式

>常用组件：
>
>XSSFWorkbook excel的文档对象
>
>XSSFSheet excel的表单
>
>XSSFRow excel的行
>
>XSSFCell excel的格子单元
>
>XSSFFont excel字体
>
>XSSFDataFormat 日期格式
>
>和HSSF类似;

#### 两个组件共同的字段类型描述

其实两个组件就是针对excel的两种格式，大部分的操作都是相同的。

```
单元格类型                        描述
CELL_TYPE_BLANK          代表空白单元格
CELL_TYPE_BOOLEAN        代表布尔单元（true或false）
CELL_TYPE_ERROR          表示在单元的误差值
CELL_TYPE_FORMULA        表示一个单元格公式的结果
CELL_TYPE_NUMERIC        表示对一个单元的数字数据
CELL_TYPE_STRING         表示对一个单元串（文本）
```

### 操作步骤

以HSSF为例，XSSF操作相同。

首先，理解一下一个Excel的文件的组织形式，一个Excel文件对应于一个workbook(HSSFWorkbook)，一个workbook可以有多个sheet（HSSFSheet）组成，一个sheet是由多个row（HSSFRow）组成，一个row是由多个cell（HSSFCell）组成。

```
1、用HSSFWorkbook打开或者创建“Excel文件对象”

2、用HSSFWorkbook对象返回或者创建Sheet对象

3、用Sheet对象返回行对象，用行对象得到Cell对象

4、对Cell对象读写。复制代码
```

