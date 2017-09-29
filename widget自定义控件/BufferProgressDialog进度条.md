
<img src="/image/page/主页.jpg" title="主页" width="300">

进度条的定义 父类中引用
```

public class BufferProgressDialog {
	private Dialog progress;

	@SuppressLint("InflateParams")
	public BufferProgressDialog(final Context context) {

		final Dialog dialog = new Dialog(context, R.style.BufferDialog);
		LayoutInflater inflater = LayoutInflater.from(context);
		View v = inflater.inflate(R.layout.buffer_dialog, null);

		dialog.setContentView(v);
		dialog.setCancelable(false);
		dialog.setCanceledOnTouchOutside(false);
		dialog.show();

		progress = dialog;
	}

	public void destroyProgressDialog() {
		progress.cancel();
		
		//取消网络请求
	}

	public Dialog get_progress() {
		return progress;
	}
}
```	
样式

```
<style name="BufferDialog" parent="@android:style/Theme.Dialog">
        <item name="android:windowFrame">@null</item>
        <item name="android:windowIsFloating">true</item>
        <item name="android:windowIsTranslucent">true</item>
        <item name="android:windowNoTitle">true</item>
        <item name="android:background">@color/transparent</item>
        <item name="android:windowBackground">@color/transparent</item>
    </style>
```
xml布局

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical" >

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:background="@drawable/buffer_bg"
        android:gravity="center"
        tools:ignore="UselessParent" >

        <ProgressBar
            android:id="@+id/BufferProgressDialog"
            style="?android:attr/progressBarStyleLarge"
            android:layout_width="60dp"
            android:layout_height="60dp"
            />
    </LinearLayout>
 

</LinearLayout>
```





