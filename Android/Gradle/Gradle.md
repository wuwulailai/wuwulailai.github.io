# 各个文件用途
## gradle.properties
敏感信息

## gradle-wrapper.properties
gradle版本下载和安装配置

## settings.gradle
文件将包含必要的一些设置，如任务或项目之间的依赖关系等
settings.gradle是模块Module配置文件，
大多数setting.gradle的作用是为了配置子模块
根目录的setting.gradle脚本文件是针对module的全局配置
setting.gradle用户创建多Project的Gradle项目。Project在IDEA对应Module模块
配置module名rootProject.name = 'project-root',为指定父模块的名称，
include 'hello'为指定包含哪些模块

// 平台根
rootProject.name = 'project-root'
// 包含子系统以及模块
include ':project-core'
// hello系统模块的加载
include ':project-hello'
// world系统模块的加载
include ':project-world'

## build.gradle
### Project的build.gradle
buildscript { //这里是gradle脚本执行所需依赖，分别是对应的maven库和插件
    repositories {
        google()//从Android Studio3.0后新增了google()配置，可以引用google上的开源项目
        jcenter()//是一个类似于github的代码托管仓库，声明了jcenter()配置，可以轻松引用 jcenter上的开源项目
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.0'////此处是android的插件gradle，gradle是一个强大的项目构建工具
        

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {//这里是项目本身需要的依赖，比如项目所需的maven库
    repositories {
        google()
        jcenter()
    }
}

// 运行gradle clean时，执行此处定义的task任务。
// 该任务继承自Delete，删除根目录中的build目录。
// 相当于执行Delete.delete(rootProject.buildDir)。
// gradle使用groovy语言，调用method时可以不用加（）。
task clean(type: Delete) {
    delete rootProject.buildDir
}

#### buildscript{}闭包里是gradle脚本执行所需依赖，分别是对应的maven库和插件
#### allprojects{}闭包里是项目本身需要的依赖，比如项目所需的maven库。
#### task clean(type: Delete){}是运行gradle clean时，执行此处定义的task任务，该任务继承自Delete，
#### 删除根目录中的build目录。其中buildscript包含repositories闭包和dependencies闭包。
repositories{}闭包：配置远程仓库
jcenter是一个代码托管仓库
google()配置，可以引用google上的开源项目。

dependencies{}闭包：配置构建工具

### Module的build.gradle文件
分为三部分：
1、apply plugin:
// 声明是Android程序，
//com.android.application 表示这是一个应用程序模块,打包得到 .apk
//com.android.library 标识这是一个库模块, 打包得到.aar文件
//而这区别：前者可以直接运行，后着是依附别的应用程序运行
apply plugin: 'com.android.application'

2、android{}闭包
配置项目构建的各种属性
2.1添加signingConfigs{}闭包
signingConfigs {// 自动化打包配置
        release {// 线上环境
            keyAlias 'test'
            keyPassword '123456'
            storeFile file('test.keystore')
            storePassword '123456'
        }
        debug {// 开发环境
            keyAlias 'test'
            keyPassword '123456'
            storeFile file('test.keystore')
            storePassword '123456'
        }
    }

2.2 compileSdkVersion 设置编译时的Android版本
2.3 buildToolsVersion 设置编译时使用的构建版本 3.0去掉此项配置
2.4 defaultConfig{} 闭包
 defaultConfig {
        applicationId "com.billy.myapplication"//项目的包名
        minSdkVersion 16//项目最低兼容的版本
        targetSdkVersion 27//项目的目标版本
        versionCode 1//版本号
        versionName "1.0"//版本名称
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"//表明要使用AndroidJUnitRunner进行单元测试
    }

 targetSdkVersion：指定项目的目标版本，在该目标版本上已经做过充分测试。   
2.5 buildTypes{}闭包
这个闭包指定生成安装文件的主要配置，包含两个子闭包，一个debug闭包，用于指定测试版安装文件的位置
一个是Release闭包，指定正式版生成安装文件的配置。
    buildTypes {// 生产/测试环境配置
        release {// 生产环境
            buildConfigField("boolean", "LOG_DEBUG", "false")//配置Log日志
            buildConfigField("String", "URL_PERFIX", "\"https://release.cn/\"")// 配置URL前缀
            minifyEnabled false//是否对代码进行混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'//指定混淆的规则文件
            signingConfig signingConfigs.release//设置签名信息
            pseudoLocalesEnabled false//是否在APK中生成伪语言环境，帮助国际化的东西，一般使用的不多
            zipAlignEnabled true//是否对APK包执行ZIP对齐优化，减小zip体积，增加运行效率
            applicationIdSuffix 'test'//在applicationId 中添加了一个后缀，一般使用的不多
            versionNameSuffix 'test'//在applicationId 中添加了一个后缀，一般使用的不多
        }
        debug {// 测试环境
            buildConfigField("boolean", "LOG_DEBUG", "true")//配置Log日志
            buildConfigField("String", "URL_PERFIX", "\"https://test.com/\"")// 配置URL前缀
            minifyEnabled false//是否对代码进行混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'//指定混淆的规则文件
            signingConfig signingConfigs.debug//设置签名信息
            debuggable false//是否支持断点调试
            jniDebuggable false//是否可以调试NDK代码
            renderscriptDebuggable false//是否开启渲染脚本就是一些c写的渲染方法
            zipAlignEnabled true//是否对APK包执行ZIP对齐优化，减小zip体积，增加运行效率
            pseudoLocalesEnabled false//是否在APK中生成伪语言环境，帮助国际化的东西，一般使用的不多
            applicationIdSuffix 'test'//在applicationId 中添加了一个后缀，一般使用的不多
            versionNameSuffix 'test'//在applicationId 中添加了一个后缀，一般使用的不多
        }
    }

  要添加signingConfigs闭包

  2.6 sourcesSets{}闭包，配置目录指向
  sourceSets {//目录指向配置
        main {
            jniLibs.srcDirs = ['libs']//指定lib库目录
        }
    }
    可以方便我们存放jar包和库文件，

  2.7 packagingOpetions{} 闭包：打包时的相关配置
  两个依赖库中存在同一个文件，打包时会提示错误，可以用exclude去除重复的文件
  packagingOptions{
        //pickFirsts做用是 当有重复文件时 打包会报错 这样配置会使用第一个匹配的文件打包进入apk
        // 表示当apk中有重复的META-INF目录下有重复的LICENSE文件时  只用第一个 这样打包就不会报错
        pickFirsts = ['META-INF/LICENSE']

        //merges何必 当出现重复文件时 合并重复的文件 然后打包入apk
        //这个是有默认值得 merges = [] 这样会把默默认值去掉  所以我们用下面这种方式 在默认值后添加
        merge 'META-INF/LICENSE'

        //这个是在同时使用butterknife、dagger2做的一个处理。同理，遇到类似的问题，只要根据gradle的提示，做类似处理即可。
        exclude 'META-INF/services/javax.annotation.processing.Processor'
    }

    2.8 productFlavors{}闭包：多个渠道配置
    适配多个渠道，位特定渠道做部分特殊的处理，如设置不同的包名、应用名等，用productFlavors生成不同的渠道包
    android {  
    productFlavors {
        wandoujia {
            //豌豆荚渠道包配置
            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "wandoujia"]
            //manifestPlaceholders的使用在后续章节（AndroidManifest里的占位符）中介绍
        }
        xiaomi {
            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "xiaomi"]
            applicationId "com.wiky.gradle.xiaomi" //配置包名

        }
        _360 {
            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "_360"]
        }
        //...
    }  
}

// 更简洁的方式：
android {  
    productFlavors {
        wandoujia {}
        xiaomi {}
        _360 {}
       //...
    }  

    productFlavors.all { 
        //批量修改，类似一个循序遍历
        flavor -> flavor.manifestPlaceholders = [UMENG_CHANNEL_VALUE: name] 
    }
}

2.9 lintOptions{}闭包：代码扫描分析
帮助发现代码结构/质量问题，提供一些解决方案。
//程序在编译的时候会检查lint，有任何错误提示会停止build，我们可以关闭这个开关
    lintOptions {
        abortOnError false //即使报错也不会停止打包
        checkReleaseBuilds false  //打包release版本的时候进行检测
    }

### 3、 dependencies {}闭包
三种依赖方式：本地依赖、库依赖和远程依赖
本地依赖：对本地的jar包或目录添加依赖
库依赖：对项目的恶库模块添加依赖
远程依赖：对jcenter的开源项目添加依赖。

通过api和implemention引入库
api引入的库整个项目都可以使用
implemention引入的库只有对Module能使用。
dependencies {//项目的依赖关系
    implementation fileTree(include: ['*.jar'], dir: 'libs')//本地jar包依赖
    implementation 'com.android.support:appcompat-v7:27.1.1'//远程依赖
    implementation 'com.android.support.constraint:constraint-layout:1.1.2'
    testImplementation 'junit:junit:4.12'//声明测试用例库
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
}

Module完成的build.gradle
// 声明是Android程序，
//com.android.application 表示这是一个应用程序模块
//com.android.library 标识这是一个库模块
//而这区别：前者可以直接运行，后着是依附别的应用程序运行
apply plugin: 'com.android.application'

android {
    signingConfigs {// 自动化打包配置
        release {// 线上环境
            keyAlias 'test'
            keyPassword '123456'
            storeFile file('test.jks')
            storePassword '123456'
        }
        debug {// 开发环境
            keyAlias 'test'
            keyPassword '123456'
            storeFile file('test.jks')
            storePassword '123456'
        }
    }
    compileSdkVersion 27//设置编译时用的Android版本
    defaultConfig {
        applicationId "com.billy.myapplication"//项目的包名
        minSdkVersion 16//项目最低兼容的版本
        targetSdkVersion 27//项目的目标版本
        versionCode 1//版本号
        versionName "1.0"//版本名称
        flavorDimensions "versionCode"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"//表明要使用AndroidJUnitRunner进行单元测试
    }
    buildTypes {// 生产/测试环境配置
        release {// 生产环境
            buildConfigField("boolean", "LOG_DEBUG", "false")//配置Log日志
            buildConfigField("String", "URL_PERFIX", "\"https://release.cn/\"")// 配置URL前缀
            minifyEnabled false//是否对代码进行混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'//指定混淆的规则文件
            signingConfig signingConfigs.release//设置签名信息
            pseudoLocalesEnabled false//是否在APK中生成伪语言环境，帮助国际化的东西，一般使用的不多
            zipAlignEnabled true//是否对APK包执行ZIP对齐优化，减小zip体积，增加运行效率
            applicationIdSuffix 'test'//在applicationId 中添加了一个后缀，一般使用的不多
            versionNameSuffix 'test'//在applicationId 中添加了一个后缀，一般使用的不多
        }
        debug {// 测试环境
            buildConfigField("boolean", "LOG_DEBUG", "true")//配置Log日志
            buildConfigField("String", "URL_PERFIX", "\"https://test.com/\"")// 配置URL前缀
            minifyEnabled false//是否对代码进行混淆
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'//指定混淆的规则文件
            signingConfig signingConfigs.debug//设置签名信息
            debuggable false//是否支持断点调试
            jniDebuggable false//是否可以调试NDK代码
            renderscriptDebuggable false//是否开启渲染脚本就是一些c写的渲染方法
            zipAlignEnabled true//是否对APK包执行ZIP对齐优化，减小zip体积，增加运行效率
            pseudoLocalesEnabled false//是否在APK中生成伪语言环境，帮助国际化的东西，一般使用的不多
            applicationIdSuffix 'test'//在applicationId 中添加了一个后缀，一般使用的不多
            versionNameSuffix 'test'//在applicationId 中添加了一个后缀，一般使用的不多
        }
    }

    sourceSets {//目录指向配置
        main {
            jniLibs.srcDirs = ['libs']//指定lib库目录
        }
    }

    packagingOptions{//打包时的相关配置
        //pickFirsts做用是 当有重复文件时 打包会报错 这样配置会使用第一个匹配的文件打包进入apk
        // 表示当apk中有重复的META-INF目录下有重复的LICENSE文件时  只用第一个 这样打包就不会报错
        pickFirsts = ['META-INF/LICENSE']

        //merges何必 当出现重复文件时 合并重复的文件 然后打包入apk
        //这个是有默认值得 merges = [] 这样会把默默认值去掉  所以我们用下面这种方式 在默认值后添加
        merge 'META-INF/LICENSE'

        //这个是在同时使用butterknife、dagger2做的一个处理。同理，遇到类似的问题，只要根据gradle的提示，做类似处理即可。
        exclude 'META-INF/services/javax.annotation.processing.Processor'
    }

    productFlavors {
        wandoujia {}
        xiaomi {}
        _360 {}
    }

    productFlavors.all {
            //批量修改，类似一个循序遍历
        flavor -> flavor.manifestPlaceholders = [IFLYTEK_CHANNEL: name]
    }

    //程序在编译的时候会检查lint，有任何错误提示会停止build，我们可以关闭这个开关
    lintOptions {
        abortOnError false
        //即使报错也不会停止打包
        checkReleaseBuilds false
        //打包release版本的时候进行检测
    }

}

dependencies {
    //项目的依赖关系
    implementation fileTree(include: ['*.jar'], dir: 'libs')
    //本地jar包依赖
    implementation 'com.android.support:appcompat-v7:27.1.1'
    //远程依赖
    implementation 'com.android.support.constraint:constraint-layout:1.1.2'
    testImplementation 'junit:junit:4.12'
    //声明测试用例库
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
}

### Gradle工作流程
1、初始化阶段：
2、首先解析settings.gradle
Configure阶段：
解析每个Project中的build.gradle,解析过程中并不执行各个build.gradle中的task

经过Configure阶段，Project之间及内部Task之间的关系就确定了。
一个Project包含很多Task，每个Task之间有依赖关系，Configuration会建立一个有向图来描述Task之间的依赖关系，
所有Project配置完成后，会有一个回调project.afterEvaluate,表示所有模块已经配置完成。
3、执行Task任务

