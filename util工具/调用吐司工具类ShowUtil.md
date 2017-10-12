
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 调用吐司工具类

```
package com.yikang.heartmark.util;

import com.example.heartmark.R;
import com.yikang.heartmark.activity.YongYaoAddActivity;

import android.content.Context;
import android.widget.Toast;

//  ShowUtil.showToast(YongYaoAddActivity.this, R.string.no_data);   测试语句
public class ShowUtil {
	// 显示toast
	public static void showToast(Context context, int value) {
		MyToast.show(context, value, Toast.LENGTH_LONG);
	}
	public static void showToast(Context context, String value) {
		MyToast.show(context, value, Toast.LENGTH_LONG);
	}
}
```
