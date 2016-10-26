---
layout: post
title:  "如何唯一的标识一台Android设备？"
date:   2016-10-24 15:08:05
categories: Android
excerpt: 
---

* content
{:toc}

---

### 唯一的标识一台Android设备

#### 几个概念：

* UUID : (Universally Unique Identifier)全局唯一标识符,是指在一台机器上生成的数字，它保证对在同一时空中的所有机器都是唯一的。由以下几部分的组合：当前日期和时间(UUID的第一个部分与时间有关，如果你在生成一个UUID之后，过几秒又生成一个UUID，则第一个部分不同，其余相同)，时钟序列，全局唯一的IEEE机器识别号（如果有网卡，从网卡获得，没有网卡以其他方式获得）。

* IMEI : (International Mobile Equipment Identity) 是国际移动设备身份码的缩写，国际移动装备辨识码，是由15位数字组成的"电子串号"，它与每台手机一一对应，而且该码是全世界唯一的。

* MEID :（ Mobile Equipment IDentifier ）是全球唯一的56bit CDMA制式移动终端标识号。标识号会被烧入终端里，并且不能被修改。可用来对CDMA制式移动式设备进行身份识别和跟踪。

> 两者的区别在于：IMEI是手机的身份证，MEID是CDMA制式（电信运营的）的专用身份证；IMEI是15位，MEID是14位。

* DEVICE_ID

根据不同的手机设备返回IMEI，MEID或者ESN码，可以根据以下代码获得：
        
        device_id = ((TelephonyManager)context.getSystemService(Context.TELEPHONY_SERVICE)).getDeviceId();

    缺陷：
    - 非手机设备：最开始搭载Android系统都手机设备，而现在也出现了非手机设备：如平板电脑、电视、音乐播放器等。这些设备没有通话的硬件功能，系统中也就没有TELEPHONY_SERVICE，自然也就无法通过上面的方法获得DEVICE_ID。
    - 权限问题：获取DEVICE_ID需要READ_PHONE_STATE权限，在Android 6.0上使用运行时动态授予权限的机制，一旦用户不给予授权，将获取不到DEVICE_ID。 
    - 厂商定制系统中的Bug：少数手机设备上，由于该实现有漏洞，会返回垃圾，如:zeros或者asterisks。

    补充：
    MEID：Mobile Equipment IDentifier（MEID）是全球唯一的56bit移动终端标识号。标识号会被烧入终端里，并且不能被修改。可用来对移动式设备进行身份识别和跟踪。MEID主要分配给CDMA制式的手机。
    IMEI：IMEI(International Mobile Equipment Identity)是国际移动设备身份码的缩写，国际移动装备辨识码，是由15位数字组成的"电子串号"，它与每台手机一一对应。IMEI码由GSM（全球移动通信协会）统一分配。
    ESN：ESN是电子序列号Electronic Serial Number的缩写。它是一个32bits长度的参数，是手机的惟一标识。

* MAC ADDRESS

        WifiManager wm = (WIfiManager)Context.getsystemService(Context.WIFI_SERVICE);
        return wm.getConnectionInfo().getMacAddress();

        可以使用手机WiFi或蓝牙的MAC地址作为设备标识，但是并不推荐这么做，原因有以下两点：
        - 硬件限制：并不是所有的设备都有WiFi和蓝牙硬件，硬件不存在自然也就得不到这一信息。
        - 获取的限制：如果WiFi没有打开过，是无法获取其Mac地址的；而蓝牙是只有在打开的时候才能获取到其Mac地址。

* Serial Number

硬件序列，在Android 2.2 以上可以通过 android.os.Build.SERIAL 获得序列号。在一些没有电话功能的设备会提供，某些手机上也可能提供（所以就是经常会返回Unknown）

* ANDROID_ID

ANDROID_ID是设备第一次启动时产生和存储的64bit的一个数，当设备被wipe后该数重置。

        androidID = "" + Settings.Secure.getString(context.getContentResolver(), Settings.Secure.ANDROID_ID);

    问题:
    ANDROID_ID似乎是获取Device ID的一个好选择，但它也有缺陷：在主流厂商生产的设备上，有一个很经常的bug，就是每个设备都会产生相同的ANDROID_ID：9774d56d682e549c 。同时刷机，或者重置ANDROID_ID的值都会变化。

---

#### 实际的标识方法

* deviceId:

        public static String getDeviceId(Context context){

            String deviceid_key = "share_prefrence_device_id";
            MySharedPreference sp = new MySharedPreference(context);
            String deviceIdString = sp.getKeyStr(deviceid_key);

            if("".equals(deviceIdString)){
                String device_id = null;
                boolean random = false;
                try {
                    device_id = ((TelephonyManager)context.getSystemService(Context.TELEPHONY_SERVICE)).getDeviceId();
                }catch (Exception e){
                    e.printStackTrace();
                    random = true;
                    device_id = new Random().nextInt(16)+"";
                }
                String device_id_md5 = MD5Util.MD5Encode(device_id, "UTF-8");
                if(random){
                    device_id_md5 = "rd_"+device_id_md5;
                }
                sp.setKeyStr(deviceid_key, device_id_md5);
                return device_id_md5;
            }else{
                return deviceIdString;
            }
        }

* Universal ID/Android ID
        
        public static String getDeviceId(Context context){

            String deviceid_key = "share_prefrence_device_id";
            MySharedPreference sp = new MySharedPreference(context);
            String deviceIdString = sp.getKeyStr(deviceid_key);

            if ("".equals(deviceIdString)) {
                String androidID;
                try {
                    androidID = "" + Settings.Secure.getString(context.getContentResolver(), Settings.Secure.ANDROID_ID);
                    if (!"9774d56d682e549c".equals(androidID)) {
                        deviceIdString = UUID.nameUUIDFromBytes(androidID.getBytes("utf8")).toString();
                    }else {
                        deviceIdString = UUID.randomUUID().toString();
                    }

                }catch (Exception e) {
                    e.printStackTrace();
                    deviceIdString = UUID.randomUUID().toString();
                }
                FLDLog.e("deviceid----->" + deviceIdString);
                String device_id_md5 = MD5Util.MD5Encode(deviceIdString, "UTF-8");
                sp.setKeyStr(deviceid_key, device_id_md5);
                return device_id_md5;
            }else {
                FLDLog.e("deviceid----->" + deviceIdString);
                return deviceIdString;
            }
        }

---

#### UUID操作类

        public class UniversalID {

            private static String filePath = File.separator + "HappyShopping" + File.separator + "comm";

            public static String getUniversalID(Context context) {
                String androidId;
                String fileRootPath = getPath(context) + filePath;
                String uuid = FileUtils.readFile(fileRootPath);
                if (uuid == null || uuid.equals("")) {
                    androidId = "" + Settings.Secure.getString(context.getContentResolver(),
                            Settings.Secure.ANDROID_ID);
                    try {
                        if (!"9774d56d682e549c".equals(androidId)) {
                            uuid = UUID.nameUUIDFromBytes(androidId.getBytes("utf8")).toString();
                        } else {
                            FLDLog.e("uuid==9774d56d682e549c----->true");
                            uuid = UUID.randomUUID().toString();
                        }
                    } catch (Exception e) {
                        uuid = UUID.randomUUID().toString();
                    }

                    String uuid_md5 = MD5Util.MD5Encode(uuid, "UTF-8");
                    if(!uuid.equals("")){
                        saveUUID(context,uuid_md5);
                    }

                    return uuid_md5;
                }else {
                    return uuid;
                }
            }

            public static void saveUUID(Context context, String UUID) {
                String ExternalSdCardPath = getExternalSdCardPath() + filePath;
                FileUtils.writeFile(ExternalSdCardPath, UUID);
                String InnerPath = context.getFilesDir().getAbsolutePath() + filePath;
                FileUtils.writeFile(InnerPath,UUID);
            }

            public static String getPath(Context context) {
                //首先判断是否有外部存储卡，如没有判断是否有内部存储卡，如没有，继续读取应用程序所在存储
                String phonePicsPath = getExternalSdCardPath();
                if (phonePicsPath == null) {
                    phonePicsPath = context.getFilesDir().getAbsolutePath();
                }
                return phonePicsPath;
            }

            /**
             * 遍历 "system/etc/vold.fstab” 文件，获取全部的Android的挂载点信息
             *
             * @return
             */
            private static ArrayList<String> getDevMountList() {
                String[] toSearch = FileUtils.readFile("/system/etc/vold.fstab").split(" ");
                ArrayList<String> out = new ArrayList<>();
                for (int i = 0; i < toSearch.length; i++) {
                    if (toSearch[i].contains("dev_mount")) {
                        if (new File(toSearch[i + 2]).exists()) {
                            out.add(toSearch[i + 2]);
                        }
                    }
                }
                return out;
            }

            /**
             * 获取扩展SD卡存储目录
             * <p/>
             * 如果有外接的SD卡，并且已挂载，则返回这个外置SD卡目录
             * 否则：返回内置SD卡目录
             *
             * @return
             */
            public static String getExternalSdCardPath() {

                if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
                    File sdCardFile = new File(Environment.getExternalStorageDirectory().getAbsolutePath());
                    return sdCardFile.getAbsolutePath();
                }

                String path = null;

                File sdCardFile = null;

                ArrayList<String> devMountList = getDevMountList();

                for (String devMount : devMountList) {
                    File file = new File(devMount);

                    if (file.isDirectory() && file.canWrite()) {
                        path = file.getAbsolutePath();

                        String timeStamp = new SimpleDateFormat("ddMMyyyy_HHmmss").format(new Date());
                        File testWritable = new File(path, "test_" + timeStamp);

                        if (testWritable.mkdirs()) {
                            testWritable.delete();
                        } else {
                            path = null;
                        }
                    }
                }

                if (path != null) {
                    sdCardFile = new File(path);
                    return sdCardFile.getAbsolutePath();
                }

                return null;
            }

        }


---

> 参考文章：[如何唯一的标识一台Android设备？](http://www.jianshu.com/p/178786f833b6)

---

### 结语：

坚持每天进步一点点...

---