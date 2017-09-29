
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 构造器和变量

```
public class DBHelper extends SQLiteOpenHelper {

	public static final String DATABASE_NAME = "HeartMark.db";   //数据库名称
	@SuppressLint("SdCardPath")
	private static final String DATABASE_PATH = "/data/data/com.example.heartmark/databases/";   //数据库的存储路径
	public static final int DATABASE_VERSION = 2;

	public DBHelper(Context context, String name, CursorFactory factory,
			int version) {
		super(context, name, factory, version);
	}

	@Override  //这个是数据库初始化  复制的数据库 升级比较麻烦一点不容易做注释 以后容易混
	public void onCreate(SQLiteDatabase db) {
	}
	
	@Override
    	protected void finalize() throws Throwable {
    		if (this != null)
    			this.close();
    		super.finalize();
    	}
    	
	}
```
> 数据库升级

```
@Override
	public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
		if (newVersion > oldVersion) {

			for (int i = (oldVersion + 1); i <= newVersion; i++)
				upgrade(db, i);//升级程序时，修改了数据库，需要在以下用sql语句去修改
		}
	}

/*
	 * 升级程序时，修改了数据库，需要在以下用sql语句去修改
	 */
	private void upgrade(SQLiteDatabase db, int version) {
		switch (version) {
		case 1:
			break;
		case 2:
			db.beginTransaction();//这个表示开启一个事物
			//添加字段
//			db.execSQL("alter table yao add column yaoTest INTEGER default 0");
			//添加表
//			db.execSQL("create table MyBaodianFavorite ([Id] INTEGER DEFAULT '0' PRIMARY KEY AUTOINCREMENT,[NewsId] INTEGER NOT NULL,[AppType] VARCHAR(50) NOT NULL,[ImageUrl] VARCHAR(50),[Introduction] TEXT NOT NULL,[Title] VARCHAR(50) NOT NULL,[Author] VARCHAR(50) NOT NULL,[PubDate] VARCHAR(50) NOT NULL)");
			db.execSQL("create table image ([content] VARCHAR(100),[image] VARCHAR(100),[newId] VARCHAR(100))");
			db.setVersion(version);
			db.setTransactionSuccessful();//事物成功
			db.endTransaction();//结束事物
			break;
		case 3:
			break;
		case 4:
			break;
		default:
			break;
		}
	}
```

> 复制数据库到sd卡中

```
// 复制数据库到sd卡中
	public static void copyDB(Context context) {
		// DBHelper.context = context;
		// 没有file先创建文件夹
		File dbFile = new File(DATABASE_PATH);
		if (!dbFile.exists()) {
			dbFile.mkdirs();
		} else {
			File dbContentFile = new File(DATABASE_PATH, DATABASE_NAME);
			if (dbContentFile.exists()) {
				return;
			}
		}

		InputStream is = null;
		FileOutputStream fos = null;
		try {
			AssetManager am = context.getAssets();  //管理器
			// 得到.db file
			File saveFile = new File(DATABASE_PATH, DATABASE_NAME);
			// 获取数据库本地资源
			is = am.open("HeartMark.db");
			// 获取数据库输入流
			fos = new FileOutputStream(saveFile);
			byte[] buffer = new byte[8192];
			int count = 0;
			while ((count = is.read(buffer)) > 0) {
				fos.write(buffer, 0, count);
			}

		} catch (Exception e) {
			Log.i("Exception", Log.getStackTraceString(e));
		} finally {
			try {
				if (fos != null)
					fos.close();
				if (is != null)
					is.close();
			} catch (Exception e) {
				Log.i("Exception", Log.getStackTraceString(e));
			}
		}
	}
```
