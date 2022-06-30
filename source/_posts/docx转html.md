---
title: docx转html
tags: []
categories: []
date: 2022-06-30 10:57:46
---

最近需要一个 word 预览的功能，直接预览有些麻烦，就想把文件转换一下，于是就有了下面的功能。

<!-- more -->

## 依赖

```xml
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.8.4</version>
</dependency>
<!--集成apache poi word 转html -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>5.2.2</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-scratchpad</artifactId>
    <version>5.2.2</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>5.2.2</version>
</dependency>
<dependency>
    <groupId>fr.opensagres.xdocreport</groupId>
    <artifactId>fr.opensagres.poi.xwpf.converter.xhtml</artifactId>
    <version>2.0.3</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml-schemas</artifactId>
    <version>4.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>ooxml-schemas</artifactId>
    <version>1.4</version>
</dependency>
```

## 方法

```java

import cn.hutool.core.io.file.FileReader;
import fr.opensagres.poi.xwpf.converter.xhtml.XHTMLConverter;
import fr.opensagres.poi.xwpf.converter.xhtml.XHTMLOptions;
import org.apache.poi.xwpf.usermodel.XWPFDocument;

import java.io.*;
import java.nio.charset.StandardCharsets;

public class WordTransHtml {

    public static void main(String[] args) throws Exception {
        // TODO Auto-generated method stub
        String docPath = "/Users/dctxf/workspace/xzr/chelizi/word2html/word.docx";
        String htmlPath = "/Users/dctxf/workspace/xzr/chelizi/word2html/word.html";

        // 获取文件流
        FileReader fileReader = new FileReader(docPath);
        fileReader.getCharset();
        BufferedInputStream inputStream = fileReader.getInputStream();

        // 创建writer
        FileWriter writer = null;
        try {
            writer = new FileWriter(htmlPath);
            String s = docxToHtml(inputStream);
            writer.write(s);
        } finally {
            if (writer != null) {
                writer.close();
            }
        }


    }

    private static String docxToHtml(InputStream inputStream) throws IOException {
        // 解析doc
        XWPFDocument xwpfDocument = new XWPFDocument(inputStream);
        // 转换为html
        XHTMLConverter xhtmlConverter = (XHTMLConverter) XHTMLConverter.getInstance();
        XHTMLOptions options = XHTMLOptions.getDefault();

        StringWriter stringWriter = new StringWriter();
        xhtmlConverter.convert(xwpfDocument, stringWriter, options);
        return new String(stringWriter.toString().getBytes(StandardCharsets.UTF_8), StandardCharsets.UTF_8);
    }
}

```

## 一些问题

注意依赖版本，升级可能造成错误

## 其他实现方式

- 阿里云的 oss 预览 阿里云本身提供这样的方式，不过收费
- 先把文件转成 pdf 再转成 html
- 把文件转成 pdf 再转成图片

具体实现大家自己来吧
