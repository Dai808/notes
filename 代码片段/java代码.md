## 1、获取昨天的时间

```java
  DateFormat dateFormat=new SimpleDateFormat("yyyy-MM-dd");
  Calendar calendar=Calendar.getInstance();
  calendar.set(Calendar.HOUR_OF_DAY,-24);
  String yesterdayDate=dateFormat.format(calendar.getTime());
```

## 2、mongodb测试代码

```java
@Autowired
private MongoTemplate mongoTemplate;

this.mongoTemplate.insert(zhgyRegion)
```

## 3、阿里大于发送短信

```java
/**
     * @date: Creat in 2020/8/10 18:12
     * @describe:  您的密码已重置为${pwd}，请尽快登录系统进行密码修改。
     * @param mobile
     * @return : void
     */
    public static void sendResetPassword(String mobile, String pwd) {
    // 此处3个参数是根据服务台来的，不是固定的
        DefaultProfile profile = DefaultProfile
            .getProfile("cn-hangzhou", "LTAI4FkiVjA8o6YyJWcLJK43", "4ZHJiI6NY9UJO1rp3PFYmjquQp5CUX");
        IAcsClient client = new DefaultAcsClient(profile);

        CommonRequest request = new CommonRequest();
        request.setMethod(MethodType.POST);
        // 此处往下四句都是固定的
        request.setDomain("dysmsapi.aliyuncs.com");
        request.setVersion("2017-05-25");
        request.setAction("SendSms");
        request.putQueryParameter("RegionId", "cn-hangzhou");
        // yaojie
        request.putQueryParameter("PhoneNumbers", mobile);
        request.putQueryParameter("SignName", "腾世");
        request.putQueryParameter("TemplateCode", "SMS_199600234");
        request.putQueryParameter("TemplateParam", "{\"pwd\":\"" + pwd + "\"}");
        try {
            CommonResponse response = client.getCommonResponse(request);
            System.out.println(response.getData());
        } catch (ServerException e) {
            e.printStackTrace();
        } catch (ClientException e) {
            e.printStackTrace();
        }
    }

```

```java
/*public List<String> getChild(List<TreeSelectRegion> list, List<String> hbId) {
    for (TreeSelectRegion region : list) {
        hbId.add(region.getId());
        List<TreeSelectRegion> children = region.getChildren();
        if (children != null && children.size() > 0) {
            getChild(children, hbId);
        }
    }
    return hbId;
}*/
```