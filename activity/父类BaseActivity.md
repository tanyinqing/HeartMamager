
<img src="/image/page/主页.jpg" title="主页" width="300">

进度条调用的方法

```
handlerBase.sendEmptyMessage(ConstantUtil.DIALOG_SHOW);

```
进度条的显示
```
public BufferProgressDialog profressDialog = null;

@SuppressLint("HandlerLeak")
	public Handler handlerBase = new Handler() {

		public void handleMessage(android.os.Message msg) {
			switch (msg.what) {
			case ConstantUtil.DIALOG_SHOW:
				profressDialog = new BufferProgressDialog(
						BaseActivity.this);
				break;
			case ConstantUtil.DIALOG_HINT:
				if (profressDialog != null) {
					profressDialog.destroyProgressDialog();
					profressDialog = null;
				}
				break;
			default:
				break;
			}
		};
	};
```






