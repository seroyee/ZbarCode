# ZbarCode
用Zbar库实现的Android扫一扫
这是一个module,直接依赖到项目中即可.


 - **依赖**: 
Android studio项目,之间点击``File``->``New``->``Import Module`` 选择Demo文件导入即可.
记得在``App`` 的`` gradle``添加依赖. ``  compile project(':zbarcode')``  

![这里写图片描述](http://img.blog.csdn.net/20161127170654974)


###使用代码
 进入扫一扫
``` java
Intent intent1 = new Intent(MainActivity.this, CaptureActivity.class);
        startActivityForResult(intent1, QR_CODE);
```
获取返回的结果
``` java

@Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
 super.onActivityResult(requestCode, resultCode, data);
 if (requestCode == QR_CODE) {
            String result = data.getStringExtra(CaptureActivity.EXTRA_STRING);
            Toast.makeText(this, result + "", Toast.LENGTH_SHORT).show();
        }
    }
```


## 源码分析
###ZbarCode结构
![这里写图片描述](http://img.blog.csdn.net/20161127230028150)

>CaptureActivity:为扫一扫界面的Activity;
AutoFocusManager:相机自动聚焦管理类
CameraConfigurationManager:相机参数信息的操作类
CameraManager:为自定义相机操作的封装,管理相机的打开,取景,参数,回收处理.
PreviewCallback:图像数据回调处理;
PreviewCallback:主线程Handler;
DecodeThread:解码子线程;
DecodeHandler:子线程Handler;
BeepManager:扫码操作滴的声音处理;

### 流程:

扫一扫大致流程如下序列图：

```sequence

CaptureActivity->SurfaceView: 出始化SurfaceView
SurfaceView-->SurfaceView: 初始化完毕
CaptureActivity->CameraManager:打开摄像头
CameraManager-->CameraManager:判断是否可以开启
CameraManager-->CaptureActivity:相机已开启
CaptureActivity->CameraManager:传递SurfaceHoler开起预览
Note right of CameraManager:开启预览
Note right of CameraManager:获取摄像头参数
Note right of CameraManager:开启自动聚焦
Note right of CameraManager:Callback回调图像数据
Note right of CaptureActivity:开启子线程
CaptureActivity->Zbar库:传递相机参数和相机图像数据
Zbar库-->Zbar库:在子线程成解码
Zbar库-->CaptureActivity: 把解码状态还回
CaptureActivity-->CaptureActivity:成功,得到结果
CaptureActivity->CameraManager:失败,重新回调图像数据,进行解码

```


----------
#博客  www.znq123.cn