
<center><h6 style="magrin-bottom:0px;text-align:left;font-size:12px;">2017年9月</center>

涉及到的变量
```
//打印日志
	private static final String TAG = "MainCeLingActivity";
	
	//单例模式
	public static MainCeLingActivity instance;
	
	/*表示本地蓝牙适配器（蓝牙无线电广播）。
	BluetoothAdapter是所有蓝牙交互的入口点。
	你能够通过它发现其它蓝牙设备，查询一系列已经匹配的设备，
	使用已知的MAC地址实例化一个 BluetoothDeviced对象，
	创建 BluetoothServerSocket 监听来自其他设备的通信。*/
	
	private BluetoothAdapter bluetoothAdapter;
	
	/*表示远程蓝牙设备。用这个类通过 BluetoothSocket
	 * 能够请求同远程设备的链接，或者查询设备的名字、
	 * 地址、类和绑定状态。*/
	private BluetoothDevice bluetoothDevice;
	
	//设备的连接线路
	private BluetoothSocket bluetoothSocket;

	public static final String BLUETOOTH_SPP = "0F:03:14:C2:00:25"; // 蓝牙spp地址
	public static final String BLUETOOTH_BLE = "0F:04:14:C2:00:25"; // 蓝牙ble地址
	// private static final String TEST_OUTER= "SetSerial:'"; // 机外发送设备头
	private static final String TEST_IN = "SetIn:'"; // 机内发送设备头
	
	/*关于UUID   通用唯一标识符（UUID）是为字符串ID而生成的标准化128bit格式，
	 * 用于唯一标示信息。UUID的关键点是它足够大，
	 * 以至于你任意随即选择都不会产生冲突。
	 * 在此情况中，它被用于标识你应用的蓝牙服务。
	 * 为了获得一个你的应用可以使用的UUID，
	 * 你可以从众多的UUID生成器中任意选择一个，
	 * 然后通过fromString(String)实例化一个UUID。*/
	private static final String SPP_UUID = "00001101-0000-1000-8000-00805F9B34FB";

	public static final int device_open_time = 3000; //三秒打开时间
	public static final int device_open_fail = 19; //蓝牙打开失败
	public static final int device_notfind = 20; // 未搜索到设备
	public static final int connect_ing = 21; // 连接过程中
	public static final int connect_success = 22;
	public static final int connect_fail = 23;
	public static final int receive_message = 24; // 接受到数据
	public static final int device_bond_fail = 25;

	private boolean isConnected = false;

	// 蓝牙通讯流
	private OutputStream mOutputStream;
	private InputStream mInInputStream;
//休眠100豪秒 后重新工作
	public static final int SLEEP_RECEIVE_TIME = 100;// 100ms
	public static final int SLEEP_SEND_TIME = 100;// 再发送指令，延时100ms

	private ReceiveMessageThread mReceiveMessageThread = null;// 收消息线程
	private boolean isReceiveMessage = false;
	public String receiveResult = "";

	// button的状态用于监听不同作用
	private final String buttonSearch = "连接设备中";
	private final String buttonSearchFail = "重新连接设备";
	private final String buttonChoose = "选择批号";
	private final String buttonStart = "开始测量";
	public final String buttonGet = "获取结果";

	public Button buttonNext; // 测量按钮
	private Button buttonGetResult; // 获取结果按钮
	private Button buttonRestart; // 从新测量
	private TextView textHint;
	private LinearLayout showLayout1;
	private LinearLayout showLayout2;
	private LinearLayout showLayout3;
	private Button step1;
	private Button step2;
	private Button step3;
	private ArrayList<Scan> scanList;
	private ArrayList<String> scanNumberArray;
	private String scanValue = null; // 标曲值
	private int countDown = 0; // 倒计时等待时间
	private Timer connectTimer; // 连接蓝牙定时器
	private Timer sendTimer; //发送消息定时(设备突然断电等情况)
	private boolean isDestory = false; // myCount没有stop方法，因此在倒计时过程中退出的话，mycount会执行到onfinish;
	private boolean isGetResult = false; // 是否点击了获取结果
	private boolean isTimeOut = false;
```

