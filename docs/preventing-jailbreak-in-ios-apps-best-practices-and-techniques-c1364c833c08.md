# 防止 iOS 应用程序越狱:最佳实践和技术

> 原文：<https://betterprogramming.pub/preventing-jailbreak-in-ios-apps-best-practices-and-techniques-c1364c833c08>

## 保护您的应用和业务

![](img/43ac31cb07b880e0d1289bbba97ddbac.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[埃米利亚诺酒吧](https://unsplash.com/@emilianobar?utm_source=medium&utm_medium=referral)拍摄的照片

越狱是一个允许用户访问其 iOS 设备的根文件系统并安装未经授权的应用程序和修改的过程。这通常是通过利用操作系统中的漏洞并应用内核补丁来消除制造商强加的软件限制来实现的。

通过越狱他们的设备，用户可以访问根文件系统，这通常仅限于操作系统和授权的应用程序。这使得他们可以安装和运行未经批准的软件，无法通过应用商店，并修改操作系统及其行为。
这个过程类似于在 Android 设备中扎根，允许用户获得对各种 Android 子系统的特权控制或根访问。

# 越狱的好处

*   **定制**:越狱允许用户定制他们设备的外观和感觉，以及添加 App Store 不提供的新特性和功能。例如，网飞 iOS 应用程序的用户可能想要越狱他们的设备，以安装自定义主题或添加一个功能，允许他们下载并离线观看他们最喜欢的系列或电影，而无需订阅。
*   **访问替代应用商店/黑色应用商店**:越狱可以让用户访问替代应用商店，这些商店提供更全面的应用和游戏选择，包括那些由于苹果严格的指导方针而可能无法在应用商店上获得的应用和游戏。
*   **性能提升:**一些用户可能会越狱他们的设备以优化其性能，并删除制造商可能安装的任何不必要的膨胀软件。

# 越狱的潜在风险和弊端

*   **取消保修:**越狱 iOS 设备通常会取消设备的保修，这意味着如果设备出现问题，用户可能无法从制造商那里获得免费维修或支持。
*   **安全问题:**越狱会使设备暴露于非越狱设备中可能不存在的漏洞。例如，安装来自不可信来源的未经批准的应用程序或修改可能会增加恶意软件或其他安全威胁的风险。
*   **兼容性问题:**一些应用和功能可能无法在越狱设备上正常工作，因为它们可能依赖于不可用的或已被越狱修改的系统级功能。

# 开发人员可以采取的措施，以检测和防止其 iOS 应用程序中的越狱

**检查特定文件或目录的存在:**检测越狱的一种方法是检查越狱工具常用的特定文件或目录。例如，Swift 中的以下代码片段检查`/Applications/Cydia.app`目录是否存在，该目录通常出现在越狱设备上:

```
if FileManager.default.fileExists(atPath: "/Applications/Cydia.app") {
 // Device is jailbroken
} else {
 // Device is not jailbroken
}
```

`/Applications/Cydia.app`目录经常出现在越狱设备上，因为 Cydia 应用程序，一个流行的越狱设备包管理器，使用它。Cydia 允许用户浏览和安装 App Store 上没有的第三方应用和修改。

**使用苹果提供的 API:**苹果提供了很多 API，可以用来检测越狱。例如，`UIApplication`类的`canOpenURL()`函数可以用来检查设备是否可以打开一个已知被越狱工具使用的 URL。Swift 中的以下代码片段检查设备是否可以打开 Cydia URL，这是越狱工具经常使用的:

```
if UIApplication.shared.canOpenURL(URL(string: "cydia://package/com.example.package")!) {
    // Device is jailbroken
} else {
    // Device is not jailbroken
}
```

`canOpenURL()`功能作为一种越狱检测方法，因为越狱工具经常修改操作系统，以允许安装和执行未经授权的应用程序和修改。因此，越狱设备可能能够打开在非越狱设备上通常无法访问的 URL。通过检查设备是否能打开`cydia://package/com.example.package` URL，开发者可以推断设备已经越狱，可能修改了系统级函数。

**实现自定义检查:**开发者也可以实现自己的检查来检测越狱。例如，他们可以检查非越狱设备上通常不存在的特定系统库或框架。Swift 中的以下代码片段演示了如何检查`libsubstrate.dylib`库的存在，越狱工具经常使用该库:

```
let libraryPath = "/Library/MobileSubstrate/MobileSubstrate.dylib"
if FileManager.default.fileExists(atPath: libraryPath) {
    // Device is jailbroken
} else {
    // Device is not jailbroken
}
```

不仅仅是这种文件检查，还有许多文件和目录可供开发人员检查，以检测 iOS 设备中的越狱。这里有几个例子:

*   这个目录经常出现在越狱设备上，由 Cydia 应用程序使用，这是一个流行的越狱设备包管理器。
*   `/usr/libexec/ssh-keysign`:这个文件经常出现在越狱设备上，由 OpenSSH 包使用，OpenSSH 包通常由越狱工具安装。
*   `/usr/libexec/sftp-server`:这个文件经常出现在越狱设备上，由 OpenSSH 包使用，OpenSSH 包通常由越狱工具安装。
*   `/var/lib/apt`:这个目录经常出现在越狱设备上，由高级打包工具(APT)使用，APT 是越狱工具经常使用的一个包管理系统。
*   `/etc/apt`:这个目录经常出现在越狱设备上，由高级打包工具(APT)使用，APT 是一个越狱工具经常使用的包管理系统。

```
 static var suspiciousSystemPathsToCheck: [String] {
        return ["/Library/MobileSubstrate/DynamicLibraries/LiveClock.plist",
                "/Library/MobileSubstrate/DynamicLibraries/Veency.plist",
                "/private/var/lib/apt",
                "/private/var/lib/apt/",
                "/private/var/lib/cydia",
                "/private/var/mobile/Library/SBSettings/Themes",
                "/private/var/stash",
                "/private/var/tmp/cydia.log",
                "/System/Library/LaunchDaemons/com.ikey.bbot.plist",
                "/System/Library/LaunchDaemons/com.saurik.Cydia.Startup.plist",
                "/usr/bin/sshd",
                "/usr/libexec/sftp-server",
                "/usr/sbin/sshd",
                "/etc/apt",
                "/bin/bash",
                "/Library/MobileSubstrate/MobileSubstrate.dylib"
        ]
    }

    static func isSuspiciousSystemPathsExists() -> Bool {
        for path in suspiciousSystemPathsToCheck {
            if FileManager.default.fileExists(atPath: path) {
                return true
            }
        }
        return false
    }
```

值得注意的是，这些文件和目录可能不会出现在所有越狱设备上，熟练的黑客可能能够修改或隐藏它们，以绕过越狱检测措施。因此，有必要考虑这些检查的潜在风险和局限性，并将其作为更大的越狱检测策略的一部分来实施。

# 附加支票

```
 static var isSimulator: Bool {
        #if targetEnvironment(simulator)
            return true
        #else
            return false
        #endif
    }

    static var isOnMac: Bool {
        if #available(iOS 14.0, *), ProcessInfo.processInfo.isiOSAppOnMac {
            return true
        }
        return false
    }
```

*   `isSimulator`:该函数使用`targetEnvironment`编译器指令来检查应用程序是否正在为模拟器编译。这对于希望根据应用程序是在实际设备上运行还是在模拟器上运行来执行不同操作的开发人员来说非常有用。
*   `isOnMac`:该函数使用 iOS 14 及更高版本中可用的`ProcessInfo`类的`isiOSAppOnMac`属性来检查应用程序是否在 Mac 上运行。

# 结论

越狱 iOS 设备允许用户访问根文件系统，安装未经授权的应用程序和修改。尽管如此，它也带来了风险和缺点，如使设备的保修无效，并使其暴露于安全漏洞。开发人员可以在他们的应用程序中实施检测和防止越狱的措施，但需要注意的是，这些措施可能不是万无一失的。

值得注意的是，熟练的黑客可以绕过或击败越狱检测技术，因此必须考虑这些措施的潜在风险和局限性。正如《权力的游戏》中的山姆威尔·塔利所说，“对于一个天才的傻瓜来说，没有什么是万无一失的。”然而，实施越狱检测措施可以为您的应用程序和业务提供额外的安全性和保护。

[](https://github.com/Nikilicious09/Preventing-Jailbreak-in-iOS/blob/main/SecurityUtils.swift) [## 防止在 iOS 中越狱/security utils . swift at main nikilicious 09/防止在 iOS 中越狱

### 开发人员如何保护您的应用和业务免受越狱威胁的实用指南…

github.com](https://github.com/Nikilicious09/Preventing-Jailbreak-in-iOS/blob/main/SecurityUtils.swift)