
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 吐司

```
package com.yikang.heartmark.util;

import android.content.Context;
import android.os.Handler;
import android.view.Gravity;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.TextView;
import android.widget.Toast;

import com.example.heartmark.R;

public class MyToast {
	// ---------------------------------------自定义多次点击只显示一次的Toast------------------------------//

	public static final int LENGTH_SHORT = android.widget.Toast.LENGTH_SHORT;
	public static final int LENGTH_LONG = android.widget.Toast.LENGTH_LONG;

	//这是系统的Toast
	private static android.widget.Toast toast;
	private static View toastRoot;
	private static TextView tv;
	// private static String oldMsg;
	private static Handler handler = new Handler();

	// 多线程任务
	private static Runnable run = new Runnable() {
		public void run() {
			toast.cancel();
		}
	};
	private static void toast(Context ctx, CharSequence msg, int duration) {

		handler.removeCallbacks(run);
		// handler的duration不能直接对应Toast的常量时长，在此针对Toast的常量相应定义时长
		switch (duration) {
		case LENGTH_SHORT:// Toast.LENGTH_SHORT值为0，对应的持续时间大概为1s
			duration = 1000;
			break;
		case LENGTH_LONG:// Toast.LENGTH_LONG值为1，对应的持续时间大概为3s
			duration = 3000;
			break;
		default:
			break;
		}
		if (null != toast) {
			// toast.setText(msg);//使用系统自带Toast
			/**
			 * 为自定义Toast设置显示内容
			 */
			tv.setText(msg);
		} else {
			/**
			 * 初始化自定义toast
			 */
			toast = new Toast(ctx);
			toastRoot = LayoutInflater.from(ctx).inflate(R.layout.toast_layout,
					null);
			tv = (TextView) toastRoot.findViewById(R.id.tv_toast_textShow);
			tv.setText(msg);
			toast.setDuration(duration);
			toast.setGravity(Gravity.CENTER_HORIZONTAL | Gravity.BOTTOM, 0, 180);
			toast.setView(toastRoot);
		}
		handler.postDelayed(run, duration);
		toast.show();
	}

	/**
	 * 弹出Toast
	 * 
	 * @param ctx
	 *            弹出Toast的上下文
	 * @param msg
	 *            弹出Toast的内容
	 * @param duration
	 *            弹出Toast的持续时间
	 */
	public static void show(Context ctx, CharSequence msg, int duration)
			throws NullPointerException {
		if (null == ctx) {
			throw new NullPointerException("The ctx is null!");
		}
		if (0 > duration) {
			duration = LENGTH_SHORT;
		}
		toast(ctx, msg, duration);
	}

	/**
	 * 弹出Toast
	 * 
	 * @param ctx
	 *            弹出Toast的上下文
	 * @param msg
	 *            弹出Toast的内容的资源ID
	 * @param duration
	 *            弹出Toast的持续时间
	 */
	public static void show(Context ctx, int resId, int duration)
			throws NullPointerException {
		if (null == ctx) {
			throw new NullPointerException("The ctx is null!");
		}
		if (0 > duration) {
			duration = LENGTH_SHORT;
		}
		toast(ctx, ctx.getResources().getString(resId), duration);
	}
}

```
