
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 声音播放

```
package com.yikang.heartmark.util;

import android.content.Context;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.iflytek.cloud.ErrorCode;
import com.iflytek.cloud.InitListener;
import com.iflytek.cloud.SpeechConstant;
import com.iflytek.cloud.SpeechError;
import com.iflytek.cloud.SpeechSynthesizer;
import com.iflytek.cloud.SynthesizerListener;

public class PlaySoundUtil {
	private Context context;
	private SpeechSynthesizer mTts;
	private boolean isFinish = true;   //播完状态
	private boolean isPause = false;   //不在暂停状态

	public PlaySoundUtil(Context context) {
		this.context = context;
		// 初始化合成对象
		mTts = SpeechSynthesizer.createSynthesizer(context, mTtsInitListener);
	}

	// 初始化监听
	private InitListener mTtsInitListener = new InitListener() {
		@Override
		public void onInit(int code) {
			if (code != ErrorCode.SUCCESS) {
				//MyToast.show(context, "初始化失败,错误码：" + code, Toast.LENGTH_SHORT);
			}
		}
	};

	//播放文本
	public void playSound(String text, Button buttonYuYin) {
//如果是播放状态和不在暂停状态
		if(isFinish && !isPause){
			setParam();//参数设置
			int code = mTts.startSpeaking(text, mTtsListener);
			if (code != ErrorCode.SUCCESS) {
				MyToast.show(context, "播放失败,请重试", Toast.LENGTH_SHORT);
				buttonYuYin.setBackgroundResource(com.example.heartmark.R.drawable.yishi_chat_yuyin_no);
			}else{//如果返回码是成功
				isFinish = false;
				buttonYuYin.setBackgroundResource(com.example.heartmark.R.drawable.yishi_chat_yuyin_yes);
			}
		}else if(!isFinish && !isPause){//如果不是播放状态和不在暂停状态
			mTts.pauseSpeaking();
			isPause = true;
			buttonYuYin.setBackgroundResource(com.example.heartmark.R.drawable.yishi_chat_yuyin_no);
			
		}else if(!isFinish && isPause){//如果不是播放状态和在暂停状态
			mTts.resumeSpeaking();
			isPause = false;
			buttonYuYin.setBackgroundResource(com.example.heartmark.R.drawable.yishi_chat_yuyin_yes);
		}
	}
	
	public void stopSound(){
		mTts.stopSpeaking();
		// 退出时释放连接
		mTts.destroy();
		
		isPause = false;
		isFinish = true;
	}
	
	
	/**
	 * 合成回调监听。
	 */
	private SynthesizerListener mTtsListener = new SynthesizerListener() {
		@Override
		public void onSpeakBegin() {
		//	showTip("开始播放");
		}

		@Override
		public void onSpeakPaused() {
		//	showTip("暂停播放");
		}

		@Override
		public void onSpeakResumed() {
		//	showTip("继续播放");
		}

		@Override
		public void onBufferProgress(int percent, int beginPos, int endPos,
				String info) {
			//缓存进度
//			mPercentForBuffering = percent;
//			showTip(String.format(getString(R.string.tts_toast_format),
//					mPercentForBuffering, mPercentForPlaying));
		}

		@Override
		public void onSpeakProgress(int percent, int beginPos, int endPos) {
			//播放进度
//			mPercentForPlaying = percent;
//			showTip(String.format(getString(R.string.tts_toast_format),
//					mPercentForBuffering, mPercentForPlaying));
		}

		@Override
		public void onCompleted(SpeechError error) {
			if(error == null)
			{
			//	showTip("播放完成");
				isFinish = true;
			}
			else if(error != null)
			{
		        showTip(error.getPlainDescription(true));
			}
		}

		@Override
		public void onEvent(int eventType, int arg1, int arg2, Bundle obj) {
			
		}
	};

	private void showTip(String text){
		//MyToast.show(context, text, Toast.LENGTH_LONG);
	}
	

	//参数设置
	private void setParam() {
		// 清空参数
		mTts.setParameter(SpeechConstant.PARAMS, null);
		// 设置合成
		mTts.setParameter(SpeechConstant.ENGINE_TYPE, SpeechConstant.TYPE_CLOUD);
		// 设置发音人
		mTts.setParameter(SpeechConstant.VOICE_NAME, 
				"xiaoyan");

		// 设置语速
		mTts.setParameter(SpeechConstant.SPEED,"50");

		// 设置音调
		mTts.setParameter(SpeechConstant.PITCH,"50");

		// 设置音量
		mTts.setParameter(SpeechConstant.VOLUME,"50");

		// 设置播放器音频流类型
		mTts.setParameter(SpeechConstant.STREAM_TYPE,"3");

	}
}
```
