# 快速生成apk渠道包, 1分钟可生成1000个渠道包
##原理:
把一个Android应用包当作zip文件包进行解压，在签名生成的目录下(META-INF)添加一个空文件不需要重新签名。利用这个机制，该文件的文件名就可以是渠道名。

`MultiChannelBuildTool.py` 用于python代码用来给apk添加空的渠道文件，渠道名的前缀为ygs，python代码是3.0以上

```
zipped = zipfile.ZipFile(target_apk, 'a', zipfile.ZIP_DEFLATED)
# 初始化渠道信息
empty_channel_file = "META-INF/ygschannel_{channel}".format(channel = target_channel)
# 写入渠道信息
zipped.write(src_empty_file, empty_channel_file)
# 关闭zip流
zipped.close()
```
`ChannelUtil.java` 用于获取渠道号



###注意:
暂不支持Signature Scheme v2签名模式, 
在Android 7.0（Nougat）推出了新的应用签名方案APK Signature Scheme v2, 它是一个对全文件进行签名的方案，能提供更快的应用安装时间、对未授权APK文件的更改提供更多保护.
目前该方案不是强制性的，在 build.gradle 添加 v2SigningEnabled false ，就能使用传统签名方案来签署我们的应用（见下面的代码片段）。

```
android {
...
defaultConfig { ... }
signingConfigs {
 release {
   storeFile file("myreleasekey.keystore")
   storePassword "password"
   keyAlias "MyReleaseKey"
   keyPassword "password"
   v2SigningEnabled false
 }
}
}
```

