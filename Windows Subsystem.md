### 安卓子系统

Windows Subsystem for Android（WSA）是微软在 Windows 11 系统上推出的 Android 子系统，它允许将 Android 应用部署在 Windows 系统上。

WSA 可以通过 Microsoft Store 获取并安装，但目前国区的应用商店内并没有上架。

### 虚拟化技术

Windows Subsystem for Android 依赖于虚拟化技术，需要在 Windows 功能中启用 Hyper-V 功能和虚拟机平台功能，请参考：[虚拟化技术](./Hyper-V & Easy-GPU-PV.md/###虚拟化技术)。

### WSA 安装

虽然国区的应用商店内没有上架 WSA 安卓子系统应用，但仍旧可以使用某些手段，将 WSA 安装到 Windows 系统上。

第一种方式需要修改系统设置内的**国家或地区**选项，将“中国”修改为“美国”（或其它支持安装 WSA 安卓子系统应用的区域）：

<img src="images/Windows Subsystem.images/Snipaste_2022-08-12_15-04-53.png" alt="Snipaste_2022-08-12_15-04-53" style="zoom: 50%;" />

修改完毕后打开 Microsoft Store 应用商店，它将自动定位到美区的应用市场上。

搜索 Amazon Appstore 下载并安装，WSA 也将会一并安装到 Windows 系统中：

<img src="images/Windows Subsystem.images/Snipaste_2022-08-12_15-07-50.png" alt="Snipaste_2022-08-12_15-07-50" style="zoom: 50%;" />

这种方式虽然很简单，但没人知道微软是否会在未来对应用商店的区域进行封锁。如果未来微软对商店区域进行封锁，还可以选择使用第二种安装方式。

第二种方式需要借助命令行安装，它没有第一种方式那么简单，且安装前需要自行获取 WSA 的离线安装包。

访问 [Microsoft Store - Generation Project](https://store.rg-adguard.net) 搜索 WSA 的 ProductId 产品编码 **9P3395VX91NR**：

<img src="images/Windows Subsystem.images/image-20220812151825728.png" alt="image-20220812151825728" style="zoom: 50%;" />

列表最末尾的 .msixbundle 类型文件就是 WSA 安装包文件，直接下载到本地即可。

进入安装包所在目录，以管理员身份打开 Windows PowerShell 终端并执行命令：

```powershell
Add-AppxPackage .\MicrosoftCorporationII.WindowsSubsystemForAndroid_2206.40000.15.0_neutral___8wekyb3d8bbwe.Msixbundle
```

<img src="images/Windows Subsystem.images/Snipaste_2022-08-12_15-34-00.png" alt="Snipaste_2022-08-12_15-34-00" style="zoom: 50%;" />

命令执行完毕后 WSA 的安装也完成了。Windows 11 系统下，可以在全部应用中查看到 WSA 安卓子系统应用：

<img src="images/Windows Subsystem.images/image-20220812153906724.png" alt="image-20220812153906724" style="zoom:50%;" />

### Android 应用安装

WSA 部署完毕后，会同时将亚马逊应用商店（Amazon Appstore）安装到安卓子系统中，以便于使用者安装其他的 Android 应用。但受国区限制，亚马逊应用商店一般无法使用。

因此，在 WSA 中安装 Android 应用需借助其他手段，即 [Android 调试桥](https://developer.android.com/studio/command-line/adb)（Android Debug Bridge, adb）。该工具可以使用命令行的方式，直接将 Android 应用安装至 WSA 中。

使用 ADB 工具可以通过任何第三方应用商店将应用安装至 WSA 中，但这也意味着 Android Package（.apk）需要自行获取。

#### Android 调试桥

ADB 工具在 Windows 系统下，是一个简单的 abd.exe 可执行程序，但它仅能够在终端中以命令行的形式调用。

ADB 工具可从 [Platform Tools](https://developer.android.com/studio/releases/platform-tools) 网站获取，选择“**适用于 Windows 的 SDK Platform-Tools**”下载：

<img src="images/Windows Subsystem.images/image-20220812163031217.png" alt="image-20220812163031217" style="zoom: 50%;" />

下载解压后，为了配置全局环境变量方便直接调用，推荐将该目录放置到固定的位置：

<img src="images/Windows Subsystem.images/image-20220812160002367.png" alt="image-20220812160002367" style="zoom: 50%;" />

将 abd.exe 所在目录添加到**系统变量**下的 Path 中：

<img src="images/Windows Subsystem.images/image-20220812163246253.png" alt="image-20220812163246253" style="zoom:50%;" />

#### 启用开发者模式

ADB 工具本质用于调试 Android 系统，启用调试需要连接到指定的 Android 系统。而 Android 系统如果要接受调试，则必须启用**开发人员模式**（开发者模式）。

打开 WSA 子系统，在开发人员选项卡中启用开发人员模式：

<img src="images/Windows Subsystem.images/image-20220812160152216.png" alt="image-20220812160152216" style="zoom: 50%;" />

首次启用开发人员模式时，WSA 内的调试模式可能仍尚未启用，使用 ADB 工具连接 WSA 可能会提示连接失败。

解决方法是**重启子系统**，或进入**管理开发人员设置**手动**激活开发人员模式**。

#### 连接/断开 Android 系统

以下命令用于连接 Android 系统：

```powershell
adb connect 127.0.0.1:58526
```

以下命令用于断开 Android 系统：

```powershell
adb disconnect 127.0.0.1:58526
```

<img src="images/Windows Subsystem.images/image-20220812160804526.png" alt="image-20220812160804526" style="zoom: 50%;" />

#### 使用 ADB 安装应用

ADB 工具安装应用的命令十分简短，连接 WSA 后使用以下命令即可安装任意 Android 应用：

```powershell
adb install {android package path}
```

以 Apple Music 为例，你需要获取 Apple Music.apk 安装包，并执行安装 Android 应用的命令：

<img src="images/Windows Subsystem.images/image-20220812161146589.png" alt="image-20220812161146589" style="zoom:50%;" />

待命令输出完毕后，表示应用安装完成。Windows 11 系统下，你可以在全部应用中找到已安装的 Android 应用入口：

<img src="images/Windows Subsystem.images/image-20220812161242068.png" alt="image-20220812161242068" style="zoom:50%;" />

卸载应用与卸载 UWP 应用无异，只需要在应用图标上右键菜单中选择卸载，即可移除指定的 WSA 应用。

### 更多设置

WSA 默认采用的**子系统资源**策略是**连续**模式，该模式下子系统始终运行在后台，以便加快 WSA 应用的启动速度。

<img src="images/Windows Subsystem.images/image-20230414193447540.png" alt="image-20230414193447540" style="zoom: 50%;" />

简单来说，子系统资源中如果选择了连续模式，就等同于选择了让 WSA 随开机一起自启动。

如果需要频繁使用 WSA 应用，则推荐选择连续模式，否则可以选择**按需要**模式。

### 其他问题

WSA 安装子系统在使用的过程中，经常弹出以下连接受限通知：

<img src="images/Windows Subsystem.images/image-20220812165347271.png" alt="image-20220812165347271" style="zoom:50%;" />

但实际上 VirtWifi 并非受限，换句话说 WSA 内的网络访问实际没有问题。

可以使用 ABD 调试工具，以命令行的方式关闭 WSA 的连接受限通知：

```shell
adb shell settings put global captive_portal_https_url https://www.google.cn/generate_204
adb shell settings put global captive_portal_http_url http://www.google.cn/generate_204
```

从命令行中不难知道，WSA 内使用 https://www.google.cn/generate_204 网址作为 VirtWifi 是否受限的测试链接，但该网址在国内始终是处于不可访问的状态。这样即便 VirtWifi 网络正常，WSA 内的网络连通性测试大概率也会以失败告终，反馈到 Windows 系统通知中就是连接受限了。
