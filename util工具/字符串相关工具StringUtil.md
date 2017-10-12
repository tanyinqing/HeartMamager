
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 字符串相关工具

```
package com.yikang.heartmark.util;

import java.util.zip.CRC32;

import android.annotation.SuppressLint;

public class StringUtil {

	@SuppressLint("DefaultLocale")
	public static String srcToCrc(String srcStr) {
		// 方法1，查表法
		String crcString = "";
		// crcString = getCRC32(srcStr);
		// 方法2，系统参数法
		CRC32 crc32 = new CRC32();
		crc32.update(srcStr.getBytes());
		crcString = Long.toHexString(crc32.getValue());

		// 转大写
		crcString = crcString.toUpperCase();
		// 取最后2位
		return srcStr + crcString.substring(crcString.length() - 2);
	}

}
```
