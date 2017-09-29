
<center><h6 style="magrin:0px;text-align:left;font-size:12px;">
2017年9月
</center>

> 日志保存到本地内存卡目录

```
/* 
* @Title:  LogcatHelper.java 
* @Copyright:  XXX Co., Ltd. Copyright YYYY-YYYY,  All rights reserved 
* @Description:  TODO<请描述此文件是做什么的> 
* @author:  ZhuZiQiang 
* @data:  2014-4-15 下午7:22:14 
* @version:  V1.0 
*/

package com.yikang.heartmark.util;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;

import android.content.Context;
import android.os.Environment;

/** 
 * TODO<log日志统计保存> 
 * @author  ZhuZiQiang 
 * @data:  2014-4-15 下午7:22:14 
 * @version:  V1.0 
 */

public class LogcatHelper {
	private static LogcatHelper INSTANCE = null;  
    private static String PATH_LOGCAT;  
    private LogDumper mLogDumper = null;  
    private int mPId;  
  
    /** 
     *  
     * 初始化目录 
     *  
     * */  
    public void init(Context context) {  
        if (Environment.getExternalStorageState().equals(  
                Environment.MEDIA_MOUNTED)) {// 优先保存到SD卡中  
            PATH_LOGCAT = Environment.getExternalStorageDirectory()  
                    .getAbsolutePath() + File.separator + "HeartMarkLog";  
        } else {// 如果SD卡不存在，就保存到本应用的目录下  
            PATH_LOGCAT = context.getFilesDir().getAbsolutePath()  
                    + File.separator + "HeartMarkLog";  
        }  
        File file = new File(PATH_LOGCAT);  
        if (!file.exists()) {  
            file.mkdirs();  
        }  
    }  
  
    public static LogcatHelper getInstance(Context context) {  
        if (INSTANCE == null) {  
            INSTANCE = new LogcatHelper(context);  
        }  
        return INSTANCE;  
    }  
  
    private LogcatHelper(Context context) {  
        init(context);  
        mPId = android.os.Process.myPid();  
    }  
  
    public void start() {  
        if (mLogDumper == null)  
            mLogDumper = new LogDumper(String.valueOf(mPId), PATH_LOGCAT);  
        mLogDumper.start();  
    }  
  
    public void stop() {  
        if (mLogDumper != null) {  
            mLogDumper.stopLogs();  
            mLogDumper = null;  
        }  
    }  
  
    private class LogDumper extends Thread {  
  
        private Process logcatProc;  
        private BufferedReader mReader = null;  
        private boolean mRunning = true;  
        String cmds = null;  
        private String mPID;  
        private FileOutputStream out = null;  
  
        public LogDumper(String pid, String dir) {  
            mPID = pid;  
            try {  
                out = new FileOutputStream(new File(dir, "heartMarkLog-"  
                        + DateUtil.getNowDate("yyyy-MM-dd") + ".txt" +
                        		""));  
            } catch (FileNotFoundException e) {  
                // TODO Auto-generated catch block  
                e.printStackTrace();  
            }  
  
            /** 
             *  
             * 日志等级：*:v , *:d , *:w , *:e , *:f , *:s 
             *  
             * 显示当前mPID程序的 E和W等级的日志. 
             *  
             * */  
  
            // cmds = "logcat *:e *:w | grep \"(" + mPID + ")\"";  
            // cmds = "logcat  | grep \"(" + mPID + ")\"";//打印所有日志信息  
            // cmds = "logcat -s way";//打印标签过滤信息  
            cmds = "logcat *:e *:i | grep \"(" + mPID + ")\"";  
  
        }  
  
        public void stopLogs() {  
            mRunning = false;  
        }  
  
        @Override  
        public void run() {  
            try {  
                logcatProc = Runtime.getRuntime().exec(cmds);  
                mReader = new BufferedReader(new InputStreamReader(  
                        logcatProc.getInputStream()), 1024);  
                String line = null;  
                while (mRunning && (line = mReader.readLine()) != null) {  
                    if (!mRunning) {  
                        break;  
                    }  
                    if (line.length() == 0) {  
                        continue;  
                    }  
                    if (out != null && line.contains(mPID)) {  
                        out.write((DateUtil.getNowDate("yyyy-MM-dd HH:mm:ss") + "  " + line + "\n")  
                                .getBytes());  
                    }  
                }  
  
            } catch (IOException e) {  
                e.printStackTrace();  
            } finally {  
                if (logcatProc != null) {  
                    logcatProc.destroy();  
                    logcatProc = null;  
                }  
                if (mReader != null) {  
                    try {  
                        mReader.close();  
                        mReader = null;  
                    } catch (IOException e) {  
                        e.printStackTrace();  
                    }  
                }  
                if (out != null) {  
                    try {  
                        out.close();  
                    } catch (IOException e) {  
                        e.printStackTrace();  
                    }  
                    out = null;  
                }  
  
            }  
  
        }  
  
    }  
}

```
