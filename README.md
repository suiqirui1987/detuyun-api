




<a name="a1"></a>
### 得图云签名认证 

#####签名格式 

  	 Authorization: DetuYun Demo_Access_Key:signature
<table class="table table-bordered table-striped table-condensed">
  <tr>
    <th>字段</th>
    <th>说明</th>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>http header 键值 </td>
  </tr>
  <tr>
    <td>DetuYun</td>
    <td>固定字符串，与 username 之间以空格间隔</td>
  </tr>
  <tr>
    <td>Demo_Access_Key</td>
    <td>分配的 Demo_Access_Key</td>
  </tr>
  <tr>
    <td>signature</td>
    <td>签名内容</td>
  </tr>
</table>
#####签名内容生成方式 

   	md5( URI & DATE & md5(PASSWORD))

<table class="table table-bordered table-striped table-condensed">
  <tr>
        <th>字段</th>
        <th>说明</th>
      </tr>
  <tr>
        <td>&</td>
        <td>字符串连接符：前后不要有空格，上面的计算式中的空格是为了阅读方便特意添加</td>
      </tr>

  <tr>
        <td>URI</td>
        <td>请求路径，必须符合<a href="http://tools.ietf.org/html/rfc1738" target="_blank">http 协议标准</a>：包含中文名称或特殊字符的文件名（或目录），需进行 urlencode 处理</td>
      </tr>
  <tr>
        <td>DATE</td>
        <td>请求日期，格式：Wed, 24 Aug 2011 09:13:05 GMT <a href="http://tools.ietf.org/html/rfc1123" target="_blank">(RFC 1123)</a>。请确保该时间与世界时间一致，否则签名将失败</td>
      </tr>

  <tr>
        <td>PASSWORD</td>
        <td>空间的操作员密码</td>
      </tr>
</table>
#####请求方法 

	> // GET 请求方法。 注：GET 和 HEAD 请求没有 Content-Length header

	> GET /demobucket/ HTTP/1.1
	> Host: s.detuyun.com
	> Authorization: DetuYun Demo_Access_Key:97295347384af91dc2b9d7374dd0821d
	> Date: Thu, 11 Jul 2013 05:34:12 GMT

	> // PUT 请求方法。注：PUT 和 POST 请求需要传递 Content-Length header

	> PUT /demobucket/a.txt HTTP/1.1
	> Host: s.detuyun.com
	> Authorization: DetuYun Demo_Access_Key:97295347384af91dc2b9d7374dd0821d
	> Date: Thu, 11 Jul 2013 05:34:12 GMT
	> Content-Length: 26





#####代码示例(PHP)
    <?php

        	// demobucket为空间名
       		$uri = '/demobucket/';

        	$process = curl_init('http://s.detuyun.com'.$uri);

        	// 取得世界时间。得到的日期格式如：Thu, 11 Jul 2013 05:34:12 GMT
       		$date = gmdate('D, d M Y H:i:s \G\M\T');

        	$sign = md5("{$uri}&{$date}&".md5("Secret Key"));

        	// 设置表头参数：Demo_Access_Key为操作员名称
        	curl_setopt($process, CURLOPT_HTTPHEADER, array(
           		"Expect:", 
            	"Date: ".$date, // header 中传递的时间需要与签名时的时间保持一致
           		"Authorization: DetuYun Demo_Access_Key:".$sign
        	));
       		curl_setopt($process, CURLOPT_HEADER, 1);
        	curl_setopt($process, CURLOPT_TIMEOUT, 60);
       		curl_setopt($process, CURLOPT_RETURNTRANSFER, 1);

        	var_dump(curl_exec($process));
       		var_dump(curl_getinfo($process, CURLINFO_HTTP_CODE));

        	curl_close($process);
		?>
    <hr style="margin-top:40px"/>
    
    
### 标准API接口

- [1、 上传文件](#b1)
- [2、 下载文件](#b2)
- [3、 获取文件信息](#b3)
- [4、 删除文件](#b4)
- [5、 创建目录](#b5)
- [6、 删除目录](#b6)
- [7、 获取目录文件列表](#b7)
- [8、 获取空间使用情况](#b8)

<a name="b"></a> 
####1、 上传文件 

#####上传说明 

-  上传文件的同时可设置其他可选参数，比如文件MD5校验以及<a href="./page3.html">图片处理参数</a>等 
 
-  图片类空间上传图片成功后，将返回图片的基本信息 
 
-  图片类空间无法上传非图片类文件 




#####请求方法 

	> PUT /demobucket/a.txt HTTP/1.1
	> Authorization: DetuYun Demo_Access_Key:WEQRASDFEWERWE33A54A
	> Host: s.detuyun.com
	> Content-Length: 26

  	...文件内容...

#####请求参数 

除<a href="./page4.html">"标准请求头"</a>之外，可根据需要添加以下请求头参数
<table class="table table-bordered table-striped table-condensed">
  <tr>
    <th>请求头</th>
    <th>是否必须</th>
    <th>参数值</th>
    <th>说明</th>
  </tr>
  <tr>
    <td>mkdir</td>
    <td>否</td>
    <td>true / false</td>
    <td>是否要自动创建不存在的父级目录。最多允许创建 10 级目录</td>
  </tr>
  <tr>
    <td>Content-MD5</td>
    <td>否</td>
    <td>文件的 MD5 值</td>
    <td>对上传文件进行 MD5 校验，保证文件的完整性。若得图云服务端计算的文件 MD5 值与用户设置的不一致，则错误返回</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>否</td>
    <td>文件的 Content-Type 值</td>
    <td>待上传的文件扩展名不存在，或扩展名不足以判断文件的
      Content-Type 时，允许用户自己设置文件的 Content-Type 值。
      得图云存储默认使用文件名的扩展名进行自动设置。</td>
  </tr>
</table>
#####返回内容 

成功时： 

	> HTTP/1.1 200 OK

	// 文件类空间上传文件成功后返回内容为空

	// 图片类空间上传图片成功之后返回以下响应头
	> x-detuyun-width: 50
	> x-detuyun-height: 50
	> x-detuyun-frames: 1
	> x-detuyun-type: image/jpeg



失败时： 

	> HTTP/1.1 401
	> x-detuyun-error: Not accept, Not accept file




其他响应头请参阅<a href="./page5.html">"标准响应头"</a>。

 详细的得图云错误代码请参阅<a href="./page6.html">标准API错误代码表</a> 
#####代码示例(PHP)
 	<?php
	       	// 保存文件到 demobucket 空间的根目录下
	        $process = curl_init('http://s.detuyun.com/demobucket/a.txt'); 
	
	        // 上传操作
	        curl_setopt($process, CURLOPT_PUT, 1);
	        curl_setopt($process, CURLOPT_USERPWD, "Demo_Access_Key:Secret Key");
	        curl_setopt($process, CURLOPT_HEADER, 1);
	        curl_setopt($process, CURLOPT_TIMEOUT, 60);
	
	        // 本地待上传的文件
	        $local_file_path = "/tmp/sample.txt";
	        $datas = fopen($local_file_path,'r'); 
	        fseek($datas, 0, SEEK_END);
	        $file_length = ftell($datas);
	        fseek($datas, 0);
	
	        // 设置待上传的内容
	        curl_setopt($process, CURLOPT_INFILE, $datas);
	
	        // 设置待上传的长度
	        curl_setopt($process, CURLOPT_INFILESIZE, $file_length);
	
	        // 设置自动创建父级目录
	        curl_setopt($process, CURLOPT_HTTPHEADER, array("Expect:", "mkdir: true"));
	        curl_setopt($process, CURLOPT_RETURNTRANSFER, 1);
	       
	        var_dump(curl_exec($process));
	        var_dump(curl_getinfo($process, CURLINFO_HTTP_CODE));
	
	        curl_close($process);
	        fclose($datas);
	?>

<a name="b2"></a> 

####2、 下载文件 

#####请求方法 

	> GET /demobucket/a.txt HTTP/1.1
	> Authorization: DetuYun Demo_Access_Key:WEQRASDFEWERWE33A54A
	> Host: s.detuyun.com

#####请求参数 

除<a href="./page4.html">"标准请求头"</a>之外，无需其他请求头参数 



#####返回内容 

成功时： 

	> HTTP/1.1 200 OK

	demodata // 文件内容


失败时：
 
	> HTTP/1.1 404 
	> x-detuyun-error: Not accept,Not found


其他响应头请参阅<a href="./page5.html">"标准响应头"</a>。

 详细的得图云错误代码请参阅<a href="./page6.html">标准API错误代码表</a> 

#####代码示例(PHP)
	 <?php
	        // 下载 demobucket 空间根目录下的 a.txt 文件
	        $process = curl_init('http://s.detuyun.com/demobucket/a.txt');
	
	        curl_setopt($process, CURLOPT_USERPWD, "Demo_Access_Key:Secret Key");
	        curl_setopt($process, CURLOPT_HEADER, 1);
	        curl_setopt($process, CURLOPT_TIMEOUT, 60);
	        curl_setopt($process, CURLOPT_RETURNTRANSFER, 1);
	
	        var_dump(curl_exec($process));
	        var_dump(curl_getinfo($process, CURLINFO_HTTP_CODE));
	
	        curl_close($process);
	?>

<a name="b3"></a> 

####3、 获取文件信息 

#####请求方法 

	> HEAD /demobucket/a.txt HTTP/1.1
	> Authorization: DetuYun Demo_Access_Key:WEQRASDFEWERWE33A54A
	> Host: s.detuyun.com





#####请求参数 

除<a href="./page4.html">"标准请求头"</a>之外，无需其他请求头参数 



#####返回内容 

成功时： 

	> HTTP/1.1 200 OK
	> x-detuyun-file: name	type	size	date        // 文件名 文件返回"file"，文件夹返回"folder" 	文件大小 文件创建时间


失败时： 

	> HTTP/1.1 404 
	> x-detuyun-error: Not accept,Not found


其他响应头请参阅<a href="./page5.html">"标准响应头"</a>。

 详细的得图云错误代码请参阅<a href="./page6.html">标准API错误代码表</a>

#####代码示例(PHP)
 	<?php
	        // 获取 demobucket 空间根目录下的 a.txt 文件的基本信息
	        $process = curl_init('http://s.detuyun.com/demobucket/a.txt');
	
	        curl_setopt($process, CURLOPT_CUSTOMREQUEST, 'HEAD');
	        curl_setopt($process, CURLOPT_USERPWD, "Demo_Access_Key:Secret Key");
	        curl_setopt($process, CURLOPT_HEADER, 1);
	        curl_setopt($process, CURLOPT_TIMEOUT, 60);
	        curl_setopt($process, CURLOPT_RETURNTRANSFER, 1);
	        
	        // 只获取 header
	        curl_setopt($process, CURLOPT_HEADER, 1); 
	        curl_setopt($process, CURLOPT_NOBODY, true);
	
	        var_dump(curl_exec($process));
	        var_dump(curl_getinfo($process, CURLINFO_HTTP_CODE));
	
	        curl_close($process);
	?>

<a name="b4"></a>
####4、 删除文件 

#####请求方法 

	> DELETE /demobucket/a.txt HTTP/1.1
	> Authorization: DetuYun Demo_Access_Key:WEQRASDFEWERWE33A54A
	> Host: s.detuyun.com



#####请求参数 

除<a href="./page4.html">"标准请求头"</a>之外，无需其他请求头参数 



#####返回内容 

成功时： 

	> HTTP/1.1 200 OK


失败时： 

	> HTTP/1.1 404 
	> x-detuyun-error: Not accept,Not found


其他响应头请参阅<a href="./page5.html">"标准响应头"</a>。

 详细的得图云错误代码请参阅<a href="./page6.html">标准API错误代码表</a> 
#####代码示例(PHP)
 	<?php
	        // 删除 demobucket 空间根目录下的 a.txt 文件
	        $process = curl_init('http://s.detuyun.com/demobucket/a.txt');
	
	        // 删除请求
	        curl_setopt($process, CURLOPT_CUSTOMREQUEST, 'DELETE');
	        curl_setopt($process, CURLOPT_USERPWD, "Demo_Access_Key:Secret Key");
	        curl_setopt($process, CURLOPT_HEADER, 1);
	        curl_setopt($process, CURLOPT_TIMEOUT, 60);
	        curl_setopt($process, CURLOPT_RETURNTRANSFER, 1);
	
	        var_dump(curl_exec($process));
	        var_dump(curl_getinfo($process, CURLINFO_HTTP_CODE));
	
	        curl_close($process);
	?>
 <a name="b5"></a> 

####5、 创建目录 

#####请求方法 

	> POST /demobucket/dir/ HTTP/1.1
	>Authorization: DetuYun Demo_Access_Key:97295347384af91dc2b9d7374dd0821d
	> Host: s.detuyun.com
	> Content-Length:0
	> Folder: true





#####请求参数 

除<a href="./page4.html">"标准请求头"</a>之外，还有以下请求头参数
<table class="table table-bordered table-striped table-condensed">
  <tr>
    <th>请求头</th>
    <th>是否必须</th>
    <th>参数值</th>
    <th>说明</th>
  <tr>
    <td>folder</td>
    <td>是</td>
    <td>true</td>
    <td>标识当前请求为“创建目录”</td>
  </tr>
  <tr>
    <td>mkdir</td>
    <td>否</td>
    <td>true / false</td>
    <td>是否要自动创建不存在的父级目录。最多允许创建 10 级目录</td>
  </tr>
</table>
#####返回内容 

成功时： 

	> HTTP/1.1 200 OK


失败时： 

	> HTTP/1.1 404 
	> x-detuyun-error: Not accept,Not found



其他响应头请参阅<a href="./page5.html">"标准响应头"</a>。

 详细的得图云错误代码请参阅<a href="./page6.html">标准API错误代码表 </a> 

#####代码示例(PHP)
	 <?php
		   // 在 demobucket 空间根目录下创建一个 dir 的目录
		   $process = curl_init('http://s.detuyun.com/demobucket/dir/');
		
		   curl_setopt($process, CURLOPT_POST, 1);
		   curl_setopt($process, CURLOPT_POSTFIELDS, '');
		
		   // 设置需要创建目录
		   curl_setopt($process, CURLOPT_HTTPHEADER, array( 'Expect:', 'folder: true'));
		
		   curl_setopt($process, CURLOPT_USERPWD, "Demo_Access_Key:Secret Key");
		   curl_setopt($process, CURLOPT_HEADER, 1);
		   curl_setopt($process, CURLOPT_TIMEOUT, 60);
		   curl_setopt($process, CURLOPT_RETURNTRANSFER, 1);
		
		   var_dump(curl_exec($process));
		   var_dump(curl_getinfo($process, CURLINFO_HTTP_CODE));
		
		   curl_close($process);
	?>
 <a name="b6"></a> 
####6、 删除目录 

#####请求方法 

	> DELETE /demobucket/dir/ HTTP/1.1
	>Authorization: DetuYun Demo_Access_Key:97295347384af91dc2b9d7374dd0821d
	> Host: s.detuyun.com   



#####请求参数 

除<a href="./page4.html">"标准请求头"</a>之外，无需其他请求头参数 



#####返回内容 

成功时： 

	> HTTP/1.1 200 OK


失败时： 

	> HTTP/1.1 404 
	> x-detuyun-error: Not accept,Not found



其他响应头请参阅<a href="./page5.html">"标准响应头"</a>。

 详细的得图云错误代码请参阅<a href="./page6.html">标准API错误代码表</a> 

#####代码示例(PHP)

	<?php
		 // 删除 demobucket 空间根目录下的 dir 目录
		 $process = curl_init('http://s.detuyun.com/demobucket/dir/');
		
		 // 删除目录请求
		 curl_setopt($process, CURLOPT_CUSTOMREQUEST, 'DELETE');
		 curl_setopt($process, CURLOPT_USERPWD, "Demo_Access_Key:Secret Key");
		 curl_setopt($process, CURLOPT_HEADER, 1);
		 curl_setopt($process, CURLOPT_TIMEOUT, 60);
		 curl_setopt($process, CURLOPT_RETURNTRANSFER, 1);
		
		 var_dump(curl_exec($process));
		 var_dump(curl_getinfo($process, CURLINFO_HTTP_CODE));
		
		 curl_close($process);
	?>
 <a name="b7"></a> 

####7、 获取目录文件列表 

#####请求方法
 
	> GET /demobucket/ HTTP/1.1
	>Authorization: DetuYun Demo_Access_Key:97295347384af91dc2b9d7374dd0821d
	> Host: s.detuyun.com





#####请求参数 

除<a href="./page4.html">"标准请求头"</a>之外，无需其他请求头参数 



#####返回内容 

成功时： 

	> HTTP/1.1 200 OK
	
	// 文件之间以"\n"分隔，属性之间以"\t"分隔
	demo.jpg\tN\t11383\t1306130450\timage.jpg\ndemo\tF\t14738423\t1306130450\t  // body 内容


解析后(目录大小表示当前目录下的文件大小，不含子目录)：
    <table class="table table-bordered table-striped table-condensed">
  <tr>
        <th>文件/目录名</th>
        <th>类型</th>
        <th>大小</th>
        <th>最后修改时间</th>
		<th>定义文件类型</th>
      <tr>
        <td>demo.jpg</td>
        <td>N (文件)</td>
        <td>11383</td>
        <td>1306130450</td>
		<td>image/jpeg</td>
      </tr>
  <tr>
        <td>demo</td>
        <td>F (目录)</td>
        <td>14738423</td>
        <td>1306130450</td>
		<td>|</td>
      </tr>
</table>
失败时： 

	> HTTP/1.1 404 
	> x-detuyun-error: Not accept,Not found


其他响应头请参阅<a href="./page5.html">"标准响应头"</a>。

 详细的得图云错误代码请参阅
<a href="./page6.html">标准API错误代码表</a> 

#####代码示例(PHP)
 	<?php
	        // 获取 demobucket 空间根目录下的文件列表
	        $process = curl_init('http://s.detuyun.com/demobucket/');
	
	        curl_setopt($process, CURLOPT_USERPWD, "Demo_Access_Key:Secret Key");
	        curl_setopt($process, CURLOPT_HEADER, 1);
	        curl_setopt($process, CURLOPT_TIMEOUT, 60);
	        curl_setopt($process, CURLOPT_RETURNTRANSFER, 1);
	
	        var_dump(curl_exec($process));
	        var_dump(curl_getinfo($process, CURLINFO_HTTP_CODE));
	
	        curl_close($process);
	?>
 <a name="b8"></a> 
####8、 获取空间使用情况 

#####请求方法 

	> GET /demobucket/?usage HTTP/1.1
	>Authorization: DetuYun Demo_Access_Key:97295347384af91dc2b9d7374dd0821d
	> Host: s.detuyun.com


#####请求参数 

除<a href="./page4.html">"标准请求头"</a>之外，无需其他请求头参数 



#####返回内容 

成功时：
 
	> HTTP/1.1 200 OK

	> x-detuyun-used: 1639920 // 空间的使用量，单位为 kb


失败时： 
	> HTTP/1.1 404 
	> x-detuyun-error: Not accept,Not found



其他响应头请参阅<a href="./page5.html">"标准响应头"</a>。

 详细的得图云错误代码请参阅<a href="./page6.html">标准API错误代码表</a> 

#####代码示例(PHP)
 	<?php
	        // 获取 demobucket 空间的使用情况
	        $process = curl_init('http://s.detuyun.com/demobucket/?usage');
	
	        curl_setopt($process, CURLOPT_USERPWD, "Demo_Access_Key:Secret Key");
	        curl_setopt($process, CURLOPT_HEADER, 1);
	        curl_setopt($process, CURLOPT_TIMEOUT, 60);
	        curl_setopt($process, CURLOPT_RETURNTRANSFER, 1);
	
	        var_dump(curl_exec($process));
	        var_dump(curl_getinfo($process, CURLINFO_HTTP_CODE));
	
	        curl_close($process);
	?>




<a name="c"></a> 

###图片处理接口 

上传图片时，允许使用以下图片处理接口直接对上传的图片进行处理。需要注意三点：
 
1. 图片处理接口只是在<a href="./page2.html#b1">上传文件接口</a>的基础上，添加了图片处理参数而已； 
2. 上传图片并传递图片处理参数时，最终将只保存处理后的图片，原图不保存 

<a name="c1"></a>

##### 图片缩略 

#####请求方法
 
	> PUT /demobucket/pic.jpg HTTP/1.1
	>Authorization: DetuYun Demo_Access_Key:97295347384af91dc2b9d7374dd0821d
	> Host: s.detuyun.com
	> Content-Length:21001
	> x-gmkerl-type:fix_width
	> x-gmkerl-value:200
	> x-gmkerl-unsharp:true
	
	  ...图片文件内容...





#####请求参数 

其他上传参数请参阅<a href="./page4.html">"标准请求头"</a>和<a href="./page2.html#b1">上传文件接口</a>，以下只对图片缩略相关的参数进行说明
<table class="table table-bordered table-striped table-condensed">
  <tr>
    <th>请求头</th>
    <th>参数值</th>
    <th>说明</th>
  </tr>
  <tr>
    <td>x-gmkerl-type</td>
    <td><p>1. fix_width: 限定宽度,高度自适应</p>
      <p>2. fix_height: 限定高度,宽度自适应</p>
      <p>3. fix_width_or_height: 限定宽度和高度，宽高不足时不缩放</p>
      <p>4. fix_both: 固定宽度和高度，宽高不足时强行缩放</p>
      </td>
    <td><p>缩略类型</p>
      <p>必须搭配 x-gmkerl-value 使用</p></td>
  </tr>
  <tr>
    <td>x-gmkerl-value</td>
    <td><p>fix_width_or_height/fix_both：widthxheight(如 200x150)</p>
      <p>其他缩略类型：数字(如 150)</p></td>
    <td><p>缩略类型对应的参数值，单位为像素</p>
      <p>必须搭配 x-gmkerl-type 使用</p></td>
  </tr>
  <tr>
    <td>x-gmkerl-quality </td>
    <td>1~100</td>
    <td>图片压缩质量，默认 95</td>
  </tr>
 
</table>
#####返回内容 

成功时：
 
	> HTTP/1.1 200 OK
	
	// 文件类空间上传文件成功后返回内容为空
	
	// 图片类空间上传图片成功之后返回以下响应头
	> x-detuyun-width: 250       // 图片宽度
	> x-detuyun-height: 190      // 图片高度
	> x-detuyun-frames: 1        // 图片帧数
	> x-detuyun-file-type: image/jpeg  // 图片类型


失败时： 

	> HTTP/1.1 401
	> x-detuyun-error: Not accept,Not found


其他响应头请参阅<a href="./page5.html">"标准响应头"</a>。

 详细的得图云错误代码请参阅<a href="./page6.html">标准API错误代码表</a> 
#####代码示例(PHP)
 <?php
	        // 保存图片到 demobucket 空间的根目录下，且保存名称为 pic.jpg
	        $process = curl_init('http://s.detuyun.com/demobucket/pic.jpg'); 
	
	        // 上传操作
	        curl_setopt($process, CURLOPT_PUT, 1);
	        curl_setopt($process, CURLOPT_USERPWD, "Demo_Access_Key:Secret Key");
	        curl_setopt($process, CURLOPT_HEADER, 1);
	        curl_setopt($process, CURLOPT_TIMEOUT, 60);
	
	        // 本地待上传的图片文件
	        $local_file_path = "/tmp/sample.jpg";
	        $datas = fopen($local_file_path,'r'); 
	        fseek($datas, 0, SEEK_END);
	        $file_length = ftell($datas);
	        fseek($datas, 0);
	
	        // 设置待上传图片的内容
	        curl_setopt($process, CURLOPT_INFILE, $datas);
	
	        // 设置待上传图片的长度
	        curl_setopt($process, CURLOPT_INFILESIZE, $file_length);
	
	        // 设置缩略参数：限定上传图片最大宽度为 250px，高度自适应，并锐化处理
	        curl_setopt($process, CURLOPT_HTTPHEADER, array(
	                        'x-gmkerl-type: fix_width',
	                        'x-gmkerl-value: 250',
	                        'x-gmkerl-unsharp: true',
	                ));
	        
	        var_dump(curl_exec($process));
	        var_dump(curl_getinfo($process, CURLINFO_HTTP_CODE));
	
	        curl_close($process);
	        fclose($datas);
	?>
#####示例效果 

原图：670x500

 ![](http://www.detuyun.com/docs/document/api/hapi/scene1.jpg)


生成的缩略图：250x190

 ![](http://www.detuyun.com/docs/document/api/hapi/scene2.jpg) 




<a name="d"></a> 
###标准请求头 

<a href="./page1.html">"得图云签名认证"</a>
方式发起任何 HTTP REST API 请求，都必须包含以下公共请求头。


<table class="table table-bordered table-striped table-condensed">
  <tr>
    <th>请求头</th>
    <th>说明 </th>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>授权：用于验证请求合法性的认证信息，比如 `Authorization: DetuYun UserName:Signature`。
      
      详细规范请参阅 <a href="http://tools.ietf.org/html/rfc2616#section-14.8" target="_blank">RFC 2616</a></td>
  </tr>
  <tr>
    <td>Host</td>
    <td><p>得图云 API 的接入地址，现提供：</p>
      <p>s.detuyun.com</p>
    </td>
  </tr>
  <tr>
    <td>Date</td>
    <td><a href="http://zh.wikipedia.org/wiki/格林尼治平时" target="_blank">标准的格林尼治标准时间</a>，且必须为`GMT`格式。
      比如 `Date: Wed, 24 Aug 2011 07:33:47 GMT`</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>请求内容长度，但不包括Headers部分。
      
      若发起的是 PUT、POST 等上传请求时，则必须设置该参数。详细规范请参阅 <a href="http://tools.ietf.org/html/rfc2616#section-14.8" target="_blank">RFC 2616</a></td>
  </tr>
</table>





<a name="e"></a> 
###标准响应头 

发起任何 HTTP REST API 请求后，返回的 Response 都包含如下公共响应头。
<table class="table table-bordered table-striped table-condensed">
  <tr>
    <th>响应头</th>
    <th>说明 </th>
  </tr>
  <tr>
    <td>Connection</td>
    <td>标明客户端和服务器端之间的链接状态。有效值包括 keep-alive / close</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>响应返回内容的类型。详细规范请参阅 <a href="http://tools.ietf.org/html/rfc2616#section-14.8"  target="_blank">RFC 2616</a></td>
  </tr>
</table>



###标准API错误代码表
<table class="table table-bordered table-striped table-condensed">
  <tr>
    <th>HTTP状态码 </th>
    <th>返回代码</th>
    <th>含义</th>
  </tr>
  <tr>
    <td>200</td>
    <td>OK</td>
    <td>操作成功</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Bad Request</td>
    <td>错误请求(如 URL 缺少空间名)</td>
  </tr>

  <tr>
    <td>400</td>
    <td>Not accept,The number of  Bucket is too large</td>
    <td>空间数量过大</td>
  </tr>


  <tr>
    <td>400</td>
    <td>Not accept, Bucket has already existed</td>
    <td>空间已存在</td>
  </tr>


  <tr>
    <td>400</td>
    <td>Not accept, Bucket does not exist</td>
    <td>空间不存在</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Sign error</td>
    <td>签名错误</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Need Date Header</td>
    <td>发起的请求缺少 Date 头信息</td>
  </tr>

  <tr>
    <td>401</td>
    <td>Not accept,Need Authorization header</td>
    <td>发起的请求缺少Authorization头信息</td>
  </tr>

  <tr>
    <td>401</td>
    <td>Not accept,Authorization format error </td>
    <td>授权信息格式错误</td>
  </tr>

  <tr>
    <td>401</td>
    <td>Not accept,Secret key does not exist</td>
    <td>密钥不存在</td>
  </tr>

  <tr>
    <td>401</td>
    <td>Not accept,Account does not exist</td>
    <td>账号不存在</td>
  </tr>


  <tr>
    <td>401</td>
    <td>Not accept,Account is closed</td>
    <td>账号已关闭</td>
  </tr>


  <tr>
    <td>401</td>
    <td>Not accept,Signature error</td>
    <td>签名错误</td>
  </tr>

  <tr>
    <td>403</td>
    <td>Not accept file</td>
    <td>不可接受的类型。</td>
  </tr>



  <tr>
    <td>403</td>
    <td>Not accept, Bucket is not empty</td>
    <td>空间不为空</td>
  </tr>


  <tr>
    <td>403</td>
    <td>Not accept,Document folder not exists or Document folder is not empty</td>
    <td>文件夹不存在或文件夹不为空</td>
  </tr>


  <tr>
    <td>403</td>
    <td>Not accept,File does not exist</td>
    <td>文件不存在</td>
  </tr>

  <tr>
    <td>404</td>
    <td>Not Found</td>
    <td>获取文件或目录不存在；上传文件或目录时上级目录不存在</td>
  </tr>
  <tr>
    <td>503</td>
    <td>System Error</td>
    <td>系统错误</td>
  </tr>







  <tr>
    <td>503</td>
    <td>System Error ... Please retry</td>
    <td>系统错误...请重试 </td>
  </tr>



</table>
