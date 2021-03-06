## LCWeChat · iOS · OC
/// 
<p align="left">
![enter image description here]
(https://img.shields.io/badge/iOS-chat-brightgreen.svg) 
![enter image description here]
(https://img.shields.io/badge/platform-iOS%208.0%2B-ff69b5618733984.svg) 
![enter image description here]
(https://img.shields.io/badge/license-MIT-green.svg?style=flat) 
</a>

在使用中有任何问题都可以提 issue, 欢迎加入QQ群:475814382

------------------------------------------------------------------------------------------------
### 聊天页面的实现难点则在于：

- 消息种类繁多，对各种消息类型需要进行不同的布局 界面的流畅以及异步处理方面有大量的开发工作；
- 推、拉 音视频图像等消息, socket编码等；
- 群组离线消息, 通知类型离线消息, 消息重复等等

### 有需要的小伙伴,可以参考思路

提供一些个人的思路与demo, 希望能帮到有需要的小伙伴

* 自动布局 计算高度 发送文本、富文本、复制粘贴、文本换行、发送图片(PhotoKit 图片选择器 图片浏览器)、发送语音(录制音频mp3格式 播放音频、音频录制与播放的封装、60秒自动发送等) 、发送定位、拍照
* 高仿最新QQ侧滑效果
* 高仿朋友圈,文本收缩, 下拉刷新
* 一些封装思想和设计模式

### 项目gif演示
![image](https://github.com/icoderRo/LCWeChat/blob/master/Resource/LCWeChat.gif)

### 部分工具用法

* [LCMqtt](https://github.com/icoderRo/LCMqtt) - an easy to use mqtt deal

```Objc
pod 'LCMqtt', '~> 1.0.0' 简单易用基于mqtt协议

```
```Objc
再比如:对于audio的使用

首先将文件夹拖进去

1.开始录音
// 检测麦克风是否可用
[LCAudioManager manager] checkMicrophoneAvailability]
[[LCAudioManager manager] startRecordingWithFileName:[NSString recordFileName] completion:nil];

2.结束录音
[[LCAudioManager manager] stopRecordingWithCompletion:^(NSString *recordPath, NSInteger aDuration, NSError *error) {
        if (aDuration < 1) { 
            [MBProgressHUD showError:@"录音时间过短"];
            return ;
        }
        if (!error) { // 录音成功
          // 执行下一步计划
        }
    }];
```

```Objc
例如使用录音指示器
老规矩 拖相关文件

1.设置录音按钮的状态
[recordButton addTarget:self action:@selector(startRecord:) forControlEvents:UIControlEventTouchDown];
[recordButton addTarget:self action:@selector(endRecord:) forControlEvents:UIControlEventTouchUpInside];
[recordButton addTarget:self action:@selector(dragExitRecord:) forControlEvents:UIControlEventTouchDragExit];
[recordButton addTarget:self action:@selector(cancelRecord:) forControlEvents:UIControlEventTouchUpOutside];
[recordButton addTarget:self action:@selector(dragEnterRecord:) forControlEvents:UIControlEventTouchDragEnter];

2. 监听
// 1.开始录音
- (void)startRecord:(UIButton *)recordButton 
{
    [LCRecordingView show];
    [LCRecordingView subTitleLabelStatues:subTitleStatuesDefault];
}
// 2.结束录音
- (void)endRecord:(UIButton *)recordButton
 {
    [LCRecordingView dismiss];
    [LCRecordingView subTitleLabelStatues:subTitleStatuesDefault];
}
// 3.取消录音
- (void)cancelRecord:(UIButton *)recordButton
{
    [LCRecordingView dismiss];
    [LCRecordingView subTitleLabelStatues:subTitleStatuesDefault];
}

//4. 离开按钮范围
- (void)dragExitRecord:(UIButton *)recordButton
{
     [LCRecordingView subTitleLabelStatues:subTitleStatuesCancel];
}

//5. 移动回按钮范围
- (void)dragEnterRecord:(UIButton *)recordButton
{
     [LCRecordingView subTitleLabelStatues:subTitleStatuesCancel];
}
```
### 可参考部分
```Objc
如: 基于netty LengthFieldBasedFrameDecoder(100000000,0,4,0,4) 
总长度 = 4byte + 包体内容
接收二进制的json字符串

设计:
1.采用面向协议的方式编码与解码
2.自定义编码器和解码器, 遵守协议, 实现协议中的方法
3.基于GCDAsyncSocket, 封装需要用到的方法

协议 - LCSocketCoderProtocol
1.编码协议:LCSocketEncoderProtocol
2.解码协议:LCSocketDecoderProtocol
3.编码完成后的输出协议:LCSocketEncoderOutputProtocol
4.解码完成后的输出协议:LCSocketDecoderOutputProtocol

解码器 - LCSocketEncoder
1. 遵守编码协议
2.实现协议中的方法:
- (void)encode:(id)object output (id<LCSocketEncoderOutputProtocol>)output
其中, 输出output遵守 编码输出协议

编码器 - LCSocketDecoder
1. 遵守解码协议
2.实现协议中的方法:
- (void)decode:(id)object output:(id<LCSocketDecoderOutputProtocol>)output
其中, 输出output遵守 解码输出协议

```

### 项目结构
```
├── weChat  
|   ├── session # 会话
│   ├── Discover  # 朋友圈代码
│   ├── Group # 讨论组列表
|   ├── Plus # 更多
|   ├── setting # 设置界面
│   └── Other # 大杂烩
│       ├── Controller
│       ├── view
|       ├── category
|       ├── tool
│       │   ├── audio
│       │   ├── socket
│       ├── Resources  # 资源文件，如图片、音频等
│       └── chat # 核心库
|            ├── Controller
|            ├── Model
|            └── View
|                ├── Photo # 图片浏览,选择
|                ├── Toolbar # 工具条
└──              └── chatCell # 核心布局库

```
 
### 布局代码结构
![image](https://github.com/icoderRo/LCWeChat/blob/master/Resource/mainController.png)

