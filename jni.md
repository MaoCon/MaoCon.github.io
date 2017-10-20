###1. build.gradle，在android/defaultConfig下面添加ndk配置


		defaultConfig {
	        applicationId "com.mc.testjni"
	        minSdkVersion 22
	        targetSdkVersion 26
	        versionCode 1
	        versionName "1.0"
	        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
	
	        ndk {
	            moduleName "JNISample"
	        }
	    }

moduleName 表示编译出的so文件的名字



2. Jni的工具类，并定义接口函数，函数关键字用native
---------------------------------------------


3. 生成头文件,mk,so
------------------

* "make-project"编译完成

*  打开终端，运行

		cd app/build/intermediates/classes/debug/
		javah 包名.类名

* 将生成的头文件.h拷贝到src\main\jni目录下，编写c代码

* NDK报版本不兼容，则需在manifest中添加最低和适配的安卓版本

* jni目录下新建Android.mk

		LOCAL_PATH := $(call my-dir)
		include $(CLEAR_VARS)
		LOCAL_MODULE := JNISample
		LOCAL_SRC_FILES := com_lilei_testjni_JniUtils.cpp
		include $(BUILD_SHARED_LIBRARY)

* jni目录下新建Application.mk

		APP_STL := gnustl_static
		APP_CPPFLAGS := -frtti -fexceptions -std=c++0x
		APP_ABI := armeabi armeabi-v7a arm64-v8a x86
		APP_PLATFORM := android-22

* 打开终端cd到jni目录下,调用ndk-build开始编译so

		ndk-build -d //打印日志
		ndk-build clean //重新编译，需要clean


4. gradle.properties下添加
--------------------------

	android.useDeprecatedNdk=true



5. build.gradle中buildTypes节点下添加sourceSets
----------------------------------------------

	 buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }

        sourceSets {
            main {
                jni.srcDirs = []
                jniLibs.srcDirs = ['src/main/libs'] //未配置，可能导致so不能打包到apk中
            }
        }
    }
