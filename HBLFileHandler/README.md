###IOS文件操作

一、ios文件操作的原理

因为IOS是沙盒机制，也就是说ios中的应用是只可以访问自己应用目录下的文件，它没有android中得SD卡得概念，所以是不可以直接访问图像、视频等内容的。所以针对IOS自身应用产生的视频图像等都是放在自己的沙盒内的。
  
默认情况下，每个沙盒含有3个文件夹：Documents,Library，tmp。其中Library包含Caches、Preferences目录。一个应用使用模拟器的这些文件的完整路径为：用户->资源库->Application Support->iPhone Simulator -> 6.1 ->Applications（具体可通过代码获取，如：/Users/benlinhuo/Library/Developer/CoreSimulator/Devices/1E83C4FA-A0F5-4C4F-A964-A8E9ACEFDD46/data/Containers/Data/Application/D1855082-D57C-4758-A11D-21A2AF30F957
）。

Documents：苹果建议将程序创建产生的文件以及应用浏览产生的文件数据保存在该目录下，iTunes备份和恢复的时候会包括此目录

Library：存储程序的默认设置或其他状态信息

Library/Caches：存放缓存文件，保存应用的持久化数据，用于应用升级或者应用关闭后的数据保存，不会被itunes同步，所以为了减少同步的时间，可以考虑将一些比较大的文件而又不需要备份的文件放到这个目录下。

tmp：提供一个即时创建临时文件的地方，但不需要持久化，在应用关闭后，该目录下的数据将删除，也可能系统在程序不运行的时候清除。

二、具体操作文件的代码如下：
```javascript
//获取应用沙盒根路径
- (NSString *)dirHome
{
    NSString *dirHome = NSHomeDirectory();
    return dirHome;
}

```
