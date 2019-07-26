---
title: Struts2图片上传(回显、多图上传、格式控制、多图上传分别保存路径到数据库不同的列)
date: 2013-10-12 11:38:57
categories:
- 服务端开发
- Java
tags: 
- Struts2
- 文件上传
description: "需求：1. 选中店铺后，选中上传LOGO或者页脚、或者一次LOGO和页脚都上传。2. 上传后，LOGO和页脚保存到表的两个列"
---

页面如下：
![](http://ww1.sinaimg.cn/large/006tNc79ly1g5d8085kmkj30hx054wee.jpg)

需求：
1. 选中店铺后，选中上传LOGO或者页脚、或者一次LOGO和页脚都上传
2. 上传后，LOGO和页脚保存到表的两个列

关键：
单张图上传、怎么判断上传的是LOGO还是页脚

表如下：
![](http://ww3.sinaimg.cn/large/006tNc79ly1g5d808q385j30ku01tt9o.jpg)

upload.jsp
```java
……….
<s:hidden id="img_type" name="imgType" value="" />
<tr>
    <td class="STYLE1">上传LOGO：<s:file id="img" name="img"/></td>
</tr>
<tr>
    <td class="STYLE1">上传页脚：<s:file id="foot" name="img"/></td>
</tr>
<tr>
    <td align="left">
    <input type="button" id="img_btn" value="上传" />
    <span style="color:red;">
    <s:actionerror/>
    <s:fielderror />
    <s:if test="message!= null">
    <s:property value="message" />
    </s:if>
    </span>
    </td>
</tr>
<tr>
    <td class="STYLE1" align="left">预览：</td>
</tr>
<tr>
    <td class="STYLE1" align="left">
    <img src="${pageContext.request.contextPath}/${imgSrcLogo}" alt="这是LOGO" title="这是LOGO" width="100" height="100" />
</td>
</tr>
<tr>
    <td class="STYLE1" align="left">
    <img src="${pageContext.request.contextPath}/${imgSrcFoot}" alt="这是页脚" title="这是页脚" width="600" height="350" />
    </td>
</tr>
```

这里面有个隐藏表单域`<s:hidden id="img_type" name="imgType" value="" />`，用来区分上传的是logo还是foot页脚

这些控制需要用JS来做：
```java
/** 图片上传 */
$("#img_btn").click(function(){
  var img = $("#img");
  var foot = $("#foot");
  var shopId = $("#shopIdSelect");
  var msg="";
  if(shopId.val()<"0"){
     msg += "请选择店铺！";
     shopId.focus();
  }else if(img.val()=="" && foot.val()==""){
     msg += "请选择要上传的图片！";
  }else if(img.val()!="" && foot.val()==""){    //如果上传的是LOGO
     document.getElementById('img_type').value="logo";
  }else if(img.val()=="" && foot.val()!=""){    //如果上传的是页脚
     document.getElementById('img_type').value="foot";
  }

  if (msg != ""){
     alert(msg);
  }else{
  // 表单提交
     $("#uploadForm").submit();
  }        
});  
```

在这里使用`document.getElementById('img_type').value="logo";`进行复制，传递到后台

Action：uploadLogoAction.java
```java
public class uploadLogoAction extends ActionSupport{
private static final long serialVersionUID = 1590947402900249195L;
/** 注入业务层*/
private SystemManageService systemManageService;
private ShoppingMerchantShop shoppingMerchantShop;
private List<ShoppingMerchantShop> shoppingMerchantShopList;
/** 图片上传的三个属性 */
private File[] img;
private String[] imgFileName;
private String[] imgContentType;
/** 店铺ID */
private String shopId;
private String imgType;
/** 图片上传 只要一张不符合要求另一张也不能上传*/
public String uploadPic(){
    try {
        //设定拖存储在服务器的路径
        String realPath = "/skin/"+shopId+"/sitemesh";
        String savePath = ServletActionContext.getServletContext().getRealPath(realPath);
        System.err.println("项目部署的路径："+savePath);
        int index = 0;
        for(File file : img){
            if(this.filterTypes(imgContentType)){
                
                FileUtils.copyFile(file, new File(savePath+File.separator+imgFileName[index]));
                //图片路径
                String picPath = realPath+"/"+imgFileName[index];
                
                ActionContext.getContext().put("shopId",shopId );                
                //上传成功后保存图片路劲到店铺表
                shoppingMerchantShop = new ShoppingMerchantShop();
                shoppingMerchantShop.setShopId(shopId);
                if(index == 0){    //如果是LOGO
                    if(imgType.equals("foot")){    //如果只上传了一个，如果上传的是页脚
                        shoppingMerchantShop.setShopFoot(picPath);
                        //保存上传后的路径、用于页面上回显
                        ActionContext.getContext().put("imgSrcFoot",picPath);
                    }else{
                        shoppingMerchantShop.setShopLogo(picPath);                        
                        //保存上传后的路径、用于页面上回显
                        ActionContext.getContext().put("imgSrcLogo",picPath);
                    }
                }else{    //如果是页脚
                    shoppingMerchantShop.setShopFoot(picPath);
                    ActionContext.getContext().put("imgSrcFoot",picPath);
                }
                index++;                

                systemManageService.updateMerchantShop(shoppingMerchantShop);
                setMessage("上传成功！");
            }
        }
    } catch (Exception ex) {
        setMessage("上传失败！");
        log(ex);
    }
    return SUCCESS;
}

/**
 * 判断图片格式是否符合要求
 * @param types 图片格式
 * @return
 */
public boolean filterTypes(String[] types){
    //WebConstant.uploadImgTypes是一个数组，存放图片格式{"image/png","image/jpeg"...}
    String[] imgTypes = WebConstant.uploadImgTypes;
    Boolean flag =true;
    for(String type : types){
        for(String trueType : imgTypes){
            if(!trueType.equals(type)){
                flag = false;
                setMessage("上传的图片类型是不被允许的！");
            }else{
                flag = true;
            }
        }
    }
    return flag;
}

/** get and set  */
......
}
```