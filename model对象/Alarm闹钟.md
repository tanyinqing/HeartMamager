
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 对象的常量

```
@SuppressWarnings("serial")
public class Alarm implements Serializable{
	public int id;
	public String type; // 区分测量，用药，护理等;不可为空
	public int alarmId; // 闹钟唯一标志    设置闹钟的时间的最后八位
	public long alarmTime; // 闹钟响起的时间的毫秒值
	public String time; // 字符串形式的时间,用于回显
	public String week; // 用于显示重复时间，列：周一||周二...
	public long repeatTime; // 循环周期 ---- 不存数据库 ----
	public int yaoId; // 药的id
	public String yaoName; // 药的名字
	public String yaoType; //药的类型
	public long setTime; // 设置闹钟的时间，用于区分时间相同的俩个闹钟
	public boolean isRepeat; // 是否循环 ---- 不存数据库 ----;不可为空

	// type 常量
	public static final String TYPE_CELING = "type_celing";
	public static final String TYPE_YAO = "type_yao";
	public static final String TYPE_HULI = "type_huli";
	}
```
> 操作闹钟的方法  发送一个启动广播的意图到定时器中

```
//AlarmManager  闹钟管理器	 将闹钟存储到数据库  并发送意图
	public static void setRemind(AlarmDB alarmDB, Alarm alarm,AlarmManager alarmManager, Context context) {
		
		String timeStr = String.valueOf(alarm.setTime);
		// 取出时间的最后8位数字
		alarm.alarmId = Integer.valueOf(timeStr.substring(timeStr.length() - 8,timeStr.length()));
		
		alarmDB.insertAlarm(alarm);
		
		//将闹钟对象的信息  放到Intent中  发送给RemindReceiver这个广播接受器
		Intent intent = new Intent();
		
		Bundle bundle = new Bundle();
		bundle.putSerializable("alarm", alarm);
		intent.putExtras(bundle);
		
		intent.setClass(context, RemindReceiver.class);
		
		//这是一个定时的意图     全局定时器
		PendingIntent pi = PendingIntent.getBroadcast(context, alarm.alarmId,intent, PendingIntent.FLAG_UPDATE_CURRENT);
		
		if (alarm.isRepeat) {//如果这是一个循环闹钟
			//早于当前时间，到下周开始提醒
			if(alarm.alarmTime < DateUtil.getNowTimeInMillis()){
				alarm.alarmTime = alarm.alarmTime + DateUtil.WEEK_MILLIS;
			}
			//闹钟管理器  定时去发送一个定时的任务  重复闹钟的设置方法
			alarmManager.setRepeating(AlarmManager.RTC_WAKEUP, alarm.alarmTime,alarm.repeatTime, pi);
		} else {//非重复闹钟
			//晚于当前时间的闹钟，才设置提醒
			if(alarm.alarmTime > DateUtil.getNowTimeInMillis()){
				alarmManager.set(AlarmManager.RTC_WAKEUP, alarm.alarmTime, pi);
			}
		}
	}

	//AlarmManager  闹钟管理器	发送意图   但不存到数据库
	public static void setRemindNoDB(Alarm alarm,AlarmManager alarmManager, Context context){
		Intent intent = new Intent();
		
		Bundle bundle = new Bundle();
		bundle.putSerializable("alarm", alarm);
		intent.putExtras(bundle);
		
		intent.setClass(context, RemindReceiver.class);
		
		PendingIntent pi = PendingIntent.getBroadcast(context, alarm.alarmId,intent, PendingIntent.FLAG_UPDATE_CURRENT);
		if (alarm.isRepeat) {//如果是一个重复的闹钟
			//早于当前时间，到下周开始提醒
			if(alarm.alarmTime < DateUtil.getNowTimeInMillis()){
				alarm.alarmTime = alarm.alarmTime + DateUtil.WEEK_MILLIS;
			}
			alarmManager.setRepeating(AlarmManager.RTC_WAKEUP, alarm.alarmTime,alarm.repeatTime, pi);
		} else {
			if(alarm.alarmTime > DateUtil.getNowTimeInMillis()){
				alarmManager.set(AlarmManager.RTC_WAKEUP, alarm.alarmTime, pi);
			}
		}
	}
	
	//关闭闹钟
	public static void closeRemind(AlarmManager alarmManager, Context context, int alarmId) {
		Intent intent = new Intent();
		
		intent.setClass(context, RemindReceiver.class);
		PendingIntent pi = PendingIntent.getBroadcast(context, alarmId, intent, 0);
		
		alarmManager.cancel(pi);
	}
```


