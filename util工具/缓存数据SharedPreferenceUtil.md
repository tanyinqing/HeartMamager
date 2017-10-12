
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 数据缓存

```
package com.yikang.heartmark.util;

import android.content.Context;
import android.content.SharedPreferences;

public class SharedPreferenceUtil {
	public static final String SHARED_PREFERENCE_NAME = "sharedPreference";
	
	//第一次使用程序标志
	public static void setFirstUse(Context context, boolean firstUse){
		SharedPreferences settings = context.getSharedPreferences(
				SHARED_PREFERENCE_NAME, Context.MODE_PRIVATE);
		SharedPreferences.Editor editor = settings.edit();
		editor.putBoolean("firstUse", firstUse);
		editor.commit();
	}
	
	//是否是第一次使用
	public static boolean getFirstUse(Context context) {
		SharedPreferences settings = context.getSharedPreferences(
				SHARED_PREFERENCE_NAME, Context.MODE_PRIVATE);
		return settings.getBoolean("firstUse", false);
	}
	
	
	//String -------
	public static void setString(Context context, String name, String value){
		SharedPreferences settings = context.getSharedPreferences(SHARED_PREFERENCE_NAME, Context.MODE_PRIVATE);
		SharedPreferences.Editor editor = settings.edit();
		editor.putString(name, value);
		editor.commit();
	}
	
	public static String getString(Context context,String name){
		SharedPreferences settings = context.getSharedPreferences(SHARED_PREFERENCE_NAME, Context.MODE_PRIVATE);
		return settings.getString(name, "");
	}
	
	//Int ------
	public static void setInt(Context context, String name, int value){
		SharedPreferences settings = context.getSharedPreferences(SHARED_PREFERENCE_NAME, Context.MODE_PRIVATE);
		SharedPreferences.Editor editor = settings.edit();
		editor.putInt(name, value);
		editor.commit();
	}
	
	public static int getInt(Context context,String name){
		SharedPreferences settings = context.getSharedPreferences(SHARED_PREFERENCE_NAME, Context.MODE_PRIVATE);
		return settings.getInt(name, 0);
	}
	
	//boolean ------
	public static void setBoolean(Context context, String name, Boolean value){
		SharedPreferences settings = context.getSharedPreferences(SHARED_PREFERENCE_NAME, Context.MODE_PRIVATE);
		SharedPreferences.Editor editor = settings.edit();
		editor.putBoolean(name, value);
		editor.commit();
	}
	
	public static boolean getBoolean(Context context,String name){
		SharedPreferences settings = context.getSharedPreferences(SHARED_PREFERENCE_NAME, Context.MODE_PRIVATE);
		return settings.getBoolean(name, false);
	}
	
	public static boolean getBooleanDefaultTrue(Context context,String name){
		SharedPreferences settings = context.getSharedPreferences(SHARED_PREFERENCE_NAME, Context.MODE_PRIVATE);
		return settings.getBoolean(name, true);
	}
}
```
