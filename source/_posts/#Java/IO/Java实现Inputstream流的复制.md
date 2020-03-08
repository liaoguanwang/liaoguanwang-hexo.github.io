---
title: Java实现Inputstream流的复制
date: 2019-09-05 23:52:01
tags:
- IO
categories: 
- Java
keywors: 
description: 
top_img: 
cover: /img/java3.jpg
---

获取到一个inputstream后，可能要多次利用它进行read的操作。由于流读过一次就不能再读了，而InputStream对象本身不能复制，而且它也没有实现Cloneable接口，所以得想点办法。

实现思路：

1、先把InputStream转化成ByteArrayOutputStream

2、后面要使用InputStream对象时，再从ByteArrayOutputStream转化回来
```java
package com.test;
 
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
 
public class StreamOperateUtil {
	public static void main(String[] args) throws FileNotFoundException {
		 
		InputStream input =  new FileInputStream("c:\\test.txt"); 
		//InputStream input =  httpconn.getInputStream(); //这里可以写你获取到的流
		
		ByteArrayOutputStream baos = cloneInputStream(input);
		
		// 打开两个新的输入流  
		InputStream stream1 = new ByteArrayInputStream(baos.toByteArray());  
		InputStream stream2 = new ByteArrayInputStream(baos.toByteArray());
		
	}
 
	private static ByteArrayOutputStream cloneInputStream(InputStream input) {
		try {
			ByteArrayOutputStream baos = new ByteArrayOutputStream();
			byte[] buffer = new byte[1024];
			int len;
			while ((len = input.read(buffer)) > -1) {
				baos.write(buffer, 0, len);
			}
			baos.flush();
			return baos;
		} catch (IOException e) {
			e.printStackTrace();
			return null;
		}
	}
 
}
```

这种适用于一些不是很大的流，因为缓存流是会消耗内存的。

备注：[关于InputStream为什么不能被重复读取？](http://blog.csdn.net/dreamtdp/article/details/26733563)

转自: [Java实现inputstream流的复制](https://blog.csdn.net/qq_25646191/article/details/78856639)