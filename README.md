#这不能算完整意义上的插件

首先使用命令行进入自己的项目目录

使用命令行添加插件

```
cordova plugin add https://github.com/zwlcoding/pg_startvideo
```

视频有 3M 可能下载较慢。请耐心等待。

进入ADT刷新资源

找到src下的主文件 （文件名非固定，与你设置的包名相关，比如我这个app的包名为[com.mtel.xxx] ,那么找到 src => com.mtel.xxx => xxx.java 文件）


##注意中文字
```
package 你的包名;

import android.os.Bundle;//如存在无需重复添加
import android.os.Handler;//如存在无需重复添加
import android.view.View;//如存在无需重复添加
import android.content.Context;
import android.content.Intent;
import android.media.MediaPlayer;
import android.net.Uri;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View.OnClickListener;
import android.view.ViewGroup;
import android.view.Window;
import android.webkit.WebView;
import android.widget.MediaController;
import android.widget.ProgressBar;
import android.widget.VideoView;

import org.apache.cordova.*;

//下面这个文件名  如果你的包名为  com.mtel.videos  那么文件名就是 videos
public class 你的文件名 extends CordovaActivity implements OnClickListener 
{
	View rowView = null;
	VideoView videoView;
	Handler mHandler;
	ProgressBar progressBar1;
	
    @Override
    public void onCreate(Bundle savedInstanceState)
    {
		
    	this.requestWindowFeature(Window.FEATURE_NO_TITLE);
		
        super.onCreate(savedInstanceState);//默认存在
        this.init();//默认存在，但这里把 super 改成了 this
			
			
        this.appView.setWebViewClient(new CordovaWebViewClient(this, this.appView) {
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, String url) {
                if(url.contains("shoulduseexternalbrowser:")) {
                     url = url.replace("shoulduseexternalbrowser:", "");
                     Intent browserIntent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
                     startActivity(browserIntent);
                     return true;
                } else {
                 return super.shouldOverrideUrlLoading(view, url);
                }
            }
        });
        
		
        super.loadUrl(Config.getStartUrl());//默认存在

		
        LayoutInflater inflater = (LayoutInflater) getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        rowView = inflater.inflate(R.layout.video, null);
        this.getWindow().addContentView(rowView,  new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT,
        		ViewGroup.LayoutParams.MATCH_PARENT));
	    
	    findViewById(R.id.ui_btnSkip).setOnClickListener(this);
	    progressBar1 = (ProgressBar) rowView.findViewById(R.id.progressBar1);
	    videoView = (VideoView)this.findViewById(R.id.videoView);
	    MediaController mc = new MediaController(this);
	    mc.setAnchorView(videoView);
	    videoView.setMediaController(null);
	    videoView.setOnCompletionListener(new MediaPlayer.OnCompletionListener() {
	        @Override
	        public void onCompletion(MediaPlayer vmp) {
	        	Log.i("m2", "m2 onCompletion");
	        	rowView.setVisibility(View.GONE);
	        }
	    });  

	    String path = "android.resource://" + getPackageName() + "/" + R.raw.start;//startapp为视频的名称可以自行进入res/raw目录修改
	    videoView.setVideoURI(Uri.parse(path));	
	    videoView.requestFocus();
	    videoView.start();
    }
	
	@Override
	protected void onResume() {
		// TODO Auto-generated method stub
		super.onResume();
	}
	
	//如果点击了跳过按钮，那么使用一个加载动画，2秒之后进入页面
	@Override
	public void onClick(View v) {
		// TODO Auto-generated method stub
		switch (v.getId()) {
			case R.id.ui_btnSkip:{
				videoView.setVisibility(View.GONE);
			    mHandler = new Handler();
			    mHandler.postDelayed(new Runnable(){
					@Override
					public void run() {
						rowView.setVisibility(View.GONE);
					}
			    }, 2000);//这里修改N秒之后进入页面
				break;
			}
		}
	}
}

```

顺便上一个可用的视频格式。我使用的转换软件为【格式工厂】
```
转换格式	【mov】

转换参数
类型：	MOV
使用系统解码器：	关闭

视频流
	视频编码：	AVC(H264)
	屏幕大小：	400x800
	比特率：		缺省
	每秒帧数：	24
	宽高比：		完全伸展
	二次编码：	否

音频流
	音视频编码：	AAC
	采样率：		44100
	比特率：		128
	音频声道：	2
	关闭音效：	否
	音量控制：	0 dB
	音频流索引：	缺省

其他默认即可
```