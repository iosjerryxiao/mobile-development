# Integrate JSPatch Practice

In fact, if you read [<u>jspatch wiki</u>](https://github.com/bang590/JSPatch/wiki) carefully, the usage of jspatch is plain and simple. But when you deploy this feature to an enterprise APP, you'd better make sure it's security, because if someone hijacked the server, and post something evil that makes your APP crash. You will know it's not funny.

The author of [<u>JSPatch</u>](https://github.com/bang590/JSPatch/) has discussed about the security issue in this article [<u>JSPatch 部署安全策略</u>](http://blog.cnbang.net/tech/2879/).

The author also has built a [<u>platform</u>](http://jspatch.com/) as a solution for deploy on an APP.

If you don't want to use the platform, or what's more important, if you want to handle the every detail of the rule of your hot patch script. You can build a platform yourself. I'll introduce you how I figure that out.

## 1. Server Side Version Control of Script File
Let's imagine that you have several APPs, and every APP has a lot of versions. YES, I'm in such a situation. so my patch server like this: 
<img src="https://www.kidneyband.com/wp-content/uploads/2016/03/fileManagment-300x131.png" alt="fileManagment" width="600" height="262" class="aligncenter size-medium wp-image-52" />
		
So your request url like this http://host/appName/appVersion/fix.zip, you may ask why we request a zip file instead of just a patch file? See part 2.
		

		
## 2. Signature of Script File
As I mentioned previous, we must find a way to sign and verify the script file. The [<u>JSPatch Platform</u>](http://jspatch.com/) use [<u>RSA</u>](https://en.wikipedia.org/wiki/RSA) to sign this script file, this solution is really safe, the flow chart shows below.
<img src="https://www.kidneyband.com/wp-content/uploads/2016/03/process-300x178.png" alt="process" width="600" height="356" class="aligncenter size-medium wp-image-78" />
		 
I've searched and read a lot of articles about how to encrypt and decrypt by RSA in iOS, most of them are bullshit. But [<u>this article</u>](http://www.ideawu.com/blog/post/132.html)  is an exception. the author of this article also open source his [<u>code</u>](https://github.com/ideawu/Objective-C-RSA), the code is really clear and simple. Unfortunately the author did not provide php code of how to pack script file to zip. so I provide it, php pack code below, all you need is write a script.js file and put it in same level as php packer file,  then run the packer like:
>php packer.php

And, one more thing, please format the public key and private key (append "\n" to each line of the key string).
```
// run this script like this: php packer.php (must have script.js file in the same level folder)  
$priviteKey = "-----BEGIN RSA PRIVATE KEY-----\nMIICXwIBAAKBgQDn3AbTfiFrkgKD+KOnFj19DhuTKyt/RDn7nW5vhtGCJ8UFIYDI\n3OQ10eLS3scgcbO27f3G0GRZaIDD8AHFs6o2OY7/jQwAh4N0juoeYkIte3jW\nZXELexBwF2xrU+faMQHxvklH5KITzcrpjrQB7UsZwiWtU4WN+VY4lBk9rQIDAQAB\nAoGBAIOeSJW4bRpWeW0XMTeBjYg3APpLMFb/kPa/yNgwrS/4iYQ/H49mrXnL2rof\n0uQdB4rZkRvHr6WgmMaUZzxTO309f0nYpTqxBQsrQglTUOOfOxWgHxZIlQI5bLox\n0NZZThtVet4mCtqM16jzZxyqo1sMboQfvFDX0zJ96CtGAjzBAkEA/qQlYhRrBHt5\nCs1sF7aufHteKDcdxYvs8YBWK75q1szZASOh8RkZrbIRn5FdSXOgmOOcYzUIBIwe\nV4o85zw78QJBAOkYwnHu1c51WOuUOM2rDYYKfLOfXv4od3InS/26kNc+4O5RKTLk\nTgH3AhHt071x1JV0dPecAqMDhYw9jjVciX0CQQDcYBbyURVlJa7VyH5572IG3YXZ\nbAnaA4UmZJku9nbkOxi7QalPTRfKGUfjPxzrCqS8bI/V0/mCZfmEbWDUBm8hAkEA\nh5tm6/+FlXIBhDQcA9LisDuDpsAt9HC4ZMnl1LvoLcEf/KVs8XWOyGfaZXHWAF2r\nzjMNJUw24H1ZAWiCRhsKxQJBAKpqCRosL5/6cnswhDHl9Iy0yz3NcyTvRuIn7Vzz\nfApZyj5DuAlYK2kRfF4kNWLEx1Rz2XU4D/vCbMpSp/8HJFo=\n-----END RSA PRIVATE KEY-----";
// $str = md5_file($file1);
$str = md5_file('script.js');
		
$encrypted = '';
for ($i=0; $i < strlen($str); $i+=117) 
{ 
		$src = substr($str, $i, 117);
		$ret = openssl_private_encrypt($src, $out, $priviteKey);
		$encrypted .= $out;
}
$resultStr = base64_encode($encrypted);
$resultFile = fopen("verify.txt", "wr");
fwrite($resultFile, $resultStr);
fclose($resultFile);
		
$files = array('verify.txt', 'script.js');
$zipName = 'fix.zip';
$zipTool = new ZipArchive;
$zipTool->open($zipName, ZipArchive::CREATE);
foreach ($files as $file)
{
			$zipTool->addFile($file);
}
$zipTool->close();
```

## 3. Local Version Control of Script File
So we have the zip, how we handle this file?
		
This zip file contains 2 files, one is  a script file , the other one is a plain text file used for verify the script file. if the script file is valid, the APP run the scrip. vice versa. 

You can verify the script by follow steps:
<ul>
<li>1. unzip the fix.zip file to temp folder.</li>
<li>2. get md5 value of the script.js.</li>
<li>3. decrypt the verify.txt to a string.  you can find the decrypt code <a href="https://github.com/ideawu/Objective-C-RSA"><u>here</u></a>.</li>
<li>4. check two string is equal, if yes, the script file is valid, you create a specific folder within library folder,  you can save it with name *version*.js to the specific folder. cause it can aviod your APP run old version script file. And bear in mind that remove old version script file.</li>
</ul>

This article heavily inspired by the [<u>this file</u>](https://github.com/bang590/JSPatch/blob/master/Loader/JPLoader.m), you can learn more from it.