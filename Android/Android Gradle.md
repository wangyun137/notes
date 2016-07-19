# Android Gradle
`Anddroid`的`Gradle`使用`DSL`（Domain Specific Language ）

## Custom Build Configurations

### Build Types（buildTypes）
在该选项下，可以定义构建的版本（`release or debug`）,`debug`版本会使用`debug key`来签名，而`release`版本则会使用`release key`签名并且进行混淆等操作。必须至少指定一种`build type`

### Profuct Flavors（productFlavors）
通过该选项可以构建不同版本（使用公共代码和资源，同时也使用属于自己版本的不同代码和资源）的`app`。该选项是可选的，而且必须手动添加，默认是没有的

### Build Variants
定义了构建的基本配置选项,`Build Types、Profuct Flavors`都属于`Build Variants`

### Manifest Entries
可以在`Build Variant Configure`指定部分`manifest`文件的属性，指定的属性会覆盖`manifest`文件中对应的值。如果想产生多个`application name、target SDK version`这些选项不相同的`apk`时，该选项非常有用

### Signing
指定签名选项， `gradle`并不会默认对`release`版本进行签名，除非在配置选项中明确定义了签名选项

### ProGuard
可以指定混淆选项

## Build Configuration Files
创建一个新的`Project`的时候，`Android Studio`会自动创建如下图所示的文件结构：
MyApp/
|_ build.gradle
|_ settings.gradle
|_ app/
  |_ build.gradle
  |_ build/
  |_ libs/
  |_ src/
     |_ main/

### settings.gradle
该文件的作用是告诉`Gradle`要构建哪些模块

### The Top-level build.gradle
位于`Project`根目录的`build.gradle`中定义的构建配置会应用在每个模块上。默认情况下，该`gradle`文件会用`buildscript{}`来定义`Gradle repositories、dependencies`， Example:
```
/**
 * The buildscript {} block is where you configure the repositories and
 * dependencies for Gradle itself--meaning, you should not include dependencies
 * for your modules here. For example, this block includes the Android plugin for
 * Gradle as a dependency because it provides the additional instructions Gradle
 * needs to build Android app modules.
 */

buildscript {

    /**
     * The repositories {} block configures the repositories Gradle uses to
     * search or download the dependencies. Gradle pre-configures support for remote
     * repositories such as JCenter, Maven Central, and Ivy. You can also use local
     * repositories or define your own remote repositories. The code below defines
     * JCenter as the repository Gradle should use to look for its dependencies.
     */

    repositories {
        jcenter()
    }

    /**
     * The dependencies {} block configures the dependencies Gradle needs to use
     * to build your project. The following line adds Android Plugin for Gradle
     * version 2.0.0 as a classpath dependency.
     */

    dependencies {
        classpath 'com.android.tools.build:gradle:2.0.0'
    }
}

/**
 * The allprojects {} block is where you configure the repositories and
 * dependencies used by all modules in your project, such as third-party plugins
 * or libraries. Dependencies that are not required by all the modules in the
 * project should be configured in module-level build.gradle files. For new
 * projects, Android Studio configures JCenter as the default repository, but it
 * does not configure any dependencies.
 */

allprojects {
   repositories {
       jcenter()
   }
}
```

### The Module-level build.gradle
位于模块下的`build.gradle`，定义的是当前模块的配置，可以在其中定义专属于该模块的`build types、product flavors`等等，Example:
```
/**
 * The first line in the build configuration applies the Android plugin for
 * Gradle to this build and makes the android {} block available to specify
 * Android-specific build options.
 */

apply plugin: 'com.android.application'

/**
 * The android {} block is where you configure all your Android-specific
 * build options.
 */

android {

  /**
   * compileSdkVersion specifies the Android API level Gradle should use to
   * compile your app. This means your app can use the API features included in
   * this API level and lower.
   *
   * buildToolsVersion specifies the version of the SDK build tools, command-line
   * utilities, and compiler that Gradle should use to build your app. You need to
   * download the build tools using the SDK Manager.
   */

  compileSdkVersion 23
  buildToolsVersion "23.0.3"

  /**
   * The defaultConfig {} block encapsulates default settings and entries for all
   * build variants, and can override some attributes in main/AndroidManifest.xml
   * dynamically from the build system. You can configure product flavors to override
   * these values for different versions of your app.
   */

  defaultConfig {

    /**
     * applicationId uniquely identifies the package for publishing.
     * However, your source code should still reference the package name
     * defined by the package attribute in the main/AndroidManifest.xml file.
     */

    applicationId 'com.example.myapp'

    // Defines the minimum API level required to run the app.
    minSdkVersion 14

    // Specifies the API level used to test the app.
    targetSdkVersion 23

    // Defines the version number of your app.
    versionCode 1

    // Defines a user-friendly version name for your app.
    versionName "1.0"
  }

  /**
   * The buildTypes {} block is where you can configure multiple build types.
   * By default, the build system defines two build types: debug and release. The
   * debug build type is not explicitly shown in the default build configuration,
   * but it includes debugging tools and is signed with the debug key. The release
   * build type applies Proguard settings and is not signed by default.
   */

  buildTypes {

    /**
     * By default, Android Studio configures the release build type to enable code
     * shrinking, using minifyEnabled, and specifies the Proguard settings file.
     */

    release {
        minifyEnabled true // Enables code shrinking for the release build type.
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
  }

  /**
   * The productFlavors {} block is where you can configure multiple product
   * flavors. This allows you to create different versions of your app that can
   * override defaultConfig {} with their own settings. Product flavors are
   * optional, and the build system does not create them by default. This example
   * creates a free and paid product flavor. Each product flavor then specifies
   * its own application ID, so that they can exist on the Google Play Store, or
   * an Android device, simultaneously.
   */

  productFlavors {
    free {
      applicationId 'com.example.myapp.free'
    }

    paid {
      applicationId 'com.example.myapp.paid'
    }
  }
}

/**
 * The dependencies {} block in the module-level build configuration file
 * only specifies dependencies required to build the module itself.
 */

dependencies {
    compile project(":lib")
    compile 'com.android.support:appcompat-v7:22.0.1'
    compile fileTree(dir: 'libs', include: ['*.jar'])
}

```

## Configure Signing Settings
```
android {
    ...
    defaultConfig {...}
    signingConfigs {
        release {
            srtoreFile file("myreleasekey.keystore")
            storePassword "password"
            keyAlias "myreleasekey"
            keyPassword "password"
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
```
**To generate a signed APK, select `Build > Generate Signed APK `from the main menu**
如果想获取系统的密码则：
```
storePassword System.getenv("KSTOREPWD")
keypassword System.getenv("KEYPWD")
```
如果想在构建的时候由用户在命令行动态输入密码则：
```
storePassword System.console().readLine("\nKeystore password: ")
keypassword System.console().readLine("\nKey password:")
```
