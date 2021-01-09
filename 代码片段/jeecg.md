# jeecg

## 1、cq的or查询

```java
Disjunction dis = Restrictions.disjunction();//替代多个Restrictions.or
dis.add(Restrictions.eq("attendanceStatus", "1"));      //未考勤
dis.add(Restrictions.eq("late", "1"));                  //迟到
dis.add(Restrictions.eq("earlyRetreat", "1"));          //早退
cq.add(dis);
cq.add();
```

## 2、返回json报错

```java
JSONObject jsonObject = new JSONObject();
jsonObject.put("personnelList",personnelList);
jsonObject.put("securityList",securityList);
jsonObject.put("perimeterList",perimeterList);

j.setObj(JSONObject.parse(jsonObject.toJSONString()));
```

##  3、在使用jeecg框架图表配置范围查询时间范围无效

找到package org.jeecgframework.web.graphreport.service.impl.core       

```
public String handleElInSQL(String sql, Map params) {
        Pattern p = Pattern.compile("\\{[^}]+\\}");
        Matcher m = p.matcher(sql);
        //如果条件中存在此标签则替换
        while (m.find()) {
            String oel = m.group();
            String el = oel.replace("{", "").replace("}", "").trim();
            //替换{xx:xx}标签
            if(el.indexOf(":") != -1) {
                String[] elSplit = el.split(":");
                String elKey = elSplit[0].trim();
                String elValue = elSplit[1].trim();
                //如果条件中存在此标签则替换
                Object condValue = params.get(elSplit[1].trim());
                if(condValue != null) {
                    sql = sql.replace(oel, elKey + condValue.toString().replace(" " + elValue + " ", " " + elKey + " "));
                }else {
                    sql = sql.replace(oel, "1=1");
                }
                params.remove(elValue);
            }else {
                //替换{xx}标签
                Object condValue = params.get(el);
                if(condValue != null) {
                    if(condValue.toString().indexOf("<=")!=-1) {
                        String condValueStr = condValue.toString();
                        String leftCondValue = condValueStr.substring(condValueStr.indexOf(":"),condValueStr.length());
                        leftCondValue = leftCondValue.replace("_end", "");
                        leftCondValue += "_begin";
                        leftCondValue = el + " >= " + leftCondValue + " and ";
                        sql = sql.replace(oel,leftCondValue+ el + condValue.toString());
                    }else {
                        sql = sql.replace(oel, el + condValue.toString());
                    }
                }else {
                    sql = sql.replace(oel, "1=1");
                }
                params.remove(el);
            }
        }
        return sql;
    }
```

## 4、文件上传

```html
<input type="file" name="upload" id="upload"/>
<input type="hidden" name="video" id="video"/>
```

```java
//监听文件上传后自动上传
$(function () {
        var upload =  $("#upload");
        // ①为input设定change事件
        upload.change(function () {
            //    ②如果value不为空，调用文件加载方法
            if($(this).val() != ""){
                fileLoad(this);
            }
        })
    })


//③创建fileLoad方法用来上传文件
function fileLoad(ele){
    //④创建一个formData对象
    var formData = new FormData();
    //⑤获取传入元素的val
    var name = $(ele).val();
    //⑥获取files
    var files = $(ele)[0].files[0];
    //⑦将name 和 files 添加到formData中，键值对形式
    formData.append("file", files);
    formData.append("name", name);
    $.ajax({
        url: "/cms/upload/upload",
        type: 'POST',
        data: formData,
        processData: false,// ⑧告诉jQuery不要去处理发送的数据
        contentType: false, // ⑨告诉jQuery不要去设置Content-Type请求头
        success: function (responseStr) {
            console.log("上传成功"+JSON.stringify(responseStr7));
            let fileName = responseStr.fileName;
            $('#video').val(fileName)
        }
        ,
        error : function (responseStr) {
            alert("上传失败，请上传10MB以内的文件！")
            console.log("上传失败");
        }
    });
}
```

后台代码，上传文件（通用方法）

```java
@ResponseBody
@PostMapping("/upload")
public UploadResponse upload(HttpServletRequest request) throws Exception {
    Part part = request.getPart("file");
    // 获得提交的文件名
    String fileName = part.getSubmittedFileName();
    // 获得文件输入流
    InputStream ins = part.getInputStream();
    // 获得文件类型
    String contentType = part.getContentType();
    // 将文件存储到mongodb中,mongodb 将会返回这个文件的具体信息
    ObjectId id = gridFsTemplate.store(ins, fileName, contentType);
    System.out.println(ServerController.HTTP + id.toString());

    String url = ServerController.HTTP + id.toString();
    return new UploadResponse(url, fileName, contentType, url, CoreConst.SUCCESS_CODE);
}
```

后台代码，预览图片（通用方法）

```java
/**
 * 预览图片
 * @param fileId
 * @param response
 * @param request
 * @throws IOException
 */
@RequestMapping(value = "/fget", method = RequestMethod.GET)
public void fget(String fileId,String type, HttpServletResponse response, HttpServletRequest request)throws IOException {
    GridFSFile gridFSFile = this.getById(fileId);
    if (gridFSFile != null) {
        // mongo-java-driver3.x以上的版本就变成了这种方式获取
        GridFSBucket bucket = GridFSBuckets.create(mongoDbFactory.getDb());
        GridFSDownloadStream gridFSDownloadStream =                                                bucket.openDownloadStream(gridFSFile.getObjectId());
        GridFsResource gridFsResource = new                                                   GridFsResource(gridFSFile,gridFSDownloadStream);
        String fileName = gridFSFile.getFilename().replace(",", "");
        //处理中文文件名乱码
        if (request.getHeader("User-Agent").toUpperCase().contains("MSIE") ||
                request.getHeader("User-Agent").toUpperCase().contains("TRIDENT")
                || request.getHeader("User-Agent").toUpperCase().contains("EDGE")) {
            fileName = java.net.URLEncoder.encode(fileName, "UTF-8");
        } else {
            //非IE浏览器的处理：
            fileName = new String(fileName.getBytes("UTF-8"), "ISO-8859-1");
        }
        
        //此处multipart/form-data会自动识别
        response.setContentType("multipart/form-data");
        
        
        
        response.setHeader("Content-Disposition", "inline;filename=\"" + fileName + "\"");

        IOUtils.copy(gridFsResource.getInputStream(), rponse.getOutputStream());
    }
}


 /**
     * 据id返回文件
     */
    public GridFSFile getById(String fileId){
        Query query = Query.query(Criteria.where("_id").is(fileId));
        GridFSFile gfsFile = gridFsTemplate.findOne(query);

        return gfsFile;
    }
```





# jeecgboot

## 1、vue实现超出字数中间用省略号显示

```html
<span>{{hashName | ellipsis}}</span>
```

```js
// 写在data中
filters: {
    ellipsis (value) {
        let len=value.length;
        if (!value) return ''
        if (value.length > 20) {
            return value.substring(0,8) + '......' +value.substring(len-8,len)
        }
        return value
    }
},
```

