
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 判断用户是否登录1

```
// 是否登录
	public static boolean isLogin(Context context) {
			return SharedPreferenceUtil.getBoolean(context, ConstantUtil.LOGIN);
		
	}
```
> 判断当前设备网络是否已经连接 

```
// 判断当前设备网络是否已经连接 
	public static boolean isConnect(Context context) {
		try {
			ConnectivityManager connectivity = (ConnectivityManager) context
					.getSystemService(Context.CONNECTIVITY_SERVICE);
			if (connectivity != null) {
				// 获取网络连接管理的对象
				NetworkInfo info = connectivity.getActiveNetworkInfo();
				if (info != null && info.isConnected()) {
					if (info.getState() == NetworkInfo.State.CONNECTED) {
						return true;
					}
				}
			}
		} catch (Exception e) {
			return false;
		}
		return false;
	}
```



> 防止连续点击

```
private static long lastClickTime;
	// 防止连续点击
	public static boolean isFastDoubleClick(int ms) {
		long time = System.currentTimeMillis();
		if (time - lastClickTime < ms) {
			return true;
		}
		lastClickTime = time;
		return false;
	}
```
