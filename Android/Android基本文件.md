# Android基本文件

1. `android_filesystem_config.h`
>定义了系统`UID`，`GID`的映射关系

2. `/data/system/packages.list`
>存放系统中安装的应用的信息

3. `/data/system/packages.xml`
>存放系统中安装的应用的具体的信息

4. `/etc/permission/platform.xml`
>内置的权限映射定义

5. `/data/system/urigrants.xml`
>权限持久化保存至该文件中

6. `system/vendor/app`
>设备制造商指定的应用

7. `/data/dalvik-cache/`
>存放每个非系统应用的优化过后的dex文件，对于所有用户都是共享的

8. `/system/framework/framework-res.apk`

9. `/data/app/`
>存放非系统应用

10. `/data/app-lib/`
>app中的native库文件

11. `/data/app-asec/`
>加密apk的存储,以.asec形式存储

12. `/data/system/users/<user ID>`
>每个用户的目录

13. `/data/system/users/userlists.xml`
>所有user的信息

14. `/data/system/users/<user ID>/package_restrictions.xml`
>存储相应用户所安装的应用信息

15. `/data/user/<user ID>/`
>该用户下应用的data目录

16. `/proc/PID/mounts`
>每个进程的挂载列表

17. `/data/media`
>实际的内部sdcard目录，存储在sdcard上的内容实际都是存储在这里

18. `/mnt/shell/emulated`
>模拟文件系统，属于shell，只能被shell进程访问

19. `/sdcard`
>`storage/emulated/lagacy`的软链接

20. `/storage/emulated/0`
>该路径被`bind mount`到`/storage/emulated/legacy`

21. `/mnt/shell/emulated/0`
>该路径被`bind mount`到`/storage/emulated/0`

22. `/system/etc/security/cacerts.bks`
>包含了Android信任的所有的CA证书

23. `/data/misc/keychain/pubkey_blacklist.txt`、`/data/misc/keychain/serial_blacklist.txt`
>存放黑名单

24. `/data/misc/keystore/`
>`keystore service`的文件

25. `/data/data/com.android.keychain/databases/grants.db`
>已授权访问key的对应数据库

26. `/data/system/users/<user ID>/accounts.db`
>账户数据库

27. `/data/system/device-policies.xml`
>主用户的的策略

28. `/data/users/<user ID>/device-policies.xml`
>对应用户的策略

29. `/data/misc/vpm/state`
>`vpn daemon`的state文件

30. `/data/misc/wifi`
>`Wi-fi`相关的配置文件
