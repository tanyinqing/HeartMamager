
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 构造器

```
public class AlarmDB {
	
	@SuppressWarnings("unused")
	private Context context;
	private DBHelper helper;

	public AlarmDB(Context context) {
		helper = new DBHelper(context, "HeartMark.db", null, DBHelper.DATABASE_VERSION);
		this.context = context;
	}
	}
```
> 获取是否有未同步的数据

```
/**
	 * 获取是否有未同步的数据
	 */
	public boolean haveNoSync(String uid) {
		boolean isHave = false;
		SQLiteDatabase db = helper.getWritableDatabase();
		Cursor cursor = db.query("celingData", new String[] { "id", "uid","sync", "date",
				"dateMonth", "day", "time", "type","result", "state", "diag" }, "sync=? and uid=?", new String[] { String.valueOf(ConstantUtil.SYNC_NO), uid}, null, null,null);
		if(cursor.moveToFirst() == false){
			isHave = false;
		}else{
			isHave = true;
		}
		cursor.close();
		db.close();
		return isHave;
	}
```



> 增删改查的操作

```
//插入一个对象
	public void insertAlarm(Alarm alarm) {
		SQLiteDatabase db = helper.getWritableDatabase();
		ContentValues values = new ContentValues();
		values.put("type", alarm.type);
		values.put("alarmId", alarm.alarmId);
		values.put("alarmTime", alarm.alarmTime);
		values.put("time", alarm.time);
		values.put("week", alarm.week);
		values.put("yaoId", alarm.yaoId);
		values.put("yaoName", alarm.yaoName);
		values.put("yaoType", alarm.yaoType);
		values.put("setTime", alarm.setTime);
		db.insert("alarm", null, values);
		db.close();
	}
	
	//插入一个集合的操作
	public void insertList(ArrayList<HuLiWeight> list) {
    		SQLiteDatabase db = helper.getWritableDatabase();
    		for(int i=0;i<list.size();i++){
    			ContentValues values = new ContentValues();
    			HuLiWeight huliWeight = list.get(i);
    			values.put("uid", huliWeight.uid);
    			values.put("sync", huliWeight.sync);
    			values.put("date", huliWeight.date);
    			values.put("dateMonth", huliWeight.dateMonth);
    			values.put("day", huliWeight.day);
    			values.put("time", huliWeight.time);
    			values.put("timeMill", huliWeight.timeMill);
    			values.put("baseWeight", huliWeight.baseWeight);
    			values.put("thisWeight", huliWeight.thisWeight);
    			values.put("diff", huliWeight.diff);
    			db.insert("weight", null, values);
    		}
    		db.close();
    	}
    	
	//得到所有对象的集合
	下面这句是利用通配符查到所有的字段
	Cursor cursor = db.query("alarm", new String[] { "*"}, null, null, null, null, null);
	
    	public ArrayList<Alarm> getAlarmList() {
    		SQLiteDatabase db = helper.getWritableDatabase();
    		ArrayList<Alarm> alarmArray = new ArrayList<Alarm>();
    		Cursor cursor = db.query("alarm", new String[] { "id", "alarmId","yaoId"}, null, null, null, null, null);
    		//将cursor对应的值放入链表集合中
    		cursorMethod(alarmArray, cursor);
    		cursor.close();
    		db.close();
    		return alarmArray;
    	}
    	
    	//查询使用某种药的人群
        	public ArrayList<Alarm> getAlarmListByYao(int yaoId) {
        		SQLiteDatabase db = helper.getWritableDatabase();
        		ArrayList<Alarm> alarmArray = new ArrayList<Alarm>();
        		Cursor cursor = db.query("alarm", new String[] { "id", "alarmId","yaoId"}, "yaoId=?", 
        				new String[] {String.valueOf(yaoId)}, null, null, null);
        		cursorMethod(alarmArray, cursor);
        		cursor.close();
        		db.close();
        		return alarmArray;
        	}
        	
        	public ArrayList<Alarm> getAlarmListByYaoType(String type, String yaoType) {
            		SQLiteDatabase db = helper.getWritableDatabase();
            		ArrayList<Alarm> alarmArray = new ArrayList<Alarm>();
            		Cursor cursor = db.query("alarm", new String[] { "id", "type","alarmId","alarmTime","time","week","yaoId","yaoName","yaoType","setTime"}, 
            				"type=? and yaoType=?", new String[] {type, yaoType}, null, null, null);
            		cursorMethod(alarmArray, cursor);
            		cursor.close();
            		db.close();
            		return alarmArray;
            	}
            	
            	/**
                	 * 查询数据,批号
                	 */
                	public Scan getScanByNumber(String scanNumber) {
                		SQLiteDatabase db = helper.getWritableDatabase();
                		Scan scan = new Scan();
                		Cursor cursor = db.query("scan", new String[] { "id", "type", "scan",
                				"scanNumber" }, "scanNumber=?", new String[] { scanNumber },null, null, null);
                		while (cursor.moveToNext()) {
                			scan.id = cursor.getInt(cursor.getColumnIndex("id"));
                			scan.type = cursor.getString(cursor.getColumnIndex("type"));
                			scan.scan = cursor.getString(cursor.getColumnIndex("scan"));
                			scan.scanNumber = cursor.getString(cursor.getColumnIndex("scanNumber"));
                		}
                		cursor.close();
                		db.close();
                		return scan;
                	}
                	
                	
            	/**
                	 * 修改数据
                	 */
                	public int updataSyncNoToYes(String uid) {
                		SQLiteDatabase db = helper.getWritableDatabase();
                		ContentValues values = new ContentValues();
                		values.put("sync", ConstantUtil.SYNC_YES);
                		int result = db.update("celingData", values, "sync=? and uid=?",new String[] { String.valueOf( ConstantUtil.SYNC_NO), uid});
                		/*int result = db.update("celingData", 数据表名
                		 * values, 代表想跟新的数据
                		 * "sync=? and uid=?",  满足条件的子类
                		 * new String[] { String.valueOf( ConstantUtil.SYNC_NO), uid});  为子句传入参数
                		 * */
                		db.close();
                		return result;
                	}
                	
            	//删除某种定时
                	public void delete(int yaoId) {
                		SQLiteDatabase db = helper.getWritableDatabase();
                		db.delete("alarm", "yaoId=?",new String[] {String.valueOf(yaoId)});
                		db.close();
                	}
                	
                	     /*
                	      * 删除该表数据
                    	 */
                    	public void delete() {
                    		SQLiteDatabase db = helper.getWritableDatabase();
                    		db.delete("celingData", null,null);
                    		db.close();
                    	}
```

> 将cursor对应的值放入链表集合中

```
//将cursor对应的值放入链表集合中
	private void cursorMethod(ArrayList<Alarm> alarmArray, Cursor cursor) {
		while (cursor.moveToNext()) {
			Alarm alarm = new Alarm();
			alarm.id = cursor.getInt(cursor.getColumnIndex("id"));
			alarm.type = cursor.getString(cursor.getColumnIndex("type"));
			alarm.alarmId = cursor.getInt(cursor.getColumnIndex("alarmId"));
			alarm.alarmTime = cursor.getLong(cursor.getColumnIndex("alarmTime"));
			alarm.setTime = cursor.getLong(cursor.getColumnIndex("setTime"));
			alarm.time = cursor.getString(cursor.getColumnIndex("time"));
			alarm.week = cursor.getString(cursor.getColumnIndex("week"));
			alarm.yaoId = cursor.getInt(cursor.getColumnIndex("yaoId"));
			alarm.yaoName = cursor.getString(cursor.getColumnIndex("yaoName"));
			alarm.yaoType = cursor.getString(cursor.getColumnIndex("yaoType"));
			alarmArray.add(alarm);
		}
	}
```