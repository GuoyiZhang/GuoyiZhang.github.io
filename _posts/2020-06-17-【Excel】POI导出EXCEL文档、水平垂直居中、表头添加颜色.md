---
layout:     post
title:      【Excel】POI导出EXCEL文档、水平垂直居中、表头添加颜色
subtitle:   【Excel】POI导出EXCEL文档、水平垂直居中、表头添加颜色
date:       2020-06-17 14:24:14
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Java
---

>【Excel】POI导出EXCEL文档、水平垂直居中、表头添加颜色

# 【Excel】POI导出EXCEL文档、水平垂直居中、表头添加颜色

package utils; import ggframework.bottom.log.GGLogger; import java.io.File; import java.io.FileOutputStream; import java.io.IOException; import java.io.OutputStream; import java.util.ArrayList; import java.util.HashMap; import java.util.Iterator; import java.util.List; import java.util.Map; import org.apache.commons.lang3.StringUtils; import org.apache.poi.hssf.usermodel.HSSFCell; import org.apache.poi.hssf.usermodel.HSSFCellStyle; import org.apache.poi.hssf.usermodel.HSSFFont; import org.apache.poi.hssf.usermodel.HSSFRichTextString; import org.apache.poi.hssf.usermodel.HSSFRow; import org.apache.poi.hssf.usermodel.HSSFSheet; import org.apache.poi.hssf.usermodel.HSSFWorkbook; import org.apache.poi.hssf.util.HSSFColor; import org.apache.poi.ss.usermodel.CellStyle; import org.apache.poi.ss.usermodel.IndexedColors; import org.apache.poi.ss.util.CellRangeAddress; public class ExcelUtil { public static File getSimpolDataExcel(List<Map<String, Object>> data, String name) { //创建Excel工作簿对象，对应一个Excel文件 HSSFWorkbook book = new HSSFWorkbook(); //创建Excel工作表对象 HSSFSheet sheet = book.createSheet(name); sheet.setVerticallyCenter(true); int index = 0; //创建单元格，并设置值表头 设置表头居中 HSSFCellStyle styleMain = book.createCellStyle(); //水平居中 styleMain.setAlignment(HSSFCellStyle.ALIGN_CENTER); //垂直居中 styleMain.setVerticalAlignment(HSSFCellStyle.VERTICAL_CENTER); for (Map<String, Object> map : data) { int cellNum = 0; //创建Excel工作表行 HSSFRow row = sheet.createRow(index); //设置行高 单位像素 row.setHeight((short)400); //创建单元格，并设置值表头 设置表头居中 HSSFCellStyle styleTitle = book.createCellStyle(); //水平居中 styleTitle.setAlignment(HSSFCellStyle.ALIGN_CENTER); //垂直居中 styleTitle.setVerticalAlignment(HSSFCellStyle.VERTICAL_CENTER); //设置背景色 styleTitle.setFillForegroundColor(IndexedColors.TEAL.getIndex()); styleTitle.setFillPattern(CellStyle.SOLID_FOREGROUND); styleTitle.setBorderBottom(HSSFCellStyle.BORDER_THIN); styleTitle.setBorderLeft(HSSFCellStyle.BORDER_THIN); styleTitle.setBorderRight(HSSFCellStyle.BORDER_THIN); styleTitle.setBorderTop(HSSFCellStyle.BORDER_THIN); if(index==0){ int i=0; for(Map.Entry<String, Object> entry: map.entrySet()) { //设置列的宽度 单位像素 sheet.setColumnWidth(i++, 6000); //创建Excel单元格 HSSFCell cell = row.createCell(cellNum); //设置Excel单元格值 cell.setCellValue(entry.getKey()==null?"--":entry.getKey().toString()); cell.setCellStyle(styleTitle); cellNum++; } }else{ for(Map.Entry<String, Object> entry: map.entrySet()) { HSSFCell cell = row.createCell(cellNum); cell.setCellValue(entry.getValue()==null?"--":entry.getValue().toString()); cell.setCellStyle(styleMain); cellNum++; } } index++; } //设置生成的文件临时存放目录 File dir = new File("temp/userfb"); if (!dir.exists()) { dir.mkdirs(); } String fileName =("temp/userfb") + File.separator + name + ".xls"; File file = new File(fileName); try { FileOutputStream fos = new FileOutputStream(file); book.write(fos); fos.close(); } catch (IOException e) { e.printStackTrace(); } return file; } }

 


