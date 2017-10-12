
> 自定义弹出框MyDialog

```
package com.yikang.heartmark.widget;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Calendar;
import java.util.Date;
import java.util.List;

import android.annotation.SuppressLint;
import android.app.Dialog;
import android.content.Context;
import android.content.DialogInterface;
import android.text.Html;
import android.text.InputFilter;
import android.text.InputType;
import android.text.method.LinkMovementMethod;
import android.view.Gravity;
import android.view.KeyEvent;
import android.view.LayoutInflater;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.ViewGroup.LayoutParams;
import android.view.inputmethod.InputMethodManager;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.ListAdapter;
import android.widget.ListView;
import android.widget.ProgressBar;
import android.widget.TextView;

import com.example.heartmark.R;
import com.yikang.heartmark.util.DateType;
import com.yikang.heartmark.wheel.ArrayWheelAdapter;
import com.yikang.heartmark.wheel.NumericWheelAdapter;
import com.yikang.heartmark.wheel.OnWheelChangedListener;
import com.yikang.heartmark.wheel.WheelView;

public class MyDialog{

	public static final int START_YEAR = 1900;

	private boolean clickDismiss = true;

	public boolean isClickDismiss() {
		return clickDismiss;
	}

	public void setClickDismiss(boolean clickDismiss) {
		this.clickDismiss = clickDismiss;
	}

	private Context mContext;
	private View dialogView;
	private Dialog mDialog;
	private int width;
	private int height;
	private ProgressBar progressBar;
	private TextView progressText;

	@SuppressLint("InflateParams")
	@SuppressWarnings("deprecation")
	// 构造器
	public MyDialog(Context context) {
		mContext = context;
		LayoutInflater inflater = LayoutInflater.from(mContext);
		dialogView = inflater.inflate(R.layout.my_dialog, null);
		int w1 = ((android.app.Activity) mContext).getWindowManager()
				.getDefaultDisplay().getWidth();
		int w2 = ((android.app.Activity) mContext).getWindowManager()
				.getDefaultDisplay().getHeight();
		// 宽和高 最小值的80%
		width = (int) (Math.min(w1, w2) * 0.8);
	}

	public View getView() {
		return dialogView;
	}

	public Dialog create(final OnClickListener backPressed) {
		mDialog = new Dialog(mContext, R.style.MyDialog) {
			@Override
			public boolean onKeyDown(int keyCode, KeyEvent event) {
				if (backPressed != null) {
					backPressed.onClick(null);
					return true;
				}

				return super.onKeyDown(keyCode, event);
			}
		};
		initStyle();
		mDialog.setContentView(dialogView);
		LayoutParams params = mDialog.getWindow().getAttributes();
		params.width = width;
		if (height > 0)
			params.height = height;
		mDialog.setContentView(dialogView, params);
		setCanceledOnTouchOutside(false);
		return mDialog;
	}

	private void initStyle() {

		// Button button1 = (Button)
		// (dialogView.findViewById(R.id.DialogButton1));
		// Button button2 = (Button)
		// (dialogView.findViewById(R.id.DialogButton2));
		// ImageView middleDivider = (ImageView) dialogView
		// .findViewById(R.id.DialogButtonDivider);
		//
		// if (button1.getVisibility() == View.VISIBLE
		// && button2.getVisibility() == View.VISIBLE) {
		// middleDivider.setVisibility(View.VISIBLE);
		// } else {
		// middleDivider.setVisibility(View.GONE);
		// }
	}

	public void showMyDialog() {
		if (mDialog != null) {
			mDialog.show();
		}
	}

	@SuppressWarnings("deprecation")
	public MyDialog setDialogCustomWidthHeight() {

		if (mDialog != null) {
			int w1 = ((android.app.Activity) mContext).getWindowManager()
					.getDefaultDisplay().getWidth();
			int w2 = ((android.app.Activity) mContext).getWindowManager()
					.getDefaultDisplay().getHeight();
			int ww = (int) (Math.min(w1, w2) * 0.8);
			int hh = (int) (Math.max(w1, w2) * 0.8);
			mDialog.getWindow().setLayout(ww, hh);
			mDialog.getWindow().setBackgroundDrawableResource(R.drawable.trans_bg);
		}

		return this;
	}

	public MyDialog setIcon(int resId) {
		ImageView icon = (ImageView) (dialogView
				.findViewById(R.id.DialogTitleIcon));
		icon.setImageResource(resId);
		icon.setVisibility(View.VISIBLE);
		dialogView.findViewById(R.id.DialogTitle).setVisibility(View.VISIBLE);
		return this;
	}

	public MyDialog setTitle(int resId) {
		TextView title = (TextView) (dialogView
				.findViewById(R.id.DialogTitleText));
		title.setText(resId);
		title.setVisibility(View.VISIBLE);
		dialogView.findViewById(R.id.DialogTitle).setVisibility(View.VISIBLE);
		return this;
	}

	public MyDialog setTitle(String text) {
		TextView title = (TextView) (dialogView
				.findViewById(R.id.DialogTitleText));
		title.setText(text);
		title.setVisibility(View.VISIBLE);
		dialogView.findViewById(R.id.DialogTitle).setVisibility(View.VISIBLE);
		return this;
	}

	public MyDialog setMessage(int resId) {
		TextView text = (TextView) (dialogView
				.findViewById(R.id.DialogContentText));
		text.setText(resId);
		text.setVisibility(View.VISIBLE);
		return this;
	}

	public MyDialog setMessage(String message) {
		TextView text = (TextView) (dialogView
				.findViewById(R.id.DialogContentText));
		text.setText(message);
		text.setVisibility(View.VISIBLE);
		return this;
	}

	public MyDialog addView(View view) {
		LinearLayout content = (LinearLayout) (dialogView
				.findViewById(R.id.DialogContent));
		content.addView(view);
		return this;
	}

	private void hideEditTextSofeBoard() {
		EditText editText = (EditText) dialogView
				.findViewById(R.id.DialogContentEditText);
		if (editText != null && editText.getVisibility() == View.VISIBLE) {
			editText.clearFocus();
			((InputMethodManager) editText.getContext().getSystemService(
					Context.INPUT_METHOD_SERVICE)).hideSoftInputFromWindow(
					editText.getWindowToken(), 0);
		}
	}

	public MyDialog setNegativeButton(int resId, final OnClickListener listener) {
		Button button = (Button) (dialogView.findViewById(R.id.DialogButton1));
		button.setText(resId);
		button.setVisibility(View.VISIBLE);
		dialogView.findViewById(R.id.DialogButton).setVisibility(View.VISIBLE);
		button.setOnClickListener(new OnClickListener() {

			@Override
			public void onClick(View v) {
				if (listener != null)
					listener.onClick(dialogView);

				hideEditTextSofeBoard();

				if (mDialog != null && clickDismiss)
					mDialog.dismiss();
			}
		});
		return this;
	}

	public MyDialog setPositiveButton(int resId, final OnClickListener listener) {
		Button button = (Button) (dialogView.findViewById(R.id.DialogButton2));
		button.setText(resId);
		button.setVisibility(View.VISIBLE);
		dialogView.findViewById(R.id.DialogButton).setVisibility(View.VISIBLE);
		button.setOnClickListener(new OnClickListener() {

			@Override
			public void onClick(View v) {
				if (listener != null)
					listener.onClick(dialogView);

				hideEditTextSofeBoard();

				if (mDialog != null  && clickDismiss)
					mDialog.dismiss();

			}
		});
		return this;
	}

	public MyDialog setBackground(int resId) {
		dialogView.setBackgroundResource(resId);
		return this;
	}

	public MyDialog setHeight(int pix) {
		height = pix;
		return this;
	}

	// 当点击弹出框外的时候 让弹出框消失
	public MyDialog setCanceledOnTouchOutside(boolean cancel) {
		if (mDialog != null)
			mDialog.setCanceledOnTouchOutside(cancel);

		return this;
	}

	public MyDialog setCancelable(boolean cancel) {
		if (mDialog != null)
			mDialog.setCancelable(cancel);

		return this;
	}

	public MyDialog setSimpleItems(String[] items,
			final DialogInterface.OnClickListener listener) {
		ListView list = (ListView) (dialogView
				.findViewById(R.id.DialogContentList));
		list.setVisibility(View.VISIBLE);
		ArrayAdapter<String> adapter = new ArrayAdapter<String>(mContext,
				R.layout.list_simple, items);
		list.setAdapter(adapter);
		list.setFocusable(false);
		list.setCacheColorHint(0);
		list.setDivider(mContext.getResources().getDrawable(R.color.lib_color_list_divider));
		list.setDividerHeight(1);
		list.setChoiceMode(ListView.CHOICE_MODE_NONE);
		list.setOnItemClickListener(new AdapterView.OnItemClickListener() {

			@Override
			public void onItemClick(AdapterView<?> arg0, View arg1, int arg2,
					long arg3) {
				if (listener != null)
					listener.onClick(null, arg2);

				if (mDialog != null  && clickDismiss)
					mDialog.dismiss();
			}
		
		});
		return this;
	}

	public MyDialog setSingleChoiceItems(String[] items, int checkedItem,
			final DialogInterface.OnClickListener listener) {
		ListView list = (ListView) (dialogView
				.findViewById(R.id.DialogContentList));
		list.setVisibility(View.VISIBLE);
		ArrayAdapter<String> adapter = new ArrayAdapter<String>(mContext,
				R.layout.list_single_choice, items);
		list.setAdapter(adapter);
		list.setFocusable(false);
		list.setCacheColorHint(0);
		list.setDivider(mContext.getResources().getDrawable(R.color.lib_color_list_divider));
		list.setDividerHeight(1);
		list.setChoiceMode(ListView.CHOICE_MODE_SINGLE);
		list.setItemChecked(checkedItem, true);
		list.setOnItemClickListener(new AdapterView.OnItemClickListener() {

			@Override
			public void onItemClick(AdapterView<?> arg0, View arg1, int arg2,
					long arg3) {
				if (listener != null)
					listener.onClick(null, arg2);

			}
		});
		return this;
	}

	public MyDialog setListView(ListAdapter adapter,
			final DialogInterface.OnClickListener listener) {
		ListView list = (ListView) (dialogView
				.findViewById(R.id.DialogContentList));
		list.setVisibility(View.VISIBLE);
		list.setAdapter(adapter);
		list.setFocusable(false);
		list.setCacheColorHint(0);
		list.setDivider(mContext.getResources().getDrawable(R.color.lib_color_list_divider));
		list.setDividerHeight(1);
		list.setChoiceMode(ListView.CHOICE_MODE_SINGLE);
		list.setOnItemClickListener(new AdapterView.OnItemClickListener() {

			@Override
			public void onItemClick(AdapterView<?> arg0, View arg1, int arg2,
					long arg3) {
				if (listener != null)
					listener.onClick(null, arg2);

			}
		});
		return this;
	}

	public MyDialog setGridView(ListAdapter adapter,
			final DialogInterface.OnClickListener listener, int numColumns,
			int verticalSpacing, int horizontalSpacing) {
		CustomGridView gridView = (CustomGridView) (dialogView
				.findViewById(R.id.DialogContentGrid));
		gridView.setVisibility(View.VISIBLE);
		gridView.setAdapter(adapter);
		gridView.setNumColumns(numColumns);
		gridView.setVerticalSpacing(verticalSpacing);
		gridView.setHorizontalSpacing(horizontalSpacing);
		gridView.setOnItemClickListener(new AdapterView.OnItemClickListener() {

			@Override
			public void onItemClick(AdapterView<?> arg0, View arg1, int arg2,
					long arg3) {
				if (listener != null)
					listener.onClick(null, arg2);

			}
		});
		return this;
	}

	public EditText getCurrentEditText() {
		EditText editText = (EditText) (dialogView
				.findViewById(R.id.DialogContentEditText));
		return editText;
	}

	public MyDialog setEditText(String text, int inputType,
			InputFilter[] filters) {
		final EditText editText = (EditText) (dialogView
				.findViewById(R.id.DialogContentEditText));
		editText.setVisibility(View.VISIBLE);
		editText.setText(text);
		editText.setInputType(inputType);
		if (inputType == InputType.TYPE_TEXT_FLAG_MULTI_LINE) {
			editText.setGravity(Gravity.TOP);
			editText.setSingleLine(false);
			editText.setHorizontallyScrolling(false);
		}
		editText.setFilters(filters);
		editText.setSelection(text.length());
		editText.postDelayed(new Runnable() {
			@Override
			public void run() {
				editText.requestFocus();
				((InputMethodManager) editText.getContext().getSystemService(
						Context.INPUT_METHOD_SERVICE)).showSoftInput(editText,
						InputMethodManager.SHOW_FORCED);
			}
		}, 50);

		return this;
	}

	public MyDialog setHtml(String html) {
		TextView linkText = (TextView) (dialogView
				.findViewById(R.id.DialogContentHtml));
		linkText.setVisibility(View.VISIBLE);
		linkText.setText(Html.fromHtml(html));
		linkText.setMovementMethod(LinkMovementMethod.getInstance());
		return this;
	}

	public MyDialog setDateTimePicker(Date date, DateType type) {
		LinearLayout timeLayout = (LinearLayout) (dialogView
				.findViewById(R.id.DialogDateTimePicker));
		timeLayout.setVisibility(View.VISIBLE);

		int year = 0;
		int month = 0;
		int day = 0;

		Calendar calendar = Calendar.getInstance();
		int currentYear = calendar.get(Calendar.YEAR);

		if (date != null)
			calendar.setTime(date);

		year = calendar.get(Calendar.YEAR);
		month = calendar.get(Calendar.MONTH);
		if (type == DateType.YEAR_MONTH_DAY) {
			day = calendar.get(Calendar.DATE);
		}

		// 添加大小月月份并将其转换为list,方便之后的判断
		String[] months_big = { "1", "3", "5", "7", "8", "10", "12" };
		String[] months_little = { "4", "6", "9", "11" };

		final List<String> list_big = Arrays.asList(months_big);
		final List<String> list_little = Arrays.asList(months_little);

		// 年
		final WheelView wv_year = (WheelView) dialogView
				.findViewById(R.id.year);
		wv_year.setAdapter(new NumericWheelAdapter(START_YEAR, currentYear));// 设置"年"的显示数据
		wv_year.setCyclic(true);// 可循环滚动
		wv_year.setLabel("年");// 添加文字
		wv_year.setCurrentItem(year - START_YEAR);// 初始化时显示的数据

		// 月
		final WheelView wv_month = (WheelView) dialogView
				.findViewById(R.id.month);
		wv_month.setAdapter(new NumericWheelAdapter(1, 12));
		wv_month.setCyclic(true);
		wv_month.setLabel("月");
		wv_month.setCurrentItem(month);

		// 日
		final WheelView wv_day = (WheelView) dialogView.findViewById(R.id.day);
		wv_day.setCyclic(true);
		// 判断大小月及是否闰年,用来确定"日"的数据
		if (list_big.contains(String.valueOf(month + 1))) {
			wv_day.setAdapter(new NumericWheelAdapter(1, 31));
		} else if (list_little.contains(String.valueOf(month + 1))) {
			wv_day.setAdapter(new NumericWheelAdapter(1, 30));
		} else {
			// 闰年
			if ((year % 4 == 0 && year % 100 != 0) || year % 400 == 0)
				wv_day.setAdapter(new NumericWheelAdapter(1, 29));
			else
				wv_day.setAdapter(new NumericWheelAdapter(1, 28));
		}
		wv_day.setLabel("日");
		wv_day.setCurrentItem(day - 1);

		// 添加"年"监听
		OnWheelChangedListener wheelListener_year = new OnWheelChangedListener() {
			public void onChanged(WheelView wheel, int oldValue, int newValue) {
				int year_num = newValue + START_YEAR;
				// 判断大小月及是否闰年,用来确定"日"的数据
				if (list_big
						.contains(String.valueOf(wv_month.getCurrentItem() + 1))) {
					wv_day.setAdapter(new NumericWheelAdapter(1, 31));
				} else if (list_little.contains(String.valueOf(wv_month
						.getCurrentItem() + 1))) {
					wv_day.setAdapter(new NumericWheelAdapter(1, 30));
				} else {
					if ((year_num % 4 == 0 && year_num % 100 != 0)
							|| year_num % 400 == 0)
						wv_day.setAdapter(new NumericWheelAdapter(1, 29));
					else
						wv_day.setAdapter(new NumericWheelAdapter(1, 28));
				}
			}
		};
		// 添加"月"监听
		OnWheelChangedListener wheelListener_month = new OnWheelChangedListener() {
			public void onChanged(WheelView wheel, int oldValue, int newValue) {
				int month_num = newValue + 1;
				// 判断大小月及是否闰年,用来确定"日"的数据
				if (list_big.contains(String.valueOf(month_num))) {
					wv_day.setAdapter(new NumericWheelAdapter(1, 31));
				} else if (list_little.contains(String.valueOf(month_num))) {
					wv_day.setAdapter(new NumericWheelAdapter(1, 30));
				} else {
					if (((wv_year.getCurrentItem() + START_YEAR) % 4 == 0 && (wv_year
							.getCurrentItem() + START_YEAR) % 100 != 0)
							|| (wv_year.getCurrentItem() + START_YEAR) % 400 == 0)
						wv_day.setAdapter(new NumericWheelAdapter(1, 29));
					else
						wv_day.setAdapter(new NumericWheelAdapter(1, 28));
				}
			}
		};
		wv_year.addChangingListener(wheelListener_year);
		wv_month.addChangingListener(wheelListener_month);

		// 显示的是 年月日 还是 年月
		if (type == DateType.YEAR_MONTH_DAY) {
			wv_day.setVisibility(View.VISIBLE);
		} else {
			wv_day.setVisibility(View.GONE);
		}

		// 根据屏幕密度来指定选择器字体的大小
		int textSize = 0;

		textSize = 50;

		wv_day.TEXT_SIZE = textSize;
		wv_month.TEXT_SIZE = textSize;
		wv_year.TEXT_SIZE = textSize;

		return this;
	}

	public MyDialog setTimePicker(Date date) {
		int hour = 0;
		int minutes = 0;

		Calendar calendar = Calendar.getInstance();

		if (date != null)
			calendar.setTime(date);

		hour = calendar.get(Calendar.HOUR_OF_DAY);
		minutes = calendar.get(Calendar.MINUTE);

		// 时
		final WheelView wv_hour = (WheelView) dialogView
				.findViewById(R.id.hour);
		wv_hour.setAdapter(new NumericWheelAdapter(0, 23));
		wv_hour.setCyclic(true);      // 可循环滚动
		wv_hour.setLabel("时");       // 添加文字
		wv_hour.setCurrentItem(hour);// 初始化时显示的数据

		// 分
		final WheelView wv_minutes = (WheelView) dialogView
				.findViewById(R.id.minute);
		wv_minutes.setAdapter(new NumericWheelAdapter(0, 59));
		wv_minutes.setCyclic(true);
		wv_minutes.setLabel("分");
		wv_minutes.setCurrentItem(minutes);

		// 根据屏幕密度来指定选择器字体的大小
		int textSize = 50;

		wv_hour.TEXT_SIZE = textSize;
		wv_minutes.TEXT_SIZE = textSize;

		return this;
	}

	public MyDialog setTwoWheel(String oneText, int oneMin, int oneMax, int oneTimes, int oneDefault, String oneUnit,
			                    String twoText, int twoMin, int twoMax, int twoTimes, int twoDefault, String towUnit) {
		LinearLayout twoWheelLayout = (LinearLayout) (dialogView.findViewById(R.id.DialogTwoWheel));
		twoWheelLayout.setVisibility(View.VISIBLE);
		TextView oneTextView = (TextView) dialogView.findViewById(R.id.twoWheelText1);
		TextView twoTextView = (TextView) dialogView.findViewById(R.id.twoWheelText2);
        oneTextView.setText(oneText);
        twoTextView.setText(twoText);
        
		final WheelView wv_hour = (WheelView) dialogView
				.findViewById(R.id.oneWheel);
		wv_hour.setAdapter(new NumericWheelAdapter(oneMin, oneMax ,oneTimes));
		wv_hour.setCyclic(true);// 可循环滚动
		wv_hour.setLabel(oneUnit);// 添加文字
		wv_hour.setCurrentItem(oneDefault);// 初始化时显示的数据

		final WheelView wv_minutes = (WheelView) dialogView
				.findViewById(R.id.twoWheel);
		wv_minutes.setAdapter(new NumericWheelAdapter(twoMin, twoMax, twoTimes));
		wv_minutes.setCyclic(true);
		wv_minutes.setLabel(towUnit);
		wv_minutes.setCurrentItem(twoDefault);

		// 根据屏幕密度来指定选择器字体的大小
		int textSize = 50;

		wv_hour.TEXT_SIZE = textSize;
		wv_minutes.TEXT_SIZE = textSize;

		return this;
	}
	
	public MyDialog setSimpleSelectPickerData(int oneMin, int oneMax, int oneTimes, int oneDefault, String oneUnit) {
		LinearLayout timeLayout = (LinearLayout) (dialogView
				.findViewById(R.id.DialogSimpleSelectPicker));
		timeLayout.setVisibility(View.VISIBLE);

		final WheelView wv_content = (WheelView) dialogView
				.findViewById(R.id.simpleSelectPicker);
		wv_content.setAdapter(new NumericWheelAdapter(oneMin, oneMax ,oneTimes));
		wv_content.setCyclic(true);// 可循环滚动
		wv_content.setLabel(oneUnit);// 添加文字
		wv_content.setCurrentItem(oneDefault);// 初始化时显示的数据

		wv_content.TEXT_SIZE = 50;

		return this;
	}
	public MyDialog setSimpleSelectPicker(int currentSelectIndex, int minRange,
			int maxRange, String rightText) {
		LinearLayout timeLayout = (LinearLayout) (dialogView
				.findViewById(R.id.DialogSimpleSelectPicker));
		timeLayout.setVisibility(View.VISIBLE);

		final WheelView wv_content = (WheelView) dialogView
				.findViewById(R.id.simpleSelectPicker);
		wv_content.setAdapter(new NumericWheelAdapter(minRange, maxRange));
		wv_content.setCyclic(true);// 可循环滚动
		wv_content.setLabel(rightText);// 添加文字
		wv_content.setCurrentItem(currentSelectIndex);// 初始化时显示的数据

		wv_content.TEXT_SIZE = 50;

		return this;
	}
	
	
	//double自增长
	public MyDialog setSimpleSelectPickerDouble(int currentSelectIndex, int minRange,
			int maxRange, String rightText, double times) {
		LinearLayout timeLayout = (LinearLayout) (dialogView
				.findViewById(R.id.DialogSimpleSelectPicker));
		timeLayout.setVisibility(View.VISIBLE);

		final WheelView wv_content = (WheelView) dialogView
				.findViewById(R.id.simpleSelectPicker);
		wv_content.setAdapter(new NumericWheelAdapter(minRange, maxRange, times));
		wv_content.setCyclic(true);// 可循环滚动
		wv_content.setLabel(rightText);// 添加文字
		wv_content.setCurrentItem(currentSelectIndex);// 初始化时显示的数据

		wv_content.TEXT_SIZE = 50;

		return this;
	}
	
	public MyDialog setArraySelectPicker(ArrayList<String> valueArray) {
		LinearLayout timeLayout = (LinearLayout) (dialogView
				.findViewById(R.id.DialogSimpleSelectPicker));
		timeLayout.setVisibility(View.VISIBLE);

		final WheelView wv_content = (WheelView) dialogView
				.findViewById(R.id.simpleSelectPicker);
		wv_content.setAdapter(new ArrayWheelAdapter(valueArray));
		wv_content.setCyclic(false);// 可循环滚动
		wv_content.setCurrentItem(0);// 初始化时显示的数据

		wv_content.TEXT_SIZE = 50;

		return this;
	}
	
	public MyDialog setProgressbar(){
		LinearLayout timeLayout = (LinearLayout) (dialogView.findViewById(R.id.DialogProgressbarLayout));
		timeLayout.setVisibility(View.VISIBLE);
		progressBar = (ProgressBar) (dialogView.findViewById(R.id.DialogProgressbar));
		progressText = (TextView) dialogView.findViewById(R.id.DialogProgress);
		return this;
	}
	
	public void setProgress(int progress){
		if(progressBar != null && progressText != null){
			progressBar.setProgress(progress);
			progressText.setText(progress+"%");
		}
	}

	public void dismiss() {
		if (mDialog != null)
			mDialog.dismiss();
	}
}

```





