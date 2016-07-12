# 给Apk签名

## 产生私钥
```
keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000
```

## 用私钥进行签名
```
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore myapp.apk alias_name
```

## 校验APK是否被签名
```
jarsigner -verify -verbose -certs my_application.apk
```

## 对最终的APK进行zipalign
```
zipalign -v 4 my-unaligned.apk my_final.apk
```
