# vue-好看的版本更新记录
## 更新历史

名称 | 内容
---|---
版本 | 1.0
创建时间 | 2021-10-26
更新时间 | 2021-10-26

## 功能清单
- 基于vue、element实现版本更新日志的显示
- 基于json文件记录更新日志

## 最终效果
![image](https://blog.pettyfox.top/blog/images/2021-10/vue-好看的版本更新记录1.png)

## 集成流程
### 拉取github地址
> [https://github.com/mathcoder23/vue-version-log](https://github.com/mathcoder23/vue-version-log)

### 集成到Spring Boot
> 拷贝github中的资源到Spring Boot的resouce文件夹的version下。并创建以下Controller

```java
package com.eface.platform.ai.administer.gateway.commapi;

import lombok.extern.slf4j.Slf4j;
import org.pettyfox.base.web.dto.rest.RestObjectResponse;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.Resource;
import org.springframework.core.io.ResourceLoader;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletResponse;
import java.io.*;
import java.nio.charset.StandardCharsets;

/**
 * 服务器软件版本信息
 *
 * @author Petty Fox
 */
@Controller
@RequestMapping("/api/commApi/version")
@Slf4j
public class VersionController {

    @Value("${project.version}")
    private String serviceVersion;
    @javax.annotation.Resource
    private ResourceLoader resourceLoader;

    @GetMapping("version.html")
    public void version(HttpServletResponse response) throws IOException {
        response(response, "classpath:/version/version.html", "text/html");
    }

    @GetMapping("element-index.css")
    public void element(HttpServletResponse response) throws IOException {
        response(response, "classpath:/version/element-index.css", "text/css");
    }

    @GetMapping("element-index.js")
    public void elementJs(HttpServletResponse response) throws IOException {
        response(response, "classpath:/version/element-index.js", "application/javascript");
    }

    @GetMapping("vue.js")
    public void vue(HttpServletResponse response) throws IOException {
        response(response, "classpath:/version/vue.js", "application/javascript");
    }

    @GetMapping("version.json")
    public void versionJson(HttpServletResponse response) throws IOException {
        response(response, "classpath:/version/version.json", "application/json");
    }

    @GetMapping("/")
    @ResponseBody
    public RestObjectResponse<String> version() {
        return RestObjectResponse.ok(serviceVersion);
    }

    private void response(HttpServletResponse response, String filename, String type) throws IOException {
        Resource resource = resourceLoader.getResource(filename);
        InputStream is = resource.getInputStream();
        InputStreamReader isr = new InputStreamReader(is, StandardCharsets.UTF_8);
        BufferedReader br = new BufferedReader(isr);
        String data = "";
        StringBuffer sb = new StringBuffer();
        while ((data = br.readLine()) != null) {
            sb.append(data).append("\n");
        }
        br.close();
        isr.close();
        is.close();

        String html = sb.toString();
        response.setContentType(type + ";charset=utf-8");
        PrintWriter out = response.getWriter();
        out.println(html);
    }
}


```

`访问路径：/api/commApi/version/version.html`

### 更新日志到`version.json`
```java
{
  "title": "xxx平台更新日志",
  "logs": [
    {
      "date": "2021-10-25",
      "version": "1.0.2",
      "contentFeature": [
        "调整版本号显示"
      ],
      "contentBug": [
      ]
    },
    {
      "date": "2021-09-30",
      "version": "1.0.0",
      "content": [
        "新增 初始化框架",
        "新增 初始化框架"
      ]
    }
  ]
}

```
> 每次更新，添加一条logs日志

