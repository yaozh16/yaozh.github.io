# Android 学习笔记(7)Camera2API代码逻辑整理

[PDF图示](../ATTACHMENT/Camera2Video.pdf)

## 大致流程
1. [获取CameraManager](#了解摄像头信息)类似系统级的管理对象，便于之后对多个摄像头分别处理
1. [申请权限](#申请权限)
1. [打开摄像头](#打开摄像头)
1. [构造配置PreviewBuilder](#构造配置PreviewBuilder)用于生成之后的请求(因为所有操作需要通过Request进行)
1. [构造配置mSessionPreviewStateCallback](#构造配置msessionpreviewstatecallback)
1. [调用mCameraDevice.createCaptureSession](#调用mcameradevicecreatecapturesession)

## 了解摄像头信息
```java
//在这里可以通过
mCameraManager=(CameraManager)getApplicationContext().getSystemService(Context.CAMERA_SERVICE);
mCameraManager.getCameraIdList()
for (String cameraId : mCameraManager.getCameraIdList())
		CameraCharacteristics characteristics = mCameraManager.getCameraCharacteristics(cameraId);    
//获取各个摄像头信息

//例如
Integer facing = characteristics.get(CameraCharacteristics.LENS_FACING);
```
## 申请权限

```java
if(ActivityCompat.checkSelfPermission(/*Activity*/this,Manifest.permission.CAMERA)!= PackageManager.PERMISSION_GRANTED){
		ActivityCompat.requestPermissions(/*Activity*/this,new String[] {Manifest.permission.CAMERA}, /*可以自己任取的requestCode，用于自己标识某一种类的申请*/1);
}
```

## 打开摄像头
通过CameraId与mCameraManager打开
```java
mCameraManager.openCamera(/*CameraId*/"0",mStateCallback,mHandler);
```
其中后两个参数表示此次打开操作的回调函数与处理打开操作的线程的handler,

其构造方面
### mHandler
```java
HandlerThread thread=new HandlerThread(/*name*/"Camera2");
thread.start();					//自动生成Looper
mHandler= new Handler(thread.getLooper());
```
### mStateCallback
```java
mStateCallback = new CameraDevice.StateCallback() {...}
```
其中自动生成必须要重写的函数会有
CameraDevice类对象cameraDevice，标识打开成功/失败的摄像头，如果是成功，保存下来这个引用之后就可以使用了

## 构造配置PreviewBuilder
由于所有的请求都需要经过Request形式发送，所以我们首先需要构造一个请求构造工厂，并按照我们的要求配置好这个工厂，
然后就可以调用它进行request的构造，发送给照相机
* 构造
```java
mPreviewBuilder = mCameraDevice.createCaptureRequest(CameraDevice.TEMPLATE_PREVIEW);
```
* 设置目标
```java
mPreviewBuilder.addTarget(mSurfaceView.getHolder().getSurface());
```
* 设置拍摄控制信息
```java
mPreviewBuilder.set(CaptureRequest.CONTROL_AF_MODE,CaptureRequest.CONTROL_AF_MODE_CONTINUOUS_PICTURE);
mPreviewBuilder.set(CaptureRequest.CONTROL_AE_MODE,CaptureRequest.CONTROL_AE_MODE_ON_AUTO_FLASH);
```
* 之后调用构造CaptureRequest类对象
```java
CaptureRequest request = mPreviewBuilder.build();
```

## 构造配置mSessionPreviewStateCallback
这个自己随意
## 调用mCameraDevice.createCaptureSession

所有的会话消息都在这个Session中使用
```java
mCameraDevice.createCaptureSession(
		Arrays.asList(mSurfaceView.getHolder().getSurface(),mImageReader.getSurface()), //Surface的列表
		mSessionPreviewStateCallback, //之前构造的Callback，成功的话在里面的onConfigued函数中会传入cameraCaptureSession对象，可以保存下来，之后操作
		mHandler);//处理这个操作的线程的handler
```
```java
//mSession是构造成功过后获得的CameraCaptureSession
//以下为预览
//等效于持续发送请求
mSession.setRepeatingRequest(mPreviewBuilder.build(), mSessionCaptureCallback, mHandler);
```
