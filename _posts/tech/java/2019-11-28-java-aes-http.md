---
layout: post
title: java aes加解密和http请求的一个示例
category: 技术
tags: [java,aes,http,hutool]
keywords: [java,aes,http,hutool]
---

### 说明
jdk1.8 版本工具类 SecurityUtils 提供aes 加解密方法
```java
import org.apache.commons.lang3.StringUtils;
//key为加解密公钥
//加密
SecurityUtils.encodeAES256HexUpper(data, SecurityUtils.decodeHexUpper(key))
//解密
SecurityUtils.decodeAES256HexUpper(data, SecurityUtils.decodeHexUpper(key));
```
[hutool](www.hutool.cn)工具几乎集合了常用的所有工具类使用简单,文档也很全

[hutool文档](https://hutool.cn/docs/#/)

[hutoll类文档](https://apidoc.gitee.com/loolly/hutool/)

```java
import cn.hutool.json.*;  //json 操作类
import cn.hutool.http.*;  //http 请求类
import cn.hutool.core.util.*; //核心类 StrUtil 字符串工具类
```

### 类示例
```java
import org.apache.commons.lang3.StringUtils;
import java.util.Scanner;
import cn.hutool.json.*; 
import cn.hutool.http.*;
import cn.hutool.core.util.*;

/**
 * 统一消息发送类
 * 
 *
 */
public class MsgSendImpl {

    private String key = "d8f92665e236bbbbed5d7be2bebfe867";
    private String getTokenUrl = "";
    private String infoSendUrl  = "";
    private String appCode = "";
    private String accessToken = "36418505-3125-4fd0-8f2e-8e75a4442e26";
    private String apiVersion = "1.0.1";
    private String apiCode = "smsSend";
    private String format = "application/json"; 
    /*
     * AES解密
     */
    public static String decrypt(String data, String key) throws Exception {
        if (StringUtils.isBlank(data) || StringUtils.isBlank(key)){
            return data;
        }

        return SecurityUtils.decodeAES256HexUpper(data, SecurityUtils.decodeHexUpper(key));
    }


    /*
     * AES加密
     */
    public static String encrypt(String data, String key) throws Exception {
        if (StringUtils.isBlank(data) || StringUtils.isBlank(key)){
            return data;
        }

        return SecurityUtils.encodeAES256HexUpper(data, SecurityUtils.decodeHexUpper(key));
    }

    //http post 请求
    public String curl(String access_token,String encodeStr){
        MsgSendImpl msi = new MsgSendImpl();
        HttpRequest hr = new HttpRequest(msi.infoSendUrl);        
        hr.method(Method.POST);
        hr.header("appCode", msi.appCode);
        hr.header("accessToken", access_token);
        hr.header("apiVersion", msi.apiVersion);
        hr.header("format", msi.format);
        hr.body(encodeStr);
        String reponseResult = hr.execute().body();
        return reponseResult; 
    }

    public static String msgSend(String msgType,String jsonStr)  throws Exception {
        MsgSendImpl msi = new MsgSendImpl();
        msi.apiCode = msgType;

        String token = HttpUtil.get(msi.getTokenUrl);
        System.out.println("token : "+token);
        JSONObject json = JSONUtil.parseObj(token);
        String access_token = json.get("access_token").toString(); 
        
        String encodeStr = encrypt(jsonStr,msi.key); 
                
        String result =  msi.curl(access_token,encodeStr);
        String desult = decrypt(result,msi.key);
        return desult; 
    }
 

    public static void main(String[] args){      
        try{
            //短信发送  
            String jsonStr = "{\"appId\":\"21\",\"appSecret\":\"6PLeJxQasRK8bczjTlENAkzR4wTJVjs4\",\"userId\":\"13867060000\",\"templateData\":{\"title\":\"新的外场工单提醒\",\"content\":\"灯塔三村外场工单提醒\",\"value\":\"灯塔三村外场工单提醒value\"},\"templateId\":\"55\"}";            
            String result = msgSend("smsSend",jsonStr);        
            System.out.println("短信结果解密:"+result);

            //微信发送
            jsonStr = "{\"appId\":\"102\",\"appSecret\":\"9ZzR5Mg4VijrUNYrTF3s4PrcqCsyFf4J\",\"userId\":\"oM8vJs3-nNseQO8-Zp4G6MmVcEQU\",\"templateId\":\"56\",\"templateData\":{\"first\":{\"value\":\"你有新的外场执行工单\",\"color\":\"#173177\"},\"keyword1\":{\"value\":\"灯塔村外场执行任务\",\"color\":\"#173177\"},\"keyword2\":{\"value\":\"灯塔三村\",\"color\":\"#173177\"},\"keyword3\":{\"value\":\"2019112011049203\",\"color\":\"#173177\"},\"keyword4\":{\"value\":\"2019年11月20日\",\"color\":\"#173177\"},\"keyword5\":{\"value\":\"李娟\",\"color\":\"#173177\"},\"remark\":{\"value\":\"请确认到场打卡成功,和效益办理率\",\"color\":\"#173177\"}},\"linkUrl\":\"https://www.baidu.com/\",\"skipApplet\":\"0\"}";
            result = msgSend("wechatSend",jsonStr);       
            System.out.println("微信结果解密:"+result);
        }catch(Exception ex){
            ex.printStackTrace();
        }
    }
  
    //测试加解密
    public static void jar() {
        try {
            Scanner scanner = new Scanner(System.in);
            System.out.println("请输入密钥：");
            String key = scanner.nextLine();
            for (;;) {
                System.out.println("请输入需要进行的操作编码(1:加密，2：解密，3：修改密钥，4：退出");
                String oper = scanner.nextLine();
                if ("1".equals(oper)) {
                    System.out.println("请输入加密的字符串：");
                    String content = scanner.nextLine();
                    System.out.println("当前密钥为：" + key);
                    System.out.println("加密结果为：" + encrypt(content, key));
                } else if ("2".equals(oper)) {
                    System.out.println("请输入解密的字符串：");
                    String content = scanner.nextLine();
                    System.out.println("当前密钥为：" + key);
                    System.out.println("解密结果为：" + decrypt(content, key));
                } else if ("3".equals(oper)) {
                    System.out.println("当前密钥为：" + key);
                    System.out.println("请输入新的密钥：");
                    key = scanner.nextLine();
                    System.out.println("新的密钥为：" + key);
                } else if ("4".equals(oper)) {
                    return;
                } else {
                    System.out.println("输如的操作有误，输入的操作为：" + oper);
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```
