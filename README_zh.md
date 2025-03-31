[English Changelog](CHANGELOG.md) | [中文变更日志](CHANGELOG_zh.md)
[English](README.md) | [中文](README_zh.md) （以下内容根据原作者英文文档翻译，如有错误请提交PR或联系作者）

# ATC_TLSR_Paper

使用TLSR8359 ARM SOC的汉朔电子纸货架标签/价格标签的自定义BLE固件

<h1 style="color:red;font-size:25px;">请注意，此固件仅适用于带有TLSR微控制器的价格标签！请务必查看<a href="#compatible-hanshow-models">兼容型号列表</a>。</h1>

### 你可以通过PayPal支持我的工作：https://paypal.me/hoverboard1 这将使类似的项目得以持续进行。

由于可以在泰凌微TLSR8359上启用BLE，所以我决定制作一个自定义固件，而不是逆向工程原厂固件及其2.4Ghz RF协议。

本仓库与以下解释视频一起制作：（点击观看）

[![YoutubeVideo](https://img.youtube.com/vi/ANHz7EgWx7k/0.jpg)](https://www.youtube.com/watch?v=ANHz7EgWx7k)

WebSerial固件烧录工具： 
https://atc1441.github.io/ATC_TLSR_Paper_UART_Flasher.html

WebBluetooth图像上传器：
https://atc1441.github.io/ATC_TLSR_Paper_Image_Upload.html

WebBluetooth固件OTA烧录：
https://atc1441.github.io/ATC_TLSR_Paper_OTA_writing.html

#### 编译：
需要安装Python
##### Windows：
要在Windows下编译，请使用命令提示符导航到“Firmware”文件夹。

输入“makeit.exe”并等待编译完成。

##### Linux：
使用终端导航到“Firmware”文件夹。

输入“make”并等待编译完成。

#### 烧录：
使用WebSerial烧录器打开编译好的.bin固件，并将其写入闪存。

首次连接时，需要解锁TLSR8359的闪存。

#### 关于显示屏：
此电子货架标签中使用的电子墨水面板为250×122像素，黑白显示（目前尚无灰度）。

Larry Bank添加了他的OneBitDisplay（https://github.com/bitbank2/OneBitDisplay）和TIFF_G4（https://github.com/bitbank2/TIFF_G4）库，以便轻松生成文本和图形。对于任何想要直接写入显示缓冲区的人来说，内存布局类似于典型的1位位图，但顺时针旋转了90度。换句话说，显示屏实际上是122宽×250高，但横向放置。每个字节包含8个像素，最高有效位在左侧。黑色为0，白色为1。每行122像素使用16个字节。以下是一个示例函数，用于在显示屏使用的方向（纵向）上设置像素：<br>
<br>
```
void SetPixel(int x, int y, uint8_t *pDisplayBuffer)
{
   uint8_t *d = &pDisplayBuffer[(y >> 3) + (249-x)*16];
   *d &= ~(0x80 >> (y & 7)); // 将像素设置为黑色
}
```

目前支持以下硬件，
大多数显示屏会自动检测，如果检测失败，你可以在OTA烧录工具中选择正确的显示屏。
图形布局并未针对每个屏幕尺寸进行编辑，因此在所有屏幕上可能无法完美适配，尤其是1.54英寸版本。

## 兼容的汉朔型号：
汉朔[解释](https://fcc.report/FCC-ID/2AHB5-M3NT/4535921.pdf)了他们的命名方案如下：
> Stellar-XXX E31X  
> XXX和X代表硬件，例如Stellar-M3YN@ E31H
> - M = 2.13英寸
> - 3 = 三色显示：黑/白/红
> - Y = 三色显示：黑/白/黄
> - N = NFC芯片
> - H = 高分辨率
> - A = 无干簧管
> - @ = LED灯

此固件需要基于TLSR8359芯片的硬件，通常Stellar-xxNx型号是这种情况，但**不**包括没有N的型号。

以下型号已确认可以正常工作：

名称                       |显示屏                       |  外壳正面               |  外壳背面
:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:
Stellar-MFN@ E31A | 2.13" 212x104 |  ![](/Compatible_models/Stellar-MFN%40_Front.jpg)|  ![](/Compatible_models/Stellar-MFN%40_Back.jpg)
Stellar-M3N@ E31HA | 2.13" 250x122 | ![](/Compatible_models/Stellar-M3N%40_Front.jpg)|  ![](/Compatible_models/Stellar-M3N%40_Back.jpg)
Stellar-MN@ E31H | 2.13" 250x122 | ![](/Compatible_models/Stellar-MN%40_Front.jpg)|  ![](/Compatible_models/Stellar-MN%40_Back.jpg)
Stellar-S3TN@ E31HA | 1.54" 200x200 | ![](/Compatible_models/Stellar-S3TN%40_Front.jpg)|  ![](/Compatible_models/Stellar-S3TN%40_Back.jpg)
