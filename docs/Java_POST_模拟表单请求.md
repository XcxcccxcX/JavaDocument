# Java POST 模拟表单提交
  
> 作者：xcx  
> 时间：2020/7/8  09：35  
  
-----------------  

```java
            //两种方法几乎相同


            //第一种 form表单格式   application/x-www-form-urlencoded
            String postURL = "http://xxxxxxxxxxx";
            PostMethod postMethod = null;
            postMethod = new PostMethod(postURL) ;
            postMethod.setRequestHeader("Content-Type", "application/x-www-form-urlencoded") ;
            //这里是从jedis中获取的json数据 可根据需求 使用其他方法
            Jedis jedis = redisUtil.getJedis();
            String str=jedis.get(outTradeNo);
            JSONObject jsonObject= JSON.parseObject(str);
            for (JSONObject.Entry<String, Object> entry : jsonObject.entrySet()) {
                postMethod.setParameter(entry.getKey(), (String) entry.getValue());
                System.out.println(entry.getKey()+","+entry.getValue());
            }
            org.apache.commons.httpclient.HttpClient httpClient = new org.apache.commons.httpclient.HttpClient();
            int response = httpClient.executeMethod(postMethod); // 执行POST方法
            String result = postMethod.getResponseBodyAsString() ;
            System.out.println("aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"+result);
            return result;

            //第二种  application/x-www-form-urlencoded
            try {
                String postURL
                PostMethod postMethod = null;
                postMethod = new PostMethod(postURL) ;
                postMethod.setRequestHeader("Content-Type", "application/x-www-form-urlencoded;charset=utf-8") ;
                //参数设置，需要注意的就是里边不能传NULL，要传空字符串
                 NameValuePair[] data = {
                         new NameValuePair("startTime",""),
                         new NameValuePair("endTime","")

                 };

                postMethod.setRequestBody(data);

                org.apache.commons.httpclient.HttpClient httpClient = new org.apache.commons.httpclient.HttpClient();
                int response = httpClient.executeMethod(postMethod); // 执行POST方法
                String result = postMethod.getResponseBodyAsString() ;

                return result;
                } catch (Exception e) {
                    logger.info("请求异常"+e.getMessage(),e);
                    throw new RuntimeException(e.getMessage());
                }

            
```


# Java 获取POST请求中的参数 
` 背景介绍: 第三方支付回调请求 加密方式通过 RSA非对称加密 返回参数有二 userId,notify  notify 是加密后的字符串(文件) 很大我也不清楚是不是文件.. 好多方法都不适用 下面这个方法适用
```java
    //第一种
    public void xh(HttpServletRequest request,HttpServletResponse response){
        DiskFileItemFactory factory = new DiskFileItemFactory();
        ServletFileUpload upload = new ServletFileUpload(factory);
        upload.setHeaderEncoding("UTF-8");
        List items = upload.parseRequest(request);
        Map param = new HashMap();
        for(Object object:items){
            FileItem fileItem = (FileItem) object;
            if (fileItem.isFormField()) {
                param.put(fileItem.getFieldName(), fileItem.getString("utf-8"));//如果你页面编码是utf-8的
            }
        }
        Object notify = param.get("notify"); //成功获取
        .......
        //成功后向其返回字符串 OK
        PrintWriter out = response.getWriter();
        out.print("OK");
        out.flush();
        out.close();

    }
```

```java
//appliction/x-www-form-urlencoded 和  application/json
public static Map<String, Object> getParam4Post(HttpServletRequest request) throws Exception {
        
　　　　　// 返回参数
        Map<String, Object> params = new HashMap<>();
        
        // 获取内容格式
        String contentType = request.getContentType();
        if (contentType != null && !contentType.equals("")) {
            contentType = contentType.split(";")[0];
        }
        
        // form表单格式  表单形式可以从 ParameterMap中获取
        if ("appliction/x-www-form-urlencoded".equalsIgnoreCase(contentType)) {
            // 获取参数
            Map<String, String[]> parameterMap = request.getParameterMap();
            if (parameterMap != null) {
                for (Map.Entry<String, String[]> entry : parameterMap.entrySet()) {
                    params.put(entry.getKey(), entry.getValue()[0]);
                }
            }
        }
        
        // json格式 json格式需要从request的输入流中解析获取
        if ("application/json".equalsIgnoreCase(contentType)) {
            // 使用 commons-io中 IOUtils 类快速获取输入流内容
            String paramJson = IOUtils.toString(request.getInputStream(), "UTF-8");
            Map parseObject = JSON.parseObject(paramJson, Map.class);
            params.putAll(parseObject);
        }
        
        return params ;
    }

```


