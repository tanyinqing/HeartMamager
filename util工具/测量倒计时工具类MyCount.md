
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 测量倒计时工具类MyCount

```
package com.yikang.heartmark.util;

import android.app.Notification;
import android.app.NotificationManager;
import android.app.PendingIntent;
import android.content.Context;
import android.content.Intent;
import android.os.CountDownTimer;

import com.example.heartmark.R;
import com.yikang.heartmark.activity.MainCeLingActivity;

/**
 * 测量倒计时工具类MyCount
 * */
public class MyCount extends CountDownTimer {

	private static MyCount instance;
	private static Context context;
	private static NotificationManager notifi; // 通知栏

	public static MyCount getInstance(Context contxt,long millisInFuture,
			long countDownInterval) {
		context = contxt;
		notifi = (NotificationManager) context.getSystemService(context.NOTIFICATION_SERVICE);
		if (instance == null) {
			instance = new MyCount(millisInFuture, countDownInterval);
		}
		return instance;
	}

	public static void stop() {
		if (instance != null) {
			instance.cancel();
			instance = null;
		}
	}

	public MyCount(long millisInFuture, long countDownInterval) {
		super(millisInFuture, countDownInterval);
	}

	@Override
	public void onTick(long millisUntilFinished) {
		if (MainCeLingActivity.instance != null) {
			MainCeLingActivity.instance.buttonNext.setText("反应倒计时("
					+ millisUntilFinished / 1000 + ")");
		}
	}

	@Override
	public void onFinish() {
		showNotification();
		if (MainCeLingActivity.instance != null) {
			 MainCeLingActivity.instance.buttonNext.setText(MainCeLingActivity.instance.buttonGet);
			 MainCeLingActivity.instance.buttonNext.setClickable(true);
			 
			
			 String sendValue = "GetDate\r\n";
			 byte[] sendByte = sendValue.getBytes();
			 MainCeLingActivity.instance.sendMessages(sendByte);
			 MainCeLingActivity.instance.buttonNext.setText( MainCeLingActivity.instance.buttonGet);
		}
	}
	
	@SuppressWarnings("deprecation")
	public void showNotification() {
		int icon = R.drawable.heart_mark_icon;
		long when = System.currentTimeMillis();

		String appName = context.getResources().getString(R.string.app_name);
		String title = context.getResources().getString(R.string.app_name);
		String info = context.getResources().getString(R.string.celing_notif_info);

		Notification notification = new Notification(icon, title, when);
		notification.flags = Notification.FLAG_ONGOING_EVENT;
		notification.defaults |= Notification.DEFAULT_SOUND; // 添加声音
		notification.flags |= Notification.FLAG_AUTO_CANCEL; // 点击自动清除
		PendingIntent contentIntent = PendingIntent.getActivity(context, 0,new Intent(), 0);

		notification.setLatestEventInfo(context, appName, info, contentIntent);
		notifi.notify(R.string.app_name, notification);
	}

}

```
