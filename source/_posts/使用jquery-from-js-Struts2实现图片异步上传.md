---
title: 使用jquery.from.js+Struts2实现图片异步上传
date: 2013-09-19 12:42:29
categories: 
- 技术
tags:
- 后端
- Java
- jQuery
- Struts2
description: "jquery.form.js是jQuery的一个官方用语支持异步上传文件的插件。官方网站：http://plugins.jquery.com/form/ ，结合Struts2三步轻松实现文件上传。"
---

jquery.form.js是jQuery的一个官方用语支持异步上传文件的插件。官方网站：[http://plugins.jquery.com/form/](http://plugins.jquery.com/form/) ，结合Struts2三步轻松实现文件上传。

一般是针对一个页面可能不止一个Form表单，所以在一个面提交表单会影响到另一个表单，为此，图片上传表单就可以使用无刷新提交方式上传，也就是异步上传，这时jquery.from.js就派上用场了。

**在HTML页面上导入JS**
```html
<script type="text/javascript" src="/js/jquery.form.js"></script>
<!—图片上传 -->
<s:form id="picForm" name="picForm" action="/notice/showAddNotice.action" method="post"
   enctype="multipart/form-data">
  <input type="file" name="pic"  size="30"/><input type="submit" value="上传"/>
</s:form>
```

**写JS**
```js
// 为表单绑定异步上传的事件
$(function(){
    // 为表单绑定异步上传的事件
    $("#picForm").ajaxForm({
        url : "${pageContext.request.contextPath}/notice/uploadPic.action", // 请求的url
        type : "post", // 请求方式
        dataType : "text", // 响应的数据类型
        async :true, // 异步
        success : function(imageUrl){
        //alert(imageUrl);
        },
        error : function(){
            alert("数据加载失败！");
        }
    });
    // 为提交按钮绑定事件
    $("#saveBtn").click(function(){
    // 表单输入较验
    var title = $("#title");
    // 获取textarea的内容
    var content = tinyMCE.get('content').getContent();
    var msg = "";
    if ($.trim(title.val()) == ""){
       msg = "公告标题不能为空！";
       title.focus();
    }else if ($.trim(content) == ""){
       msg = "内容不能为空！";
    }
         msg = "";
    if (msg != ""){
        alert(msg);
    }else{
    // 表单提交
    $("#noticeForm").submit();
  }
});
```

**写Struts2代码**
```java
public class uploadPicAjax extends AbstractAjaxAction {
private static final long serialVersionUID = -4742151106080093662L;
/** Struts2文件上传的三个属性 */
private File pic;
private String picFileName;
private String picContentType;
@Override
protected String getJson() throws Exception {
     //获取项目部署的路径
     String realPath = ServletActionContext.getServletContext().getRealPath("/images/notice");
     //生成新的文件名
     //获取文件的后缀名 aa.jpg --> jpg
     String newFileName=UUID.randomUUID().toString()+"."+FilenameUtils.getExtension(picFileName);
     FileUtils.copyFile(pic, new File(realPath + File.separator + newFileName));
     return "/images/notice/" + newFileName;
}
/** setter and getter method  **/
        ......
}
```
我这里继承了一个抽象的的处理Ajax的基类，所以直接返图片地址回到页面。你可以return到一个视图。

**配置Struts2.xml**
```html
<!-- 图片的异步上传 -->
<action name="uploadPic" class="com.wise.hrm.action.notice.uploadPicAjax"></action>
```

好了，从页面到后台就已经写完了、、、、这样就可以上传了。完毕！