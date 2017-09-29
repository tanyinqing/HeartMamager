
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 利用SQL语句建表 这种建表方式方便做注释  以后两种建表方法共用 建2个数据库 拷贝的数据库存常规数据 自建的数据库放业务相关的数据

```
/*
    *    photo表建表语句
    */
	public static final String CREATE_PHOTO = "create table photo ("
			+ "id integer NOT NULL primary key autoincrement,"//注释
			+ "[photo] VARCHAR(50), "
			+ "[rmd5] VARCHAR(100), "
			+ "[time] VARCHAR(100))";
	/*
    *    BasicData表建表语句
    */
	public static final String CREATE_BASICDATA = "create table BasicData ("
			+ "id integer NOT NULL primary key autoincrement,"//注释 id 自增长
			+ "[RenWuId] intege, "
			+ "[type] VARCHAR(100), "
			+ "[frequency] integer)";//次数

@Override
	public void onCreate(SQLiteDatabase db) {
		db.execSQL(CREATE_BASICDATA);
		db.execSQL("alter table SecurityItemEntity add column ST_Id VARCHAR(100)");
		db.execSQL("alter table SecurityItemEntity add column SN_Number VARCHAR(100)");
	}
```
> 燃气中用到的分页查询数据

```
//得到分页内所有对象的集合  决定只加载分页内的7个条目
	public ArrayList<Task_entity> getAlarmList11() {
		String UserId=ServiceApplication.getInstance().readUser().getId();
		int yema=ServiceApplication.mPrefUtil.getIntSetting(Constant.FenYeYeMa);
		if (yema<0){  //防止首页为小于1的情况出现
			yema=0;
			ServiceApplication.mPrefUtil.putSetting(Constant.FenYeYeMa,0);
		}
		SQLiteDatabase db = helper.getWritableDatabase();
		ArrayList<Task_entity> alarmArray = new ArrayList<Task_entity>();
		Cursor cursor = db.query("Task_entity", new String[] {"*"}, "LeiBie=? and UserId=?", new String[] {"1",UserId}, null, null, null,yema*7+","+7);
		//将cursor对应的值放入链表集合中
		cursorMethod1(alarmArray, cursor);
		cursor.close();
		db.close();
		return alarmArray;
	}
```
> 通过Group找到所有的小区的集合 以及一个字段去适配两个值

```
// 通过Group找到所有的小区的集合
	public List<String> getAlarmListByShenHeShuJu1(String SP_Id) {
		String UserId= ServiceApplication.getInstance().readUser().getId();
		SQLiteDatabase db = helper.getWritableDatabase();
		ArrayList<String> alarmArray = new ArrayList<String>();
		Cursor cursor = db.query("Task_entity", new String[] {"*"}, "(LeiBie=? or LeiBie=?) and UserId=? and SP_Id=?", new String[] {"3","4",UserId,SP_Id},"XiaoQuId", null, null);
		while (cursor.moveToNext()) {
			alarmArray.add(cursor.getString(cursor.getColumnIndex("XiaoQuId")));
		}
		cursor.close();
		db.close();
		return alarmArray;
	}
```