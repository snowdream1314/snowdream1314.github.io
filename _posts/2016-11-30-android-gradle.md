---
layout: post
title:  "Android Studio Gradle的配置及打包多个不同特性的应用"
date:   2016-11-30 16:24:05
categories: Android
excerpt: 
---

* content
{:toc}

---

#### Android Gradle相关配置

		android {

			compileSdkVersion 24
		    buildToolsVersion "24.0.1"

		    //默认的一些设置
		    defaultConfig {		
		    	applicationId "com.example.demo"	//应用包名
		        minSdkVersion 15	//支持的最低版本，低于此版本的设备无法安装应用
		        targetSdkVersion 19		//目标版本
		        multiDexEnabled true

		        ndk {	//ndk相关设置
		            moduleName = "core"		//对应的C/C++文件名
		            abiFilters "armeabi", "armeabi-v7a", "x86"		//支持的平台
		        }

		    }

		    //编译的版本的一些设置，默认生成debug和release，也可以自定义
		    buildTypes {	

		        release {	//release版本的一些默认设置
		//            signingConfig signingConfigs.config
		            minifyEnabled true		//是否进行混淆
		            zipAlignEnabled true	//安装包中的资源按4字节对齐,减少应用在运行时的内存消耗
		            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
		            
		        }

		        debug {  //debug版本的设置
		            applicationIdSuffix ".debug"  //包名加后缀，即生成的包名是com.example.demo.debug
		        }

		        //创建一个新的BuildType，名字是jnidebug，同时配置它是复制自debug Build Type;
		        jnidebug.initWith(buildTypes.debug)  
		        jnidebug {  
		            packageNameSuffix ".jnidebug"  //添加一个不同的包名后缀
		            jniDebuggable true  			//配置jnidebug开启debug版本的JNI组件
		        }    
    		}

    		//签名的一些设置
    		signingConfigs {	//不同的版本可以配置不同的签名

		        demo1 {
		            keyAlias 'demo'
		            keyPassword '111111'
		            storeFile file('C:\\Desktop\\apps\\demo1.jks')
		            storePassword '111111'
		        }

			}
		}

---

#### 打包不同版本的App

* Gradle配置

		//除了默认的配置以外还要配置不同版本的签名
		signingConfigs {	//不同的版本可以配置不同的签名

	        demo1 {		//版本1
	            keyAlias 'demo1'
	            keyPassword '111111'
	            storeFile file('C:\\Desktop\\apps\\demo1.jks')
	            storePassword '111111'
	        }

	        demo2 {		//版本2
	            keyAlias 'demo2'
	            keyPassword '111111'
	            storeFile file('C:\\Desktop\\apps\\demo2.jks')
	            storePassword '111111'
	        }

		}


		//利用productFlavors配置不同特性的App

		flavorDimensions "flavor"

	    productFlavors {
	        //App1
	        demo1 {
	            dimension "flavor"
	            applicationId "com.example.demo1"	//不同的包名
	            versionCode 10
	            versionName "1.0"
	            signingConfig signingConfigs.demo1 		//demo1的签名配置

	            buildConfigField "String", "App_Name", "\"我的Demo1\""	//配置一些常量，用以代码中做版本判断
	            buildConfigField "String", "App_Name_En", "\"demo1\""
	            buildConfigField "String", "App_Version", "\"1.0\""
	        }

	        //App2
	        demo2 {
	            dimension "flavor"
	            applicationId "com.example.demo2"
	            versionCode 18
	            versionName "1.85"
	            signingConfig signingConfigs.demo2

	            buildConfigField "String", "App_Name", "\"我的Demo2\""
	            buildConfigField "String", "App_Name_En", "\"demo2\""
	            buildConfigField "String", "App_Version", "\"1.85\""
	        }
	    }

* 经过以上的配置以后，点击Android Studio左下角的Build Variant，可以看到一共又四个版本的App可以编译，分别是：demo1Debug、demo1Release、demo2Debug、demo2Release;而且demo1和demo2的包名不同，可以安装在同一部手机里

* 不同的特性的应用往往很多东西需要改，比如名字，图标，启动图，主题色，及其他一些功能，比如推送、更新的一些设置等。

		//根据不同的版本配置AndroidManifest.xml文件,同productFlavors的manifestPlaceholders属性
		productFlavors {
	        //App1
	        demo1 {
	            ......

	            manifestPlaceholders = ["app_name":"demo1", "app_icon":"@mipmap/icon","umeng_app_key": "111111"]

	            ......

	        //App2
	        demo2 {
	            ......

	            manifestPlaceholders = ["app_name":"demo2", "app_icon":"@mipmap/icon", "umeng_app_key": "1123211"]

	            .....
	        }
	    }

	    //在AndroidManifest.xml文件中更改对应的属性,格式为 “${变量名}”
	    <application
	        android:name="com.caibo_inc.happyshopping.application.HSApplication"
	        android:allowBackup="true"
	        android:icon="${app_icon}"
	        android:label="${app_name}"
	        
	        android:theme="@style/AppTheme"
	        android:windowIsTranslucent="true"
	        tools:replace="android:icon,android:allowBackup">

	        ......

	        <meta-data
	            tools:replace="android:value"	//在需要替换的属性处加tools:replace="属性名"，否则会报冲突错误
	            android:name="UMENG_APPKEY"
	            android:value="${umeng_app_key}" />

	        ......

	    </applocation>

* 除了以上的简单配置以外，还可以根据需要更改其他的一些特性，比如不同版本的App需要不同的启动图，以及更改主色等。方法是直接再Src目录下，建立一个与main同级的结构和main一样的目录,去除所有的Java代码和资源等，在需要更改的地方，比如需要修改字符串或者颜色，就重写values下的colors，或是strings文件，编译的时候同样的属性会合并替换，layout下的同名xml文件会替换。这样不同版本的App只要建立不同的目录，修改对应的资源文件即可。然后在代码中根据不同的版本做相应处理即可。

* 所建立的目录不能有和main中一样的类，否则会报同名冲突错误；但是可以增加类， 也就是可以增加功能。

---



---

> 参考文章：[如何通过Gradle实现一套代码开发不同特性的APK](https://ghui.me/post/2015/03/create-several-variants/)、[Build Types（构建类型）](https://avatarqing.gitbooks.io/gradlepluginuserguidechineseverision/content/basic_project/build_types.html)

---

### 结语：

坚持每天进步一点点...

---