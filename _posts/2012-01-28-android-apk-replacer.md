---
layout: post
title: Android 应用无缝替换工具
category: work
---

经常捣鼓Android的童鞋们，常常会碰到应用程序由于签名不同，无法替换安装的问题（至于签名为什么会不同，当然是因为各种汉化、破解啦）。虽然这是系统为我们提供的安全策略之一，但是同时又带来了不少麻烦，常用的方案卸载重装，还会丢失宝贵数据，怎么才能巧妙的的绕过这一限制呢？

![appreplacer01](http://blog.toraleap.com/wp-content/uploads/2012/01/appreplacer01.png)  
要汉化，还是要新关卡，这是一个问题。要是就这么一卸载，辛苦玩过来的存档可就全飞走了。

候选方案有二者： 
1.  想办法取消系统的签名验证策略 
2.  备份应用数据，完成卸载重装后恢复

显然，以上不管哪种方法，需要Root权限是毋庸置疑的。难度上方法2明显更简单，但是可靠性上面会差一些。分析了部分系统源码后，未找到方法1的突破口，还是果断来到方法2吧。

应用程序的数据存放在“/data/data/应用程序包名”的文件夹中，至于在SD卡上的部分，卸载过程中不会清空，因此不需要我们做任何操作。以下以游戏贪婪的蜘蛛作演示，注意需要一款支持Root权限的文件浏览器才能查看。

![appreplacer02](http://blog.toraleap.com/wp-content/uploads/2012/01/appreplacer021.png)  

在有Root权限的情况下，可行性已经有了，首先来一个简单的规划：

当点击一个apk文件时，我们的程序启动： 
1.  分析apk文件，从中获取包名、版本号、签名信息 
2.  判断本地是否已经安装同名应用，若否，启动系统默认安装器 
3.  判断本地应用的签名是否和apk文件相同，若是，启动系统默认安装器 
4.  显示UI界面对比新旧应用，让用户确认是否替换

替换任务分四步走： 
1.  备份数据文件 
2.  卸载原始应用 
3.  安装新版应用 
4.  恢复数据文件

根据上面的规划，模块就这么分好了，接下来依次进行。

1-0.点击apk启动应用：

打开AndroidManifest.xml文件，将主Activity的intent-filter修改如下：

	<intent-filter> 
	    <action android:name="android.intent.action.VIEW" /> 
	    <category android:name="android.intent.category.DEFAULT" /> 
	    <data android:scheme="content" /> 
	    <data android:scheme="file" /> 
	    <data android:mimeType="application/vnd.android.package-archive" /> 
	</intent-filter>
目标是作为默认应用安装器，但是只在有必要时才自己处理，其他时候都把任务转交给系统自带安装器。

1-1.分析本地apk文件：

	PackageManager pm = getPackageManager(); 
	PackageInfo newInfo = pm.getPackageArchiveInfo(apkFile, PackageManager.GET_ACTIVITIES | PackageManager.GET_SIGNATURES);
接下来使用newInfo的各个域获得apk相关信息。注意Android大部分版本存在的一个bug会导致获取apk文件信息失败，我们先用如下的workaround。

	ApplicationInfo newAppInfo = newInfo.applicationInfo; 
	newAppInfo.sourceDir = apkFile; 
	newAppInfo.publicSourceDir = apkFile;
 
之后再进行newAppInfo的读取。

1-2.判断本地同名应用

	PackageInfo oldInfo = pm.getPackageInfo(packageName, PackageManager.GET_ACTIVITIES | PackageManager.GET_SIGNATURES);
 
这个很简单，用上面获得的包名调用此函数即可，若应用未安装会抛出NameNotFoundException异常。

启动系统默认安装器的方法如下，因为我们的应用也是作为一个安装器存在，因此克隆下请求的原始Intent，转发给系统自带安装器就好了：

	private void launchDefaultPackageInstaller() { 
	    Intent intent = (Intent)getIntent().clone(); 
	    intent.setClassName("com.android.packageinstaller", "com.android.packageinstaller.PackageInstallerActivity"); 
	    startActivity(intent); 
	}
1-3.签名比对

PackageInfo类有一个域signatures包含了应用签名数据，比对此域即可。

	private boolean compareSignature(Signature[] s1, Signature[] s2) { 
	    if (s1 == null || s2 == null) { 
	        return false; 
	    } 
	    HashSet<Signature> set1 = new HashSet<Signature>(); 
	    for (Signature sig : s1) { 
	        set1.add(sig); 
	    } 
	    HashSet<Signature> set2 = new HashSet<Signature>(); 
	    for (Signature sig : s2) { 
	        set2.add(sig); 
	    } 
	    return set1.equals(set2); 
	}
同样由于某些版本Android的bug，apk文件的签名可能无法正常获取，需要修改一个隐藏类PackageParser的源码以解决。

1-4.用户确认替换

实现个简单的UI，显示一些必要的信息，这个就一图以概吧。

![appreplacer03](http://blog.toraleap.com/wp-content/uploads/2012/01/appreplacer03.png)  

2-1.备份应用数据

应用数据的位置已经知道了，为了访问到该受保护的数据，我们需要用到Root权限。使用Root权限运行命令的函数如下：

	public static boolean runRootCommand(String command) { 
	    Process process = null; 
	    DataOutputStream os = null; 
	    try { 
	        process = Runtime.getRuntime().exec("su"); 
	        os = new DataOutputStream(process.getOutputStream()); 
	        os.writeBytes(command + "\n"); 
	        os.writeBytes("exit\n"); 
	        os.flush(); 
	        process.waitFor(); 
	    } catch (Exception e) { 
	        Log.d("*** DEBUG ***", "Unexpected error – Here is what I know: " + e.getMessage());
	        return false; 
	    } 
	    finally { 
	        try { 
	            if (os != null) { 
	                os.close(); 
	            } 
	            process.destroy(); 
	        } catch (Exception e) { 
	            // nothing 
	        } 
	    } 
	    return true; 
	}
Root过的机器大多都有busybox工具包，我们就用tar命令将数据文件打包到存储卡好了，注意这里使用了参数-zcpf：

	private boolean backupData() { 
	    String sdcard = Environment.getExternalStorageDirectory().getAbsolutePath(); 
	    String rootCmd = String.format("rm \"%s/fpibackup_%s.tar.gz\";tar -zcpf \"%s/fpibackup_%s.tar.gz\" \"/data/data/%s\"", sdcard, packageName, sdcard, packageName, packageName); 
	    return runRootCommand(rootCmd); 
	}
2-2.卸载原始应用

两种方案，一种是利用Root权限无提示安装，另一种是启动系统自带卸载工具（注释部分），既然前面已经有UI了，这里果然还是无提示的更好吧？

	private void uninstallPackage() { 
	    runRootCommand("pm uninstall \"" + packageName + "\""); 
	    //Uri packageURI = Uri.parse("package:" + packageName);      
	    //Intent uninstallIntent = new Intent(Intent.ACTION_DELETE, packageURI);      
	    //startActivity(uninstallIntent); 
	}
2-3.安装新版应用

同上有两种方案，这里还是用静默安装了。

	private void installPackage() { 
	    runRootCommand("pm install \"" + apkFile + "\""); 
	}
2-4.恢复数据文件

同样是tar命令，只是这里解压使用了参数-zxpf，还原后删除备份文件。

	private boolean restoreData() { 
	    String sdcard = Environment.getExternalStorageDirectory().getAbsolutePath(); 
	    String rootCmd = String.format("tar -zxpf \"%s/fpibackup_%s.tar.gz\" -C /;rm \"%s/fpibackup_%s.tar.gz\"", sdcard, packageName, sdcard, packageName); 
	    return runRootCommand(rootCmd);    
	}
大体上就是这么些内容，测试游戏存档完美继承。当然由于根本原理是卸载重装，应用的桌面插件及快捷方式还是不可避免的受到影响了。

![appreplacer04](http://blog.toraleap.com/wp-content/uploads/2012/01/appreplacer04.png)

![appreplacer05](http://blog.toraleap.com/wp-content/uploads/2012/01/appreplacer05.png)

![appreplacer06](http://blog.toraleap.com/wp-content/uploads/2012/01/appreplacer06.png)

最后放出下载：[源代码](http://res.toraleap.com/attachments/AppReplacer.zip) [APK文件](http://res.toraleap.com/attachments/AppReplacer.apk)