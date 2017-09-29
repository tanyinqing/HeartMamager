
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 在新的进程中去启动一个广播

```
  /*这是一个广播接受器，去启动一个闹钟的功能   启动闹钟的的广播接收器   */
public class RemindReceiver extends BroadcastReceiver {

	@Override
	public void onReceive(Context context, Intent intent) {
		Intent intentSound = new Intent(context, RemindSoundActivity.class);
		
		Alarm alarm = (Alarm) intent.getSerializableExtra("alarm");
		
		Bundle bundle = new Bundle();
		bundle.putSerializable("remindAlarm", alarm);
		intentSound.putExtras(bundle);
		intentSound.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK); //在新的进程中 打开意图
		
		context.startActivity(intentSound);
		}
		}
```


