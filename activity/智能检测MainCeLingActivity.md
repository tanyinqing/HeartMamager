
<center><h6 style="magrin-bottom:0px;text-align:left;font-size:12px;">2017年9月</center>

点击按钮的响应 连接蓝牙 并选择批号

从手机向设备发送消息


> 涉及到的变量

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
	 这个值相当于端口号 两台设备必须一致才可以通信
	private static final String SPP_UUID = "00001101-0000-1000-8000-00805F9B34FB";

定义状态关键词
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
	private boolean isReceiveMessage = false;
    public String receiveResult = "";	
	
//休眠100豪秒 后重新工作
	public static final int SLEEP_RECEIVE_TIME = 100;// 100ms
	public static final int SLEEP_SEND_TIME = 100;// 再发送指令，延时100ms

	private ReceiveMessageThread mReceiveMessageThread = null;// 收消息线程
	

	// button的状态用于监听不同作用
	private final String buttonSearch = "连接设备中";
	private final String buttonSearchFail = "重新连接设备";
	private final String buttonChoose = "选择批号";
	private final String buttonStart = "开始测量";
	public final String buttonGet = "获取结果";

多功能的按钮控件
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
	
	这个是测量的数据
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

>初始化程序 onCreate
```
instance = this;
init();
```
init 初始化的工作
```
这里是3个常量
public static final String CELING_FROM = "celingFrom";// 测量界面from key
public static final String CELING_DESTORY = "celingDestory";// 测量界面destory key
	
private boolean isDestory = false; // myCount没有stop方法，因此在倒计时过程中退出的话，mycount会执行到onfinish;
界面是否销毁
isDestory = SharedPreferenceUtil.getBoolean(MainCeLingActivity.this,
				ConstantUtil.CELING_DESTORY);
		// 若从通知栏进来会重新Create 当前activity，因此需要销毁掉。
		String from = getIntent().getStringExtra(ConstantUtil.CELING_FROM);
		if (from == null || !from.equals("main") && isDestory == false) {			
			finish();
		}
		SharedPreferenceUtil.setBoolean(MainCeLingActivity.this,
				ConstantUtil.CELING_DESTORY, false); false这个缓存数据表明界面时未关闭的  true 表明界面时关闭的
				
		标题和变量的初始化
		
		按钮加入监控
	        	buttonNext.setOnClickListener(new MyViewOnclicklistener());
        		buttonRestart.setOnClickListener(new MyViewOnclicklistener());
        		buttonGetResult.setOnClickListener(new MyViewOnclicklistener());
        		
        		bluetoothAdapter = BluetoothAdapter.getDefaultAdapter();
        		
        		// 注册蓝牙广播
                		registerBluetooth();
                		
                		// 上次是否测量完成，显示不同界面
                        		if (getStart()) {
                        			showLayout(4);
                        		} else {
                        			showLayout(1);
                        		}
```
>扫描接收的数据的集合
```
public class Scan {
	public int id;
	public String type; //
	public String scan;
	public String scanNumber;
}

```
> 注册蓝牙生成的广播
```
// 注册接收蓝牙状态返回的广播
	private void registerBluetooth() {
		IntentFilter intentFilter = new IntentFilter();
		intentFilter.addAction(BluetoothAdapter.ACTION_STATE_CHANGED);
		intentFilter.addAction(BluetoothDevice.ACTION_FOUND);
		intentFilter.addAction(BluetoothDevice.ACTION_BOND_STATE_CHANGED);
		intentFilter.addAction(BluetoothAdapter.ACTION_SCAN_MODE_CHANGED);
		intentFilter.addAction(BluetoothAdapter.ACTION_DISCOVERY_FINISHED);
		registerReceiver(receiverBluetooth, intentFilter);
	}
// 蓝牙状态返回的广播
	private BroadcastReceiver receiverBluetooth = new BroadcastReceiver() {
		@Override
		public void onReceive(Context context, Intent intent) {
			Log.d(TAG, intent.getAction());
			// 搜索到蓝牙
			if (BluetoothDevice.ACTION_FOUND.equals(intent.getAction())) {
				BluetoothDevice device = intent
						.getParcelableExtra(BluetoothDevice.EXTRA_DEVICE);
				Log.d(TAG, "蓝牙名字:" + device.getName() + "   蓝牙mac地址:"
						+ device.getAddress());
				String name = device.getName();
				String mac = device.getAddress();
				//if (device != null && name != null&& name.substring(0, 2).equals("LP")&& mac.split(":")[1].equals("03")) {
				//用小米3手机模拟设备
				if (device != null && name != null&& name.substring(0, 2).equals("LP")&& mac.split(":")[1].equals("97")) {
					bondBluetoothDevice(device, false);//绑定蓝牙
					return;
				}
			}
			// 改变
			if (BluetoothDevice.ACTION_BOND_STATE_CHANGED.equals(intent
					.getAction())) {
				Log.d(TAG, "收到changed广播");
				if (bluetoothDevice != null&& bluetoothDevice.getBondState() == BluetoothDevice.BOND_BONDED) {
					//连接蓝牙的线程开启
					ConnectThread connectThread = new ConnectThread(bluetoothDevice, true);
					//if (!connectThread.isAlive()) {
					connectThread.start();
					startConnectTimer();
					handlerBase.sendEmptyMessage(ConstantUtil.DIALOG_SHOW);
					//}
					
					Log.d(TAG, "连接绑定的设备");
				}
				//绑定失败
				else
				{
					sppHandler.obtainMessage(device_bond_fail).sendToTarget();
				}
			}
			// 搜索完毕
			if (BluetoothAdapter.ACTION_DISCOVERY_FINISHED.equals(intent
					.getAction())) {
                Log.d(TAG, "搜索完毕");
			}

		}
	};
```
> 连接设备的线程
```
//连接设备线程
	private class ConnectThread extends Thread {

		private BluetoothDevice tempDevice;
		private boolean tempIsOldDevice; //是否搜索
//传递过来两个参数，一个是设备信息，一个是true
		public ConnectThread(BluetoothDevice device, boolean isOldDevice) {
			tempDevice = device;
			tempIsOldDevice = isOldDevice;
			
			//如果是获取结果的话不需要搜索,获取指定设备
			if(isGetResult){
				tempIsOldDevice = false;
			}
		}

		@SuppressLint("NewApi")
		@Override
		public void run() {

			UUID uuid = UUID.fromString(SPP_UUID);
			Log.d(TAG, "第一次连接");
			// 先试一下createInsecureRfcommSocketToServiceRecord
			try {
				bluetoothSocket = tempDevice
						.createInsecureRfcommSocketToServiceRecord(uuid);
				bluetoothSocket.connect();

				isConnected = true;
			} catch (IOException e) {
				Log.d(TAG, "第一次连接失败"+e);
			}


			if (isConnected && !isTimeOut) {
				// 记录最新的连接设备名称
				SharedPreferenceUtil.setString(MainCeLingActivity.this,ConstantUtil.CELING_DEVICE, tempDevice.getName());

				sppHandler.obtainMessage(connect_success).sendToTarget();
			} else {
				// 再试一下 createRfcommSocketToServiceRecord
				try {
					Log.d(TAG, "第二次连接");
					bluetoothSocket = tempDevice.createRfcommSocketToServiceRecord(uuid);
					bluetoothSocket.connect();

					isConnected = true;
				} catch (IOException e) {
					Log.d(TAG, "第二次连接失败"+e);
					try {
						if (bluetoothSocket != null) {
							bluetoothSocket.close();
						}
					} catch (IOException e1) {
						e1.printStackTrace();
					}
				}

				if (isConnected && !isTimeOut) {
					// 记录最新的连接设备名称
					SharedPreferenceUtil.setString(MainCeLingActivity.this,ConstantUtil.CELING_DEVICE, tempDevice.getName());

					sppHandler.obtainMessage(connect_success).sendToTarget();
				} else {
					if (tempIsOldDevice && !isTimeOut) {
						connectBondBluetooth(true);
					} else {
						// 新搜索出来的，失败
						Log.d(TAG, "发送失败消息");
						//sppHandler.obtainMessage(connect_fail).sendToTarget();
					}
				}
			}
		}
	}
```
> 执行绑定设备的方法
```
// 没绑定 先绑定,绑定过得话直接取连接(绑定完之后会走广播去连接) 参数为false
	private void bondBluetoothDevice(BluetoothDevice device, boolean isOldDevice) {

		bluetoothAdapter.cancelDiscovery();//停止搜索
		bluetoothDevice = device;
// 获取到设备的连接状态
		int bondState = bluetoothDevice.getBondState();
		if (bondState == BluetoothDevice.BOND_BONDED) {
		new ConnectThread(bluetoothDevice, isOldDevice).start();
			
		} else {
			try {
				//执行绑定  这个是从class文件中获取方法
				Method createBondMethod = BluetoothDevice.class.getMethod("createBond");
				createBondMethod.invoke(bluetoothDevice); 
				stopConnectTimer();
				Log.d(TAG, "执行绑定");
				
			} catch (Exception e) {
				e.printStackTrace();
				// 绑定失败
				//buttonNext.setText(buttonSearchFail);
				//finish();
				Log.d(TAG, "绑定失败"+e);
			}
		}
	}
```
> 连接已经绑定过得设备
```
// 连接已经绑定过得设备
	private void connectBondBluetooth(boolean startSearch) {

		boolean flag = false;
		// 记录最新的连接设备名称
		String lastDeviceName = SharedPreferenceUtil.getString(MainCeLingActivity.this,ConstantUtil.CELING_DEVICE);
		if (lastDeviceName.length() > 0 && !startSearch) {
//获得绑定的设备的列表
			Set<BluetoothDevice> pairedDevices = bluetoothAdapter.getBondedDevices();
			if (pairedDevices != null && pairedDevices.size() > 0) {
				for (BluetoothDevice device : pairedDevices) {
					String name = device.getName();
					if (name != null && !name.equals("")&& name.equals(lastDeviceName)) {
						Log.d(TAG, "开始连接绑定过得设备"+name);
						bondBluetoothDevice(device, true);
						flag = true;
						break;
					}
				}
			}
		}
		
		
```
从手机向设备发送消息
```
public static final int SLEEP_SEND_TIME = 100;// 再发送指令，延时100ms

                  // 上次没有测量完成，直接获取结果
                  String sendValue = "GetDate\r\n";
                  byte[] sendByte = sendValue.getBytes();
                  sendMessages(sendByte);
                  buttonNext.setText(buttonGet);
                  					
// 一次握手代表成功
					String say = "SetCMD:'GetStatus'\r\n";
					byte[] send = say.getBytes();
					sendMessages(send);
					
					
// 从手机向设备发送消息
	public boolean sendMessages(byte[] datas) {

		try {
			Thread.sleep(SLEEP_SEND_TIME); 延时100ms
		} catch (InterruptedException e2) {
			e2.printStackTrace();
		}

		if (bluetoothSocket == null) {
			//ShowUtil.showToast(instance, "尚未连接设备");
			return false;
		}

		boolean flag = false;
		try {
			mOutputStream = bluetoothSocket.getOutputStream();
			if (mOutputStream != null) {
				mOutputStream.flush();
				mOutputStream.write(datas);
				flag = true;
			}
		} catch (Exception e) {
			e.printStackTrace();
			try {
				if (mOutputStream != null) {
					mOutputStream.flush();
					mOutputStream.close();
				}
			} catch (IOException e1) {
				e1.printStackTrace();
			}
		}
		Log.d(TAG, "write success="+flag);
		return flag;
	}
	
```
分析结果值

```
// 分析结果值
	private synchronized void resultJudge(String result) {
		if ("".equals(result) || result == null) {
			ShowUtil.showToast(MainCeLingActivity.this, "无结果");
			setStart(false);
			return;
		}
		if (result.equals("MOER")) {
			ShowUtil.showToast(MainCeLingActivity.this, "电机超时故障");
			return;
		}
		if (result.equals("LGER")) {
			ShowUtil.showToast(MainCeLingActivity.this, "通信故障，请重启设备");
			return;
		}
		if (result.equals("Inv")) {
			ShowUtil.showToast(MainCeLingActivity.this, "无效结果");
			setStart(false);
			return;
		}
		if (result.equals("ComEr")) {
			ShowUtil.showToast(MainCeLingActivity.this, "指令错误");
			return;
		}
		if (result.equals("QrEr")) {
			ShowUtil.showToast(MainCeLingActivity.this, "扫描标曲错误");
			return;
		}
		if (result.equals("QrNT")) {
			ShowUtil.showToast(MainCeLingActivity.this, "标曲与项目不匹配");
			return;
		}
		if (result.substring(result.length() - 1, result.length()).equals("s")) {
			if (isGetResult) {
				showGetResultDialog((Integer.valueOf(result.substring(0,
						result.length() - 1)) + 12)
						+ "秒");
			} else {
				countDown = Integer.valueOf(result.substring(0,
						result.length() - 1)) + 12;
				showLayout(2);
			}
			return;
		}

		Intent resultIntent = new Intent(MainCeLingActivity.this,
				CeLingResultActivity.class);
		resultIntent.putExtra("celingResult", result);
		startActivity(resultIntent);
		setStart(false);
		isGetResult = false;
		stopTest();
	}
```
//按返回键的操作
```
//按返回键的操作
	@Override
	public void onBackPressed() {
		destroy();
		stopTest();
		finish();
	}
	
	//由于onDestroy耗时,所以我们需要在onDestroy之前执行下面代码。
    	public void destroy(){
    		instance = null;
    		unregisterReceiver(receiverBluetooth);//注销广播
    		SharedPreferenceUtil.setBoolean(MainCeLingActivity.this,ConstantUtil.CELING_DESTORY, true);
    	}
    	
    	private void stopTest() {
        //关闭输入流 输出流 蓝牙连接 和接收信息的线程就可以了
        		try {
        			if (mOutputStream != null) {
        				mOutputStream.close();
        				mOutputStream = null;
        			}
        			if(mInInputStream != null){
        				mInInputStream.close();
        				mInInputStream = null;
        			}
        			if(bluetoothSocket != null){
        				bluetoothSocket.close();
        				bluetoothSocket = null;
        			}
        			
        			isConnected = false;
        			isReceiveMessage = false;
        			if(mReceiveMessageThread != null){
        				mReceiveMessageThread = null;
        			}
        			
        			stopConnectTimer();
        			stopSendTimer();
        			//清除所有消息,以免退出后发消息发生异常
        			//handlerBase.removeCallbacksAndMessages(null);
        			//sppHandler.removeCallbacksAndMessages(null);
        			
        		    Thread.sleep(SLEEP_SEND_TIME);
        			
        		} catch (Exception e) {
        			e.printStackTrace();
        		}
        	}
        	
        	public void stopConnectTimer() {
            		if (connectTimer != null) {
            			connectTimer.cancel();// 退出计时器
            		}
            	}
            	
            	public void stopSendTimer() {
                		if (sendTimer != null) {
                			sendTimer.cancel();// 退出计时器
                		}
                	}
```
各种弹出框
```
// 是否连接设备
	private void showConnectDialog(final boolean isStart) {
		// 退出当前activity,不显示此对话框
		if (instance == null) {
			return;
		}
		MyDialog dialog = new MyDialog(MainCeLingActivity.this);
		dialog.setTitle(R.string.phone_title);
		dialog.setMessage("蓝牙连接失败,请保持设备开启,点击按钮重新连接设备");
		dialog.setPositiveButton(R.string.ok, new View.OnClickListener() {

			@Override
			public void onClick(View arg0) {
				if (isStart) {
					//startTest();
				}
			}
		});

		dialog.create(null);
		dialog.showMyDialog();
	}
```
连接超时和发送消息超时
```
//连接超时
	public void startConnectTimer() {
		TimerTask task = new TimerTask() {
			public void run() {
				//停止搜索,发送失败消息
				bluetoothAdapter.cancelDiscovery();
				stopTest();
				isTimeOut = true;
				sppHandler.obtainMessage(connect_fail).sendToTarget();
				
			}
		};
		connectTimer = new Timer(true);
		connectTimer.schedule(task, 10 * 1000);
		isTimeOut = false;
	}
	
	//发送消息超时
    	public void startSendTimer() {
    		TimerTask task = new TimerTask() {
    			public void run() {
    				//停止搜索,发送失败消息
    				sppHandler.obtainMessage(connect_fail).sendToTarget();
    			}
    		};
    		sendTimer = new Timer(true);
    		sendTimer.schedule(task, 10 * 1000);
    	}
```
点击按钮的响应  连接蓝牙 并选择批号
```
class MyViewOnclicklistener implements View.OnClickListener {

		@Override
		public void onClick(View v) {
			final int rid = v.getId();
			switch (rid) {

			case R.id.celing_next_button:
				if (btnText().equals(buttonSearchFail)) {
					startTest();
				}
				// 点击选择批号
				else if (btnText().equals(buttonChoose)) {
					Map<String, String> params = new HashMap<String, String>();
					// 有网络获取服务器上的标曲，没有网络本地扫描
					if (ConnectUtil.isConnect(AppContext.context)) {
						ConnectionManager.getInstance().send("FN11080WD00",
								"queryBnpStandardCurve", params,
								"getScanCallBack", MainCeLingActivity.this);
						handlerBase.sendEmptyMessage(ConstantUtil.DIALOG_SHOW);
					} else {
						if (instance != null)
							showScanDialog();
					}
				}
				// 点击开始测量，开始倒计时
				else if (btnText().equals(buttonStart)) {
					showLayout(3);
					MyCount.getInstance(MainCeLingActivity.this,countDown * 1000, 1000).start();
					buttonNext.setClickable(false);
					setStart(true);

					String sendValue = "InStart\r\n";
					byte[] sendByte = sendValue.getBytes();
					sendMessages(sendByte);
				}
				// 点击获取结果
				else if (btnText().equals(buttonGet)) {
					if (ConnectUtil.isFastDoubleClick(2000)) {
						return;
					}
					isGetResult = true;
					handlerBase.sendEmptyMessage(ConstantUtil.DIALOG_SHOW);
					// 表明设备连接状态
					if (isConnected) {
						String sendValue = "GetDate\r\n";
						byte[] sendByte = sendValue.getBytes();
						sendMessages(sendByte);
						buttonNext.setText(buttonGet);
						//可能设备断电收不到结果,定时提示用户
						startSendTimer();
					} else {
						startTest();
					}
				}
				break;
			// 重新测量
			case R.id.celing_get_button:
				if (ConnectUtil.isFastDoubleClick(2000)) {
					return;
				}
				isGetResult = true;
				handlerBase.sendEmptyMessage(ConstantUtil.DIALOG_SHOW);
				// 表明设备连接状态
				if (isConnected) {
					String sendValue = "GetDate\r\n";
					byte[] sendByte = sendValue.getBytes();
					sendMessages(sendByte);
					buttonNext.setText(buttonGet);
					//可能设备断电收不到结果,定时提示用户
					startSendTimer();
				} else {
					startTest();
				}

				break;
			case R.id.celing_restart_button:
				showRestartDialog();
				break;
			default:
				break;
			}
		}
	}
```
此handler只用于跟蓝牙通讯
```
public final Handler sppHandler = new Handler() {
		public void handleMessage(Message msg) {

			switch (msg.what) {
			case connect_success:
				//关闭进度条
				handlerBase.sendEmptyMessage(ConstantUtil.DIALOG_HINT);
				stopConnectTimer();
				startReceiveMessageThread();
				// 告诉设备
				if (isGetResult) {
					// 上次没有测量完成，直接获取结果
					String sendValue = "GetDate\r\n";
					byte[] sendByte = sendValue.getBytes();
					sendMessages(sendByte);
					buttonNext.setText(buttonGet);
				} else {
					// 一次握手代表成功
					String say = "SetCMD:'GetStatus'\r\n";
					byte[] send = say.getBytes();
					sendMessages(send);
				    //Toast.makeText(MainCeLingActivity.this, "建立连接   发送消息 = "+sendResult, Toast.LENGTH_LONG).show();
				}
				break;
			case connect_fail:
				handlerBase.sendEmptyMessage(ConstantUtil.DIALOG_HINT);
				stopConnectTimer();
				//如果蓝牙不能用
				if(!bluetoothAdapter.isEnabled()){
					ShowUtil.showToast(MainCeLingActivity.this, R.string.celing_open_fail);
				}
				if (isGetResult) {
					if (instance != null)
						showConnectDialog(false);
				} else {
					if (instance != null)
						showConnectDialog(false);
					buttonNext.setText(buttonSearchFail);
				}
				break;
			case device_open_fail:
				ShowUtil.showToast(MainCeLingActivity.this, R.string.celing_open_fail);
				stopTest();
				finish();
				break;
			case device_notfind:

				break;
			case device_bond_fail:
				buttonNext.setText(buttonSearchFail);
				break;
			case connect_ing:
				buttonNext.setText(buttonSearch);
				break;
			case receive_message:
			    stopSendTimer();
			    stopConnectTimer();
				handlerBase.sendEmptyMessage(ConstantUtil.DIALOG_HINT);
				String readMessage = msg.obj.toString();
				if (readMessage.indexOf("READY") != -1) {
					buttonNext.setText(buttonChoose);
					textHint.setVisibility(View.VISIBLE);
				}
				// 结果
				else {
					String[] temp = readMessage.split("#");
					if (temp.length > 0) {
						resultJudge(temp[0]);
					}
				}
				break;
			default:
				break;
			}

		}
	};

```
开启接受信息的线程  和接收任务线程的定义
```
//开启接受信息的线程
	private void startReceiveMessageThread() {
		if (mReceiveMessageThread == null) {
			mReceiveMessageThread = new ReceiveMessageThread();
			mReceiveMessageThread.start();
			isReceiveMessage = true;
		}
	}
	
	// 收消息线程
    	private class ReceiveMessageThread extends Thread {
    		@Override
    		public void run() {
    			// synchronized (mLockObj) {
    
    			if (bluetoothSocket != null) {
    
    				try {//从连接中获取输入流
    					mInInputStream = bluetoothSocket.getInputStream();
    
    					byte[] buffer = null;
    					int len = 0;
    
    					while (isReceiveMessage) {
    
    						buffer = new byte[1024];
    
    						if (mInInputStream != null&& mInInputStream.available() > 0) {
    							len = mInInputStream.read(buffer);
    							Log.d("tanyinqing",buffer.toString());
    						} else {
    							len = 0;
    						}
    
    						// 发送过程中数据为13字节，（发送过程中，有可能每次不是13个字节，4个字节
    						// 9个字节两次发）发生丢包数据
    						for (int i = 0; i < len; i++) {
    							Log.d(null, (buffer[i]) + "=======>len=" + len);
    						}
    
    						if (len > 0) {
    							byte[] buf_data = new byte[len];
    							for (int i = 0; i < len; i++) {
    								buf_data[i] = buffer[i];
    							}
    							String msg = new String(buf_data);
    							//等到消息接受完成，在发送出去
    							if (msg.indexOf("READY") != -1|| msg.indexOf("QROK") != -1) {
    								//接受到的信息叠加
    								sppHandler.obtainMessage(receive_message, len,-1, msg).sendToTarget();
    							} else {
    								receiveResult = receiveResult + msg;
    								if (receiveResult.indexOf("#") != -1) {
    									sppHandler.obtainMessage(receive_message,len, -1, receiveResult).sendToTarget();
    									receiveResult = "";
    								}
    
    							}
    
    						}
    
    						try {
    							Thread.sleep(SLEEP_RECEIVE_TIME);
    						} catch (InterruptedException e) {
    							e.printStackTrace();
    						}
    
    					}
    				} catch (IOException e) {
    					e.printStackTrace();
    
    					// sppHandler.sendEmptyMessage(MSG_MEASURE_EXCEPTION_FAIL);
    				}
    			} else {
    				// sppHandler.sendEmptyMessage(MSG_MEASURE_EXCEPTION_FAIL);
    			}
    		}
    	}
```
单例模式
```
public static MainCeLingActivity getInstance(){
		if(instance != null){
			return instance;
		}else{
			return new MainCeLingActivity();
		}
	}   
```