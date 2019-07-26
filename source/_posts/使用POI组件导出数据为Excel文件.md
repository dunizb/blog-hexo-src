---
title: 使用POI组件导出数据为Excel文件
date: 2014-04-09 13:53:19
categories:
- 服务端开发
- Java
tags:
- 组件使用
---

以下例子为HR系统中一个员工管理模块，导出员工数据为Excel文件的功能。

系统结构为：Struts2+MyBaties+Spring3+MySQL

![](http://ww4.sinaimg.cn/large/006tNc79ly1g5d7z9zoggj30ku04o3zh.jpg)

HTML、JS：
```js
<input type="button" value="导出EXCEL" onclick="excelFn();"/>
/** 生成Excel文件 */
function excelFn(){
    document.location = "${pageContext.request.contextPath}/employee/downExcel.action";
};
```

Java Action：
```java
/** 员工资料导出Excel */
public String downExcel(){
    try{
          // 调用业务层导出员工资料
          hrmService.exportEmployeeInfo(employee);
    }catch(Exception ex){
           log(ex);
    }
    return NONE;
}
```

Java Service：
```java
/**
 * 导出员工资料
 * @param employee
 */
public void exportEmployeeInfo(Employee employee){
    try{
           // 查询条件判断(对查询参数进行封装)
           Map<String, Object> params = new HashMap<String, Object>();
           if (employee != null){
               if (!StringUtils.isNullOrEmpty(employee.getName())){
                    params.put("name", "%" + employee.getName() + "%");
               }
               if (!StringUtils.isNullOrEmpty(employee.getCardId())){
                    params.put("cardId", "%" + employee.getCardId() + "%");
               }
               if (!StringUtils.isNullOrEmpty(employee.getPhone())){
                    params.put("phone", "%" + employee.getPhone() + "%");
               }
               params.put("employee", employee);
            }
            List<EmployeeInfo> emps = employeeMapper.getEmployeeInfo(params);
            // 调用工具类来导出员工资料
            // 
            String[] titleHeader = {"编号","姓名","性别", "手机号码","邮箱", "职位","学历","身份证号码"," 部门","联系地址","建档日期"};
            ExcelUtils.export("员工信息", "员工资料", titleHeader, 
                        ServletActionContext.getResponse(),
                        ServletActionContext.getRequest(), emps);
        }catch(Exception ex){
            throw new HrmException("导出员工资料时出现异常！", ex);
        }
    }
}
```

ExcelUtils.java：
```java
/**
 * Excel操作的工具类
 * @date 2013-5-29 上午9:47:52
 * @version 1.0
 */
public class ExcelUtils {
    
    /**
     * 导出Excel的方法
     * @param excelFileName 生成Excel文件的名称
     * @param sheetname 工作单的名称
     * @param titleHeader (第一行)标题行
     * @param respone 响应
     * @param request 请求
     * @param data 数据
     */
    public static void export(String excelFileName, String sheetname, String[] titleHeader, 
                        HttpServletResponse response, 
                        HttpServletRequest request, List<?> data) {
        /** 创建工作簿 */
        HSSFWorkbook workbook = new HSSFWorkbook();
        /** 创建工作单*/
        HSSFSheet sheet = workbook.createSheet(sheetname);
        
        /** 创建第一行作为标题行 */
        HSSFRow row = sheet.createRow(0);
        /** 循环创建第一行中的单元格 */
        for (int i = 0; i < titleHeader.length; i++){
            HSSFCell cell = row.createCell(i);
            cell.setCellValue(titleHeader[i]);
        }
        
        try{
            /** 下面把集合中的数据写到Excel中  (从第二行开始)*/
            for (int i = 0; i < data.size(); i++){
                row = sheet.createRow(i + 1);
                // 从集合中获取一个实体
                Object obj = data.get(i);
                // 获取这个实体中的所有属性
                Field[] fields = obj.getClass().getDeclaredFields();
                // 循环该实体中所有属性
                for (int j = 0; j < fields.length; j++){
                    // 创建单元格
                    HSSFCell cell = row.createCell(j);
                    //  判断该Field是否能访问
                    Field field = fields[j];
                    if (!field.isAccessible()) field.setAccessible(true);
                    // 获取该Field的值
                    Object res = field.get(obj);
                    // 设置单元格的值
                    cell.setCellValue(res.toString());
                }
            }
            try {
                // 判断浏览器类型
                String userAgent = request.getHeader("user-agent");
                if (userAgent.toLowerCase().indexOf("msie") != -1){ //MSIE
                    excelFileName = URLEncoder.encode(excelFileName, "utf-8");
                }else{
                    excelFileName = new String(excelFileName.getBytes("utf-8"), "iso-8859-1");
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
            // 设置响应头
            response.setHeader("Content-Disposition", "attachment;filename=" + excelFileName + ".xls");
            // 输出Excel
            workbook.write(response.getOutputStream());
        }catch(Exception ex){
            ex.printStackTrace();
        }
    }
}
```

代码完成后点击导出按钮，结果如图：
![](http://ww4.sinaimg.cn/large/006tNc79ly1g5d7zaw7csj30eo0973yk.jpg)
