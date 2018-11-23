---
title: java Excel导出工具类
keywords: java导出工具,导出工具,java导出,excel导出,java excel导出,java excel,java export,java excel export
---
### [源码](https://github.com/crewor/Notes/blob/master/util/poi/ExcelUtil.java)
### 特性:
    1. 支持自动拆分为多个sheet页。
    2. 支持自定义标题，内容，后置内容和3种内容的格式设置。
    3. 支持获取值的格式化。
    
<!--more-->
### 使用实例:
    
##### 1.截图
{% asset_img p1.png 实例截图 %}
##### 2.实例
    package excel;
    ​
    import org.apache.poi.hssf.usermodel.HSSFDataFormat;
    import org.apache.poi.ss.usermodel.CellStyle;
    import org.apache.poi.ss.usermodel.Font;
    import org.apache.poi.ss.usermodel.HorizontalAlignment;
    import org.apache.poi.ss.usermodel.Workbook;
    ​
    import java.io.FileOutputStream;
    import java.math.BigDecimal;
    import java.util.Arrays;
    ​
    /**
     * @author:likun
     * @email:likun18@jd.com
     * @erp:likun101
     * @CreateTime:2018/5/16 17:25
     * @Project notes
     */
    public class ExcelUtilTest {
    ​
    ​
        public static class Person {
            private String name;
            private Integer age;
            private String phone;
            private BigDecimal deposit;
    ​
            /**
             * @param name    姓名
             * @param age     年龄
             * @param phone   电话
             * @param deposit 存款
             */
            public Person(String name, Integer age, String phone, BigDecimal deposit) {
                this.name = name;
                this.age = age;
                this.phone = phone;
                this.deposit = deposit;
            }
    ​
        }
    ​
    ​
        public static void main(String[] args) {
    ​
            ExcelUtil excelUtil = new ExcelUtil();
            excelUtil.setAllAddTitle(true);
            excelUtil.setEverySheetCount(2048);
            excelUtil.setSheetName("结算明细信息");
            //创建数字格式化风格1
            final CellStyle rightStyle1 = excelUtil.createCellStyle();
            rightStyle1.setAlignment(HorizontalAlignment.RIGHT);
    ​
    ​
            //创建数字格式化风格1
            final CellStyle moneyStyle1 = excelUtil.createCellStyle();
            moneyStyle1.setDataFormat(HSSFDataFormat.getBuiltinFormat("0.00"));
            moneyStyle1.setAlignment(HorizontalAlignment.RIGHT);
            //创建粗体
            Font boldFont1 = excelUtil.createFont();
            boldFont1.setBold(true);
            boldFont1.setFontHeightInPoints((short) 10);
    ​
    ​
            final CellStyle titleStyle1 = excelUtil.createCellStyle();
            titleStyle1.setAlignment(HorizontalAlignment.CENTER);
            //粗体风格格式化
            titleStyle1.setFont(boldFont1);
    ​
            ExcelUtil.ExcelContent.CellFormatter titleCellFormatter = (cell) -> cell.setCellStyle(titleStyle1);
            ExcelUtil.ExcelContent.CellFormatter moneyCellFormat1 = (cell) -> cell.setCellStyle(moneyStyle1);
            ExcelUtil.ExcelContent.CellFormatter righhtCellFormat = (cell) -> cell.setCellStyle(rightStyle1);
            excelUtil.setTitle(
                    new ExcelUtil.ExcelContent.CellField("序号", null, titleCellFormatter)
                    , new ExcelUtil.ExcelContent.CellField("姓名", null, titleCellFormatter)
                    , new ExcelUtil.ExcelContent.CellField("年龄", null, titleCellFormatter)
                    , new ExcelUtil.ExcelContent.CellField("电话", null, titleCellFormatter)
                    , new ExcelUtil.ExcelContent.CellField("存款", null, titleCellFormatter)
            );
            excelUtil.setCellWidth(2, 8, 8, 20, 6);
            excelUtil.setRowHeight(22, 20, 20);
    ​
            ExcelUtil.ExcelContent<Person> content = new ExcelUtil.ExcelContent<Person>();
            excelUtil.setContent(content);
            content.setFields(
                    ExcelUtil.ExcelContent.Field.SEQUENCE,
                    new ExcelUtil.ExcelContent.Field("name", (name) -> "formated:" + name, righhtCellFormat)
                    , new ExcelUtil.ExcelContent.Field("age", null, righhtCellFormat)
                    , new ExcelUtil.ExcelContent.Field("phone", null, righhtCellFormat)
                    , new ExcelUtil.ExcelContent.Field("deposit", (deposit) -> deposit == null ? 0 : deposit, moneyCellFormat1)
            );
    ​
    ​
            content.addObjs(
                    new Person("李某", 15, "15555555555", new BigDecimal("1"))
                    , new Person("刘某", 15, "1666666666", new BigDecimal("2"))
                    , new Person("收到", 11, "1777777", new BigDecimal("3"))
                    , new Person("刘某", 1, "18888888", new BigDecimal("4"))
                    , new Person("收到", 32, "1999999", new BigDecimal("5"))
                    , new Person("刘某", 1231, "120000000", new BigDecimal("6"))
                    , new Person("收到", 2, "1422000022", new BigDecimal("7"))
                    , new Person("刘某", 23, "18888888", new BigDecimal("8"))
                    , new Person("收到", 25, "18888888", new BigDecimal("9"))
            );
    ​
            excelUtil.setContent(content);
    ​
            //订单金额、数量、系数、单价、减项、加项
            excelUtil.setTail(
                    Arrays.asList(
                            new ExcelUtil.ExcelContent.CellField("总人数", null, titleCellFormatter)
                            , new ExcelUtil.ExcelContent.CellField("总金额为", null, titleCellFormatter)
                            , new ExcelUtil.ExcelContent.CellField("总纳税金额为(50%)", null, titleCellFormatter)
                    ),
                    Arrays.asList(
                            new ExcelUtil.ExcelContent.CellField(content.getColumnNum())
                            , new ExcelUtil.ExcelContent.CellField(content.getObjs().stream().reduce(BigDecimal.ZERO, (t, u) -> t.add(u.deposit == null ? BigDecimal.ZERO : u.deposit), BigDecimal::add),null,moneyCellFormat1)
                            , new ExcelUtil.ExcelContent.CellField(content.getObjs().stream().reduce(BigDecimal.ZERO, (t, u) -> t.add(u.deposit == null ? BigDecimal.ZERO : u.deposit), BigDecimal::add), (value) -> ((BigDecimal) value).multiply(new BigDecimal("0.5")),moneyCellFormat1
                            )
                    )
    ​
            );
    ​
            Workbook workbook = excelUtil.gnnerate();
            FileOutputStream outputStream = null;
            try {
                outputStream = new FileOutputStream("E://1.xls");
                workbook.write(outputStream);
    ​
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                try {
                    if (outputStream != null) {
                        outputStream.close();
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
    }
        


