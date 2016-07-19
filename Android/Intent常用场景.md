# Intent 常用场景

## Google搜索内容：
```
Intent intent = new Intent();
intent.setAction(Intent.ACTION_WEB_SEARCH);
intent.putExtra(SearchManager.QUERY,"searchString")
startActivity(intent);

```

## 浏览网页
```
Uri uri =Uri.parse("http://www.google.com");
Intent it = new Intent(Intent.ACTION_VIEW,uri);
startActivity(it);

```

## 显示地图
```
Uri uri = Uri.parse("geo:38.899533,-77.036476");
Intent it = newIntent(Intent.Action_VIEW,uri);
startActivity(it);

```

## 拨打电话
```
Uri uri =Uri.parse("tel:xxxxxx");
Intent it = new Intent(Intent.ACTION_DIAL,uri);  
startActivity(it);

```

## 调用发短信的程序
```
Intent it = newIntent(Intent.ACTION_VIEW);   
it.putExtra("sms_body", "TheSMS text");   
it.setType("vnd.android-dir/mms-sms");   
startActivity(it);

```

## 发送短信
```
Uri uri =Uri.parse("smsto:0800000123");   
Intent it = newIntent(Intent.ACTION_SENDTO, uri);   
it.putExtra("sms_body", "TheSMS text");   
startActivity(it);

String body="this is sms demo";
Intent mmsintent = newIntent(Intent.ACTION_SENDTO, Uri.fromParts("smsto", number, null));
mmsintent.putExtra(Messaging.KEY_ACTION_SENDTO_MESSAGE_BODY,body);
mmsintent.putExtra(Messaging.KEY_ACTION_SENDTO_COMPOSE_MODE,true);
mmsintent.putExtra(Messaging.KEY_ACTION_SENDTO_EXIT_ON_SENT,true);
startActivity(mmsintent);

```

## 发送彩信
```
Uri uri =Uri.parse("content://media/external/images/media/23");   
Intent it = newIntent(Intent.ACTION_SEND);   
it.putExtra("sms_body","some text");   
it.putExtra(Intent.EXTRA_STREAM, uri);   
it.setType("image/png");   
startActivity(it);
StringBuilder sb = new StringBuilder();
sb.append("file://");
sb.append(fd.getAbsoluteFile());
Intent intent = newIntent(Intent.ACTION_SENDTO, Uri.fromParts("mmsto", number, null));
// Below extra datas are all optional.
intent.putExtra(Messaging.KEY_ACTION_SENDTO_MESSAGE_SUBJECT,subject);
intent.putExtra(Messaging.KEY_ACTION_SENDTO_MESSAGE_BODY,body);
intent.putExtra(Messaging.KEY_ACTION_SENDTO_CONTENT_URI,sb.toString());
intent.putExtra(Messaging.KEY_ACTION_SENDTO_COMPOSE_MODE,composeMode);
intent.putExtra(Messaging.KEY_ACTION_SENDTO_EXIT_ON_SENT,exitOnSent);
startActivity(intent);

```

## 发送Email
```
Uri uri =Uri.parse("mailto:xxx@abc.com");
Intent it = newIntent(Intent.ACTION_SENDTO, uri);
startActivity(it);
Intent it = new Intent(Intent.ACTION_SEND);   
it.putExtra(Intent.EXTRA_EMAIL,"me@abc.com");   
it.putExtra(Intent.EXTRA_TEXT, "Theemail body text");   
it.setType("text/plain");   
startActivity(Intent.createChooser(it,"Choose Email Client"));

Intent it=new Intent(Intent.ACTION_SEND);     
String[] tos={"me@abc.com"};     
String[]ccs={"you@abc.com"};     
it.putExtra(Intent.EXTRA_EMAIL, tos);     
it.putExtra(Intent.EXTRA_CC, ccs);     
it.putExtra(Intent.EXTRA_TEXT, "Theemail body text");     
it.putExtra(Intent.EXTRA_SUBJECT, "Theemail subject text");     
it.setType("message/rfc822");     
startActivity(Intent.createChooser(it,"Choose Email Client"));   


Intent it = newIntent(Intent.ACTION_SEND);   
it.putExtra(Intent.EXTRA_SUBJECT, "Theemail subject text");    
it.putExtra(Intent.EXTRA_STREAM,"file:///sdcard/mysong.mp3");   
sendIntent.setType("audio/mp3");   
startActivity(Intent.createChooser(it,"Choose Email Client"));

```

## 播放多媒体
```
Intent it = new Intent(Intent.ACTION_VIEW);
Uri uri =Uri.parse("file:///sdcard/song.mp3");
it.setDataAndType(uri,"audio/mp3");
startActivity(it);

Uri uri =Uri.withAppendedPath(MediaStore.Audio.Media.INTERNAL_CONTENT_URI,"1");   
Intent it = new Intent(Intent.ACTION_VIEW,uri);   
startActivity(it);

```

## 卸载应用
```
Uri uri =Uri.fromParts("package", strPackageName, null);   
Intent it = newIntent(Intent.ACTION_DELETE, uri);   
startActivity(it);

```

##　安装应用
```
Uri installUri = Uri.fromParts("package","xxx", null);
returnIt = newIntent(Intent.ACTION_PACKAGE_ADDED, installUri);

```

## 打开照相机
```
<1>
Intent i = new Intent(Intent.ACTION_CAMERA_BUTTON, null);
this.sendBroadcast(i);

<2>
long dateTaken = System.currentTimeMillis();
String name = createName(dateTaken) + ".jpg";
fileName = folder + name;
ContentValues values = new ContentValues();
values.put(Images.Media.TITLE, fileName);
values.put("_data", fileName);
values.put(Images.Media.PICASA_ID, fileName);
values.put(Images.Media.DISPLAY_NAME, fileName);
values.put(Images.Media.DESCRIPTION, fileName);
values.put(Images.ImageColumns.BUCKET_DISPLAY_NAME, fileName);
Uri photoUri = getContentResolver().insert(
          MediaStore.Images.Media.EXTERNAL_CONTENT_URI,values);


Intent inttPhoto = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
inttPhoto.putExtra(MediaStore.EXTRA_OUTPUT, photoUri);
startActivityForResult(inttPhoto, 10);

```

## 选取图片
```
Intent i = new Intent();
i.setType("image/*");
i.setAction(Intent.ACTION_GET_CONTENT);
startActivityForResult(i, 11);

```

## 打开录音机
```
Intent mi = new Intent(Media.RECORD_SOUND_ACTION);
startActivity(mi);

```

## 打开联系人列表
```
<1>           
Intent i = new Intent();
i.setAction(Intent.ACTION_GET_CONTENT);
i.setType("vnd.android.cursor.item/phone");
startActivityForResult(i, REQUEST_TEXT);

<2>
Uri uri = Uri.parse("content://contacts/people");
Intent it = new Intent(Intent.ACTION_PICK, uri);
startActivityForResult(it, REQUEST_TEXT);

```

## 调用系统编辑添加联系人（高版本SDK有效）
```
Intent it = newIntent(Intent.ACTION_INSERT_OR_EDIT);
it.setType("vnd.android.cursor.item/contact");
//it.setType(Contacts.CONTENT_ITEM_TYPE);
it.putExtra("name","myName");
it.putExtra(android.provider.Contacts.Intents.Insert.COMPANY,  "organization");
it.putExtra(android.provider.Contacts.Intents.Insert.EMAIL,"email");
it.putExtra(android.provider.Contacts.Intents.Insert.PHONE,"homePhone");
it.putExtra(android.provider.Contacts.Intents.Insert.SECONDARY_PHONE,"mobilePhone");
it.putExtra(  android.provider.Contacts.Intents.Insert.TERTIARY_PHONE,"workPhone");
it.putExtra(android.provider.Contacts.Intents.Insert.JOB_TITLE,"title");
startActivity(it);

```

## 调用系统编辑添加联系人(全有效)
```
Intent intent = newIntent(Intent.ACTION_INSERT_OR_EDIT);
intent.setType(People.CONTENT_ITEM_TYPE);
intent.putExtra(Contacts.Intents.Insert.NAME, "My Name");
intent.putExtra(Contacts.Intents.Insert.PHONE, "+1234567890");
intent.putExtra(Contacts.Intents.Insert.PHONE_TYPE,Contacts.PhonesColumns.TYPE_MOBILE);
intent.putExtra(Contacts.Intents.Insert.EMAIL, "com@com.com");
intent.putExtra(Contacts.Intents.Insert.EMAIL_TYPE, Contacts.ContactMethodsColumns.TYPE_WORK);
startActivity(intent);

```
