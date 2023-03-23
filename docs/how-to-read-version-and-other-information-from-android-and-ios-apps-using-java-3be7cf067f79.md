# 如何使用 Java 从 Android 和 iOS 应用程序中读取版本和其他信息

> 原文：<https://betterprogramming.pub/how-to-read-version-and-other-information-from-android-and-ios-apps-using-java-3be7cf067f79>

## 从 APK、IPA 和应用程序包中轻松检索版本信息

![](img/537216ecbfdf22f1956d01c45b81f88f.png)

[图片来源:Maurizio Pesce/flickr](https://www.flickr.com/photos/pestoverde/16324871102/)

很多时候，尤其是在对移动应用程序运行自动化测试时，验证应用程序的正确版本是非常重要的。本文描述了使用命令行工具或 Java 库从 Android 和 iOS 应用程序包中查询版本信息的简单方法。这两种方法都可以很容易地集成到构建管道中。

## 命令行方式

对于 Android，从 APK 文件中解析元信息的一种方法是像这样使用`aapt` :

```
aapt dump badging /path/test.apk
```

这将打印大量信息，因此我们需要将输出调整为我们感兴趣的内容:

```
aapt dump badging test.apk | grep -o 'versionName=[^,]*' | cut -d'=' -f 2 | cut -d ' ' -f 1 | tr -d "'"
```

类似地，我们也可以查询版本代码(或任何其他相关信息):

```
aapt dump badging /path/test.apk | grep -o ‘versionCode=[^,]*’ | cut -d’=’ -f 2 | cut -d ‘ ‘ -f 1 | tr -d "'"
```

如果我们将应用程序安装在通过`adb`连接的设备(物理或模拟器)上，我们也可以像这样查询版本:

```
adb shell dumpsys package package.of.the.app | grep versionName | cut -d'=' -f 2 | cut -d ' ' -f 1adb shell dumpsys package package.of.the.app | grep versionCode | cut -d'=' -f 2 | cut -d ' ' -f 1
```

对于 iOS 应用，我们先考虑应用包(用于模拟器)。如果我们在 Mac OSX 上，我们可以使用`defaults`。请注意，路径必须是绝对的:

```
defaults read /path/test.app/Info CFBundleShortVersionString
defaults read /path/test.app/Info CFBundleVersion
```

在 Linux 上，我们需要`plistutil`和像`xmllint`这样的 XML 解析器:

```
plistutil -i /path/test.app/Info | xmllint --xpath "//key[text()='CFBundleShortVersionString']/following-sibling::string[1]/text()" -
plistutil -i /path/test.app/Info | xmllint --xpath "//key[text()='CFBundleVersion']/following-sibling::string[1]/text()"
```

如果我们想从一个 IPA 文件(用于物理设备)中检索版本信息，我们必须首先解包它。然后，我们可以运行与上面相同的命令:

```
# Unpack IPA file
unzip -q /path/test.ipa -d /tmp/ipa# Mac OS
defaults read /tmp/ipa/Payload/test.app/Info CFBundleShortVersionString
defaults read /tmp/ipa/Payload/test.app/Info CFBundleVersion# Linux
plistutil -i /tmp/ipa/Payload/test.app/Info | xmllint --xpath "//key[text()='CFBundleShortVersionString']/following-sibling::string[1]/text()" -
plistutil -i /tmp/ipa/Payload/test.app/Info | xmllint --xpath "//key[text()='CFBundleVersion']/following-sibling::string[1]/text()"# Clean-up
rm -rf /tmp/ipa
```

## Java 之路

从 Java 程序中检索版本信息的一种方法是使用`Runtime`或`ProcessBuilder`来执行上面的 shell 命令。然而，这是依赖于平台的，有点脆弱。它要求使用的工具(`aapt`、`plistutil`、`xmllint`等)。)可在运行 Java 代码的机器上获得。

因此，本地 Java 解决方案是更可取的。幸运的是，有满足我们所有需求的开源库:

*   apk 解析器:这个库允许我们从 APK 文件中读取信息。
*   [dd-plist](https://github.com/3breadt/dd-plist) :这个库可以处理用于存储 iOS 应用元信息的`plist` 文件。
*   [zip4j](http://www.lingala.net/zip4j/) :这个库是 Java 中处理 zip 文件的众多选项之一。

使用 Maven，我们可以将它们包含在我们的项目中:

```
<properties>
  <dd.plist.version>1.21</dd.plist.version>
  <apk.parser.version>2.6.4</apk.parser.version>
  <zip4j.version>1.3.2</zip4j.version>
</properties><dependencies>
  <dependency>
    <groupId>com.googlecode.plist</groupId>
    <artifactId>dd-plist</artifactId>
    <version>${dd.plist.version}</version>
  </dependency>
  <dependency>
    <groupId>net.dongliu</groupId>
    <artifactId>apk-parser</artifactId>
    <version>${apk.parser.version}</version>
  </dependency>
  <dependency>
    <groupId>net.lingala.zip4j</groupId>
    <artifactId>zip4j</artifactId>
    <version>${zip4j.version}</version>
  </dependency>
</depdencies>
```

对于 Android，我们可以像这样简单地获取版本信息:

```
String appPath = "/path/test.apk";
ApkFile apkFile = **new** ApkFile(**new** File(appPath));
ApkMeta apkMeta = apkFile.getApkMeta();
String versionName = apkMeta.getVersionName();
String versionCode = apkMeta.getVersionCode().toString();
```

对于 iOS，应用程序包的代码如下所示:

```
String appPath = "/path/test.app";
NSDictionary dictionary = (NSDictionary) PropertyListParser.*parse*(path);
String versionName = dictionary.objectForKey(“CFBundleShortVersionString”).toString();
String versionCode = dictionary.objectForKey(“CFBundleVersion”).toString();
```

如果我们处理的是 IPA 文件:

```
String appPath = "/path/test.ipa";# Unpack IPA file
String tmpFolder = File.***separator*** + "tmp" + File.***separator*** + UUID.*randomUUID*().toString();
ZipFile zipFile = **new** ZipFile(appPath);
zipFile.extractAll(tmpFolder);# Fetch version information
NSDictionary dictionary = (NSDictionary) PropertyListParser.*parse*(**new** File(**new** File(tmpFolder + File.***separator*** + "Payload").listFiles()[0].getAbsolutePath() + File.***separator*** + "Info.plist"));String versionName = dictionary.objectForKey(“CFBundleShortVersionString”).toString();
String versionCode = dictionary.objectForKey(“CFBundleVersion”).toString();# Clean-up
Files.*walk*(Paths.*get*(tmpFolder)).sorted(Comparator.*reverseOrder*()).map(Path::toFile).forEach(File::delete);
```

为了简单起见，上面的例子中已经去掉了异常处理。

完整的代码可以在这里[找到。](https://github.com/martinschneider/justtestlah/blob/master/justtestlah-mobile-tools/src/main/java/io/github/martinschneider/justtestlah/mobile/tools/ApplicationInfoService.java)

## 多功能图书馆

上面的演示是测试框架的一部分。您可以使用`mobile-tools` JAR(附带了最少的附加依赖)来查询 Android 和 iOS 应用程序的版本和其他元信息。按如下方式导入它:

```
<properties>
  <justtestlah.version>1.3.2</justtestlah.version>
</properties><dependencies>
  <dependency>
    <groupId>io.github.martinschneider</groupId>
    <artifactId>justtestlah-mobile-tools</artifactId>
    <version>${justtestlah.version}</version>
  </dependency>
</depdencies>
```

然后，简单地像这样使用它:

```
ApplicationInfo appInfo = **new** ApplicationInfoService().getAppInfo(appPath);String versionName = appInfo.getVersionName();
String versionCode = appInfo.getVersionCode();
String applicationName = appInfo.getApplicationName();
```

`appPath`可以指向任何 APK、IPA 或 APP 文件。