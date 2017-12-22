---
title: Java读写excel文件
date: 2017-09-06 13:59:59
tags:
categories:
---

最近做了一个退款的需求，根据 XXX 提供的 userid，orderid，refundmoney 等数据调用相应的接口退款，数据给的形式不是 txt，而是 excel 表格的形式。之前没做过，在此记录下。

选用的库是 Apache POI，提供对 excel 的读写功能的 java api。

* HSSF：excel2003（.xls）
* XSSF：excel2007 及以后（.xlsx）

对应的 maven 依赖是

```
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>3.14</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>3.14</version>
</dependency>
```

### XLSReader

```java
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.ss.usermodel.Workbook;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

import java.io.FileInputStream;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class XLSReader {
    public static List<List> readExcel(String url, int sheetIndex) throws Exception {
        // 从xlsx/xls文件创建输入流
        FileInputStream fis = new FileInputStream(url);
        List list = new ArrayList();

        // 创建工作薄Workbook
        Workbook wb = null;

        if (url.toLowerCase().endsWith("xlsx")) { // 读取2007版，以.xlsx结尾
            try {
                wb = new XSSFWorkbook(fis);
            } catch (IOException e) {
                e.printStackTrace();
            }
        } else if (url.toLowerCase().endsWith("xls")) { // 读取2003版，以.xls结尾
            try {
                wb = new HSSFWorkbook(fis);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        Sheet sheet = wb.getSheetAt(sheetIndex);
        Row row;
        String cell;
        for (int i = sheet.getFirstRowNum(); i < sheet.getPhysicalNumberOfRows(); i++) {
            // 循环行数
            row = sheet.getRow(i);
            List cells = new ArrayList();
            for (int j = row.getFirstCellNum(); j < row.getPhysicalNumberOfCells(); j++) {
                // 循环列数
                cell = row.getCell(j).toString();
                cells.add(cell);
            }
            list.add(cells);
        }

        return list;
    }
}
```

### XLSWriter

```java
import org.apache.poi.xssf.usermodel.*;

import java.io.FileOutputStream;
import java.util.List;

public class XLSWriter {
    public static void writeToExcel(List<List> list, String url) throws Exception {
        // 创建工作薄
        XSSFWorkbook workBook = new XSSFWorkbook();
        // 在工作薄中创建一工作表
        XSSFSheet sheet = workBook.createSheet();

        for (int rowIndex = 0; rowIndex < list.size(); rowIndex++) {
            // 在指定的索引处创建一行
            XSSFRow row = sheet.createRow(rowIndex);
            List cells = list.get(rowIndex);
            for (int cellIndex = 0; cellIndex < cells.size(); cellIndex++) {
                // 在指定的索引处创建一列（单元格）
                XSSFCell code = row.createCell(cellIndex);
                // 定义单元格为字符串类型
                code.setCellType(XSSFCell.CELL_TYPE_STRING);
                // 输入cell内容
                code.setCellValue(cells.get(cellIndex).toString());
            }
        }

        // 新建输出流并把相应的excel文件存盘
        FileOutputStream fos = new FileOutputStream(url);
        workBook.write(fos);
        fos.flush();
        //操作结束，关闭流
        fos.close();
    }
}
```

# 参考链接

* http://zhoushijie5230.iteye.com/blog/2114964
