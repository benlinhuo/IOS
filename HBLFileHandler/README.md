###IOS文件操作

一、ios文件操作的原理

因为IOS是沙盒机制，也就是说ios中的应用是只可以访问自己应用目录下的文件，它没有android中得SD卡得概念，所以是不可以直接访问图像、视频等内容的。所以针对IOS自身应用产生的视频图像等都是放在自己的沙盒内的。一般来说，每个应用程序之间是不可以相互访问的。
  
默认情况下，每个沙盒含有3个文件夹：Documents,Library，tmp。其中Library包含Caches、Preferences目录。一个应用使用模拟器的这些文件的完整路径为：用户->资源库->Application Support->iPhone Simulator -> 6.1 ->Applications（具体可通过代码获取，如：/Users/benlinhuo/Library/Developer/CoreSimulator/Devices/1E83C4FA-A0F5-4C4F-A964-A8E9ACEFDD46/data/Containers/Data/Application/D1855082-D57C-4758-A11D-21A2AF30F957
）。

Documents：苹果建议将程序创建产生的文件以及应用浏览产生的文件数据保存在该目录下，iTunes备份和恢复的时候会包括此目录

Library：存储程序的默认设置或其他状态信息

Library/Caches：存放缓存文件，保存应用的持久化数据，用于应用升级或者应用关闭后的数据保存，不会被itunes同步，所以为了减少同步的时间，可以考虑将一些比较大的文件而又不需要备份的文件放到这个目录下。

tmp：提供一个即时创建临时文件的地方，但不需要持久化，在应用关闭后，该目录下的数据将删除，也可能系统在程序不运行的时候清除。

二、具体操作文件的代码如下：
```javascript
获取程序的根目录（home）目录

NSString *homePath = NSHomeDirectory()

获取Document目录

NSArray  *paths = NSSearchPathDorDirectoriesInDomains(NSDocumentDicrectory,, NSUserDomainMark, YES);                                                                           NSString *docPath = [paths lastObject];

获取Library目录

NSArray *paths = NSSearchPathForDirectoriseInDomains(NSLibraryDirectory, NSUserDomainMask, YES);                                                                                   NSString *docPath = [paths lastObject];   

获取Library中的Cache

NSArray *paths = NSSearchPathForDirectoriseInDomains(NSCachesDirectory, NSUserDomainMask, YES);                                                                                   NSString *docPath = [paths lastObject];

获取temp路径

NSString *temp = NSTemporaryDirectory( );
```

三、NSString类路径的处理方法
```javascript
文件路径的处理

NSString *path = @"/Uesrs/apple/testfile.txt"

常用方法如下

获得组成此路径的各个组成部分，结果：（"/","User","apple","testfile.txt"）

- (NSArray *)pathComponents;

提取路径的最后一个组成部分，结果：testfile.txt

- (NSString *)lastPathComponent;

删除路径的最后一个组成部分，结果：/Users/apple

- (NSString *)stringByDeletingLastPathCpmponent;

将path添加到先邮路径的末尾，结果：/Users/apple/testfile.txt/app.txt

- (NSString *)stringByAppendingPathConmponent:(NSString *)str;

去路径最后部分的扩展名，结果：text

- (NSString *)pathExtension;

删除路径最后部分的扩展名，结果：/Users/apple/testfile

- (NSString *)stringByDeletingPathExtension;

路径最后部分追加扩展名，结果：/User/apple/testfile.txt.jpg

- (NSString *)stringByAppendingPathExtension:(NSString *)str;
```

四、NSData

NSData是用来包装数据的。NSData存储的时二进制数据，屏蔽了数据之间的差异，文本、音频、图像等数据都可以用NSData来存储。

NSData用法：
```javascript
1.NSString与NSData互相转换

NSData－> NSString                                                                                     NSString *aString = [[NSString alloc] initWithData:adataencoding:NSUTF8StringEncoding];

NSString－>NSData                                                                                      NSString *aString = @"1234abcd";
NSData *aData = [aString dataUsingEncoding: NSUTF8StringEncoding]; 

将data类型的数据,转成UTF8的数据

+(NSString *)dataToUTF8String:(NSData *)data
{
NSString *buf = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
return [buf autorelease];
}

将string转换为指定编码 
+(NSString *)changeDataToEncodinString:(NSData *)data encodin:(NSStringEncoding )encodin{
    NSString *buf = [[[NSString alloc] initWithData:data encoding:encodin] autorelease];
    return buf;
}

2. NSData 与 UIImage
NSData－>UIImage
UIImage *aimage = [UIImage imageWithData: imageData];
 
//例：从本地文件沙盒中取图片并转换为NSData
NSString *path = [[NSBundle mainBundle] bundlePath];
NSString *name = [NSString stringWithFormat:@"ceshi.png"];
NSString *finalPath = [path stringByAppendingPathComponent:name];
NSData *imageData = [NSData dataWithContentsOfFile: finalPath];
UIImage *aimage = [UIImage imageWithData: imageData];

3.NSData与NSArray  NSDictionary

+(NSString *)getLocalFilePath:(NSString *) fileName
{
return [NSString stringWithFormat:@"%@/%@%@", NSHomeDirectory(),@“Documents”,fileName];
}
```

五、包括将NSData写进Documents目录
从Documents目录读取数据
在进行网络数据通信的时候，经常会遇到NSData类型的数据。在该数据是dictionary结构的情况下，系统没有提供现成的转换成NSDictionary的方法，为此可以通过Category对NSDictionary进行扩展，以支持从NSData到NSDictionary的转换。声明和实现如下：
```javascript
+ (NSDictionary *)dictionaryWithContentsOfData:(NSData *)data {     
    CFPropertyListRef list = CFPropertyListCreateFromXMLData(kCFAllocatorDefault, (CFDataRef)data, kCFPropertyListImmutable, NULL);
    if(list == nil) return nil; 
    if ([(id)list isKindOfClass:[NSDictionary class]]) { 
         return [(NSDictionary *)list autorelease]; 
        } 
    else { 
         CFRelease(list); 
         return nil; 
        } 
}
```

手动将网络传送过来的NSDictionary类型的json数据转成，BIFModel中（如Broker）的字段以及对应值【BIFModel可以理解成数据表，其中的数据，可以理解成表中的数据】
```javascript
BIFModel中得方法如下：
- (void)loadPropertiesWithData:(NSDictionary *)data
{
    if (![data isKindOfClass:[NSDictionary class]]) {
        CFLLog(@"load property error, data is not dictionary: %@", data);
        return;
    }
    
    for (NSString *key in [data keyEnumerator]) {
        if ([self respondsToSelector:NSSelectorFromString(key)]) {
            NSString *setter = [NSString stringWithFormat:@"set%@%@:",
                                [[key substringToIndex:1] uppercaseString],
                                [key substringFromIndex:1]];
            [self performSelector:NSSelectorFromString(setter)
                       withObject:data[key]];
        }
    }
}
使用：BIFModel *model = [[Broker alloc] init];
      [model loadPropertiesWithData:result];
      return model;//则返回的model就是Broker类型的数据了
```

