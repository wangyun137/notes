# Android DSL

## Configuration Blocks
下面列出来的是可以在`android{}`范围内定义的`Block`：

+ **`defaultConfig{}`**       默认的配置选项，会被所有`Product Flavors`继承
+ **`aaptOptions{}`**         `appt`配置选项
+ **`adbOptions{}`**          `adb`配置选项
+ **`buildTypes{}`**          `Build Types`配置选项
+ **`compileOptions{}`**      `Compile`的配置选项
+ **`dataBinding{}`**         `Data Binding`的配置选项
+ **`dexOptions{}`**          `dex`的配置选项
+ **`jacoco{}`**              `JaCoCo`的配置选项
+ **`lintOptions{}`**         `lint`配置选项
+ **`packageingOptions{}`**   `packaging`配置选项
+ **`profuctFlavors{}`**      `Product Flavors`的配置选项
+ **`signingConfigs{}`**      `Signing`的配置选项
+ **`sourceSets{}`**          配置`Source Set`
+ **`splits{}`**              配置`Apk splits`
+ **`testOptions {}`**        配置`test options`

### defautlConfig
在这个`Block`中可以定义的属性有：

+ **`applicationId`**                        -->  应用的`application ID`, **应用的包名是用来在代码内部使用`R`以及确定各个类的位置， `applicationId`是用来在设备以及Googl Play上区分app的**
+ **`consumerProguardFiles`**                -->  将`ProGuard`文件包含在发布的`AAR`中
+ **`dimension`**                            -->  Name of the dimension this product flavor belongs to
+ **`jackOptions`**                          -->  编译`jack`的选项
+ **`manifestPlaceholders`**                 -->  The manifest placeholders， 同`Manifest Merge`相关
+ **`multiDexEnabled`**                      -->  是否开启`Multi-Dex`
+ **`proguardFiles`**                        -->  指明要使用的`proguard files`， **Using `getDefaultProguardFile(String filename)`will return the full path to the files**
+ **`signingConfig`**                        -->  签名选项
+ **`testApplicationId`**                    -->  Test application ID
+ **`testFunctionalTest`**                   -->  `instrument`相关
+ **`testHandleProfiling`**                  -->  `instrument`相关
+ **`testInstrumentationRunner`**            -->  Test instrumentation class name
+ **`testInstrumentationRunnerArguments`**   -->  Test instrumentation runner custom arguments. e.g. [key: "value"] will give adb shell am instrument -w -e key value com.example...".
+ **`vectorDrawable`**                       -->  Options to configure the build-time support for vector drawables
+ **`versionCode`**                          -->  版本号
+ **`versionName`**                          -->  版本名称

在这个`Block`中可以使用的`Method`有：

+ **`buildConfigField(type, name, value)`**      -->  添加一个新的属性到`BuildConfig`
+ **`consumerProguardFile(proguradFile)`**       -->  添加一个`progurad file`到`AAR`中
+ **`consumerProguardFiles(proguradFiles)`**     -->  添加多个`proguard file`到`AAR`中
+ **`maxSdkVersion(targetSdkVersion)`**          -->  设置最大的SDK版本
+ **`minSdkVersion(targetSdkVersion)`**          -->  设置最小的SDK版本
+ **`proguardFile(proguardFile)`**               -->  添加一个新的`Proguard file`
+ **`proguradFiles(proguradFiles)`**             -->  添加多个新的`Proguard file`
+ **`resConfig(config)`**                        -->  添加一个`resource configuration filter`
+ **`resConfigs(config)`**                       -->  添加多个`resource configuration filter`
+ **`resValue(type, name, value)`**              -->  添加一个新的`resource`
+ **`setConsumerProguardFiles(proguardFileIterable)`** -->  指定一个`proguard file`并添加到`AAR`中
+ **`setProguardFiles(proguardFileIterable)`**   -->  设置`proguard file`
+ **`setTestProguardFiles(files)`**              -->  指定执行`test code`时的`proguard file`
+ **`targetSdkVersion(targetSdkVersion)`**       -->  设置`target SDK version`
+ **`testInstrumentationRunnerArgument(key, value)`**  -->   Adds a custom argument to the test instrumentation runner
+ **`testInstrumentationRunnerArguments(key, value)`** -->   Adds custom arguments to the test instrumentation runner
+ **`testProguardFile(proguardFile)`**           -->  添加一个执行`test code`时的`proguard file`
+ **`testProguardFiles(proguardFiles)`**         -->  添加多个执行`test code`时的`proguard file`

### BuildType
在这个`Block`中可以定义的属性有:

+ **`applicationIdSuffix`**                 -->  给`applicationId`添加后缀
+ **`consumerProguardFiles`**               -->  包含在`AAR`中的`Proguard rule files`
+ **`debuggable`**                          -->  指明当前`build type`是否产生`debuggable apk`
+ **`embedMicroApp`**                       -->  Whether a linked Android Wear app should be embedded in variant using this build type
+ **`jniDebuggable`**                       -->  指明产生的APK是否包含可调试的`native code`
+ **`manifestPlaceholders`**                -->  The Manifest placegolders
+ **`minifyEnabled`**                       -->  指明`Minify`是否开启
+ **`multiDexEnabled`**                     -->  指明`Multi-Dex`是否开启
+ **`proguardFiles`**                       -->  指明要使用的`Proguard rule files`
+ **`pseudoLocalesEnabled`**                -->  指明是否在APK中产生`pseudo locale`
+ **`renderscriptOptmLevel`**               -->  指明使用`renderscript compiler`时的`Optimization level`
+ **`shrinkResources`**                     -->  指明是否`shrink`不用的资源，默认是`false`
+ **`signingConfig`**                       -->  签名选项
+ **`testCoverageEnabled`**                 -->  指明`test coverage`是否开启
+ **`versionNameSuffix`**                   -->  Version name suffix
+ **`zipAlignEnabled`**                     -->  指明`zipalign`是否开启

可以使用的方法有:
+ **`buildConfigField(type, name, value)`**   
+ **`consumerProguardFile(proguardFile)`**
+ **`consumerProguardFiles(proguardFiles)`**
+ **`proguardFile(proguardFile)`**
+ **`proguardFiles(proguardFiles)`**
+ **`resValue(type, name, value)`**
+ **`setProguardFiles`**
+ **`shrinkResources(flag)`**   -->  指明是否`shrink`不用的资源，默认为`false`


###
