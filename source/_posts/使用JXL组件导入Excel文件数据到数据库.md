---
title: 使用JXL组件导入Excel文件数据到数据库
date: 2013-11-15 23:19:20
categories:
- 服务端开发
- Java
tags:
- 组件使用
---

一、功能需求和设计功能：

1. 点击浏览选择一个Excel文件，点击导入，即把Excel文件里的数据传输到数据库
2. 过滤上传文件类型
3. 需要验证文件标题顺序是否正确
4. 表格字段验证
5. 操作过程删除上传的文件
<!-- more -->
功能界面如下：
![](//ww4.sinaimg.cn/large/006tNc79ly1g5d8g5x1psj30ku08ytb9.jpg)

**注意**
1. Excel文件数据格式需要先约定好（模板），随便乱七八糟的可不行。
2. 上传MS Office 2007以上版本、WPS Office需要添加MIME文件类型，详见《文件上传支持WPS Office、MS Office2003-2010的办法》

![](//ww1.sinaimg.cn/large/006tNc79ly1g5d8gck5q0j30jg07s0sy.jpg)

二、思路：

先上传、再读取

三、上传（本人使用Struts2+iBatis+Spring框架，上传部分自然也是Struts2方式上传）

上传主题代码：
```java
/** 导入xls数据-UIM卡信息 */  
private File excl;
private String exclFileName;
private String exclContentType;
/** 上传Excel文件 */
public String import_xls(){
    try {
        //设定拖存储在服务器的路径
        String path = WebConstant.UIM_EXCEL_PATH;
        String realPath = ServletActionContext.getServletContext().getRealPath(path);
            
        //设置新的文件名
        String newFileName = this.generateFileName(exclFileName);
        String saveFilePath = realPath + File.separator + newFileName;
        FileUtils.copyFile(excl, new File(saveFilePath));
        
        //读取Excel文件
        this.import_xls(new File(saveFilePath));
            
    } catch (Exception ex) {
        setMessage("导入失败！");
        log(ex);
    }
    return SUCCESS;
}
/** 省略getter and setting method */
```

**文件重命名**

由于本人项目中上传的文件都有一个固定的约定格式，是这样的：xxx-2010-09-09-admin.xls即，原文件名+日期+上传者.xls
```java
**
 * 重命名上传文件
 * @param oldFileName 旧文件名
 * @return 新文件名
 */
public String generateFileName(String oldFileName) 
{
        //xxxxx-2013-11-07-admin.xls
        Date dateNow=new Date();  
        SimpleDateFormat  dateFormat=new SimpleDateFormat ("yyyy-MM-dd");  
        String dateNowStr=dateFormat.format(dateNow);  
        //获取文件名（不带后缀）
        String fileBaseName = FilenameUtils.getBaseName(oldFileName);
        //获取文件后缀
        String extension = FilenameUtils.getExtension(oldFileName);
        //新文件名
        String newFileName = fileBaseName+"-"+dateNowStr+"-"+WebConstant.getSessionUser().getFxUsername()
            +"."+extension;
        return newFileName;
}
四、下面是解析Excel，导入Excel的代码：
/**
 * 导入Excel源文件
 * @param file 要导入的Excel源文件
 */
private void import_xls(File file) {
    Workbook workbook = null;
    List<ShoppingUimCard> uim_list = new ArrayList<ShoppingUimCard>();
    try {
        InputStream is = new FileInputStream(file); 
        //获取工作薄和第一个工作单
        workbook = Workbook.getWorkbook(is);
        Sheet sheet = workbook.getSheet(0);
        // 拿到列，行 
        //int column = sheet.getColumns(); 
        int row = sheet.getRows();
            
        //定义开始的一行
        int rowStart = 1;
            
        //获取第一行的标题行,并判断顺序是否正确
        if(this.checkTitleRule(sheet)){
            //循环行,标题行的下一行开始
            for (int i = rowStart; i < row; i++) {
                //输入一张卡的信息
                ShoppingUimCard uim = new ShoppingUimCard();
                Cell[] cells = sheet.getRow(i); 
                //int cellsLength = cells.length;
                    
                //天翼靓号
                uim.setUimCard(this.checkDataFormat("UIM_CARD",cells[0]));    
                //预存话费
                uim.setUimMoney(new BigDecimal(this.checkDataFormat("NUMBER",cells[1])));    
                //每月最低消费金额
                uim.setUimMin(new BigDecimal(this.checkDataFormat("NUMBER",cells[2])));        
                //签约时长
                uim.setUimTime(new BigDecimal(this.checkDataFormat("DATE",cells[3])));   
                //号池方向
                uim.setUimKind(this.checkDataFormat("NUMBER",cells[4]));    
               //UIM_ICCID
             uim.setUimIccid(this.checkDataFormat("NUMBER",cells[5]));                    
                    
                //UIM卡其他信息
                uim.setCreateDate(new Date());
                uim.setUpdateDate(new Date());
                uim.setIsActive("1");
                uim.setUimType(new BigDecimal(uim.getUimCard().length()));
                uim.setUserId(WebConstant.getSessionUser().getId()+"");
                uim.setDel(null);
                uim.setBookTime(null);
                uim.setUimShow(null);
                uim.setOrderId(null);
                uim.setUimShopType(null);
                uim.setComboId(null);
                uim.setAssProfit(null);
                uim.setItemCompany(null);
                uim.setUimShowDetail(null);
                uim.setCityName(null);
                uim.setProvince(null);
                uim.setShopid(null);
                    
                uim_list.add(uim);
            }
            //插入数据到数据库
            uimManageService.insertUimCard(uim_list);
            setMessage("导入成功！");
        }else{
            throw new ShopManageException("导入UIM基础信息时出现错误:模板标题顺序不符合要求");
        }
    } catch (Exception e) {
        setMessage("导入失败，读取Excel文件失败！");
        System.err.println("插入Excel表格数据到数据库失败！：UimManageAction.insert_uim_card()");
        deleteFile(file);
        log(e);
    }finally{
        workbook.close();
    }
}
```

五、获取第一行的标题行,并判断顺序是否正确：
```java
/*
  * 获取第一行的标题行,并判断顺序是否正确
  * @param sheet
  */
private boolean checkTitleRule(Sheet sheet) {
    Cell[] cells = sheet.getRow(0);
    boolean flag = true;
    //规定的标题顺序
    String[] title = WebConstant.IMPORT_XLS;
    for(int i=0;i<cells.length;i++){
        System.out.print(cells[i].getContents() + "\t");
        for(int j=0;j<title.length;j++){
            if(!cells[0].getContents().equalsIgnoreCase(title[0].toString())){
                flag = false;
                System.err.println("Error:模板标题'"+cells[i].getContents()+"'顺序不符合要求");
            }
        }
    }
    return flag;
}
```

六、检查每个单元格数据是否符合要求：
```java
/**
 * 检查数据是否符合要求
 * @param contents 列值
 * @return
 */
private String checkDataFormat(String title,Cell cells) {
    String contents = cells.getContents();
    boolean flag = true;
    //检查天翼靓号
    if("UIM_CARD".equalsIgnoreCase(title) && cells.getType()==CellType.NUMBER){
        Pattern p = Pattern.compile("^((13[0-9])|(15[^4,\\D])|(18[0,5-9]))\\d{8}$");  
        Matcher m = p.matcher(contents);
        if(!m.matches()){
           flag = false;
           throw new ShopManageException("导入UIM基础信息时出现错误:天翼靓号不符合格式要求");
         }
    }
    //检查是否是数字,整数或者小数
    if("NUMBER".equalsIgnoreCase(title) || "DATE".equalsIgnoreCase(title)){
         if(cells.getType()!=CellType.NUMBER){
         flag = false;
             throw new ShopManageException("导入UIM基础信息时出现错误:检查数据是否符合要求没通过");
         }
    }
    //检查时间日期(签约时长)
//        if("DATE".equalsIgnoreCase(title)){
//            if(cells.getType() != CellType.DATE){
//                flag = false;
//                throw new ShopManageException("导入UIM基础信息时出现错误:日期格式错误");
//            }
//        }
    if(!flag){
        throw new ShopManageException("导入UIM基础信息时出现错误:检查数据是否符合要求没有通过，请检查数据!");
    }
    return contents;
}
```

七、当操作出错时删除服务器上的文件：
```java
/**
 * 删除文件
 * @param fileName 源文件
 * @return false and true
 */
private static boolean deleteFile(File fileName){
    if(fileName.isFile() && fileName.exists()){
        fileName.delete();
        System.err.println("删除单个文件"+fileName.getName()+"成功！");
        return true;
    }else{
        System.err.println("删除单个文件"+fileName.getName()+"失败！");
        return false;
    }
}
```

八、过滤文件类型

本人使用JS控制，当然你要可以使用Struts2框架进行校验：
```java
$("#btn_submit").click(function(){
    var excl = $("#excl");
    var suffix = excl.val().split(".")[1];
    if(excl.val() == ""){
        $("#picMsg").text("请选择上传的Excl文件！");
    }else if(suffix!="xls"){
        $("#picMsg").text("格式不正确，请选择上传的Excl文件！");
    }else{
        $("#uploadUimDataForm").submit();
    }
});
```

这个JS校验只是简单的判断文件后缀是否正确，当这是不够严谨的，你可以做的更好