# Grandroid2
[![](https://jitpack.io/v/claireliu14/Grandroid2.svg)](https://jitpack.io/#claireliu14/Grandroid2)

Grandroid2 是一個開源的 Android Library。基於 [Grandroid](https://github.com/Grasea/Grandroid) 重新整合，更合理的拆分成多個Dependency，新版將更輕量化。

Grandroid2 is a open source library for Android. More stronger than [Grandroid](https://github.com/Grasea/Grandroid).

## 功能 Features
**Grandroid2 包含 Modules：**

 1. **Core**：開發上常用之核心物件。

 2. **MVP-Framework（使用 Annotation 技術）**：利用 Annotation 技術 Presenter 及 View 之間的 setup 更加快速及簡便。

 3. **Bluetooth LE Library（使用 Annotation 技術）**：利用 Annotation 技術，快速實現 BLE Device 搜尋、裝置連接、監聽資料以及發送資料。

 4. **ORM Database**：簡單易用的ORM Database library。

## 如何使用 Usage
Step1. Add Repository in build.gradle:

```
	allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
```

Step2. Add dependency in build.gradle what library you want.


### Core引用了
1. com.android.support:appcompat-v7:26.0.2
2. com.android.support:recyclerview-v7:26.0.2

```
	dependencies {
	        implementation 'com.github.claireliu14.Grandroid2:grandroid-core:latest-version'
	}
```
### MVP-Framework引用了
1. grandroid-core
2. com.android.support:appcompat-v7:26.0.2
3. com.mcxiaoke.volley:library:1.0.+

```
	dependencies {
	        implementation 'com.github.claireliu14.Grandroid2:grandroid-mvp-framework:latest-version'
	}
```
### Bluetooth LE Library引用了
1. com.android.support:appcompat-v7:26.0.2

```
	dependencies {
	        implementation 'com.github.claireliu14.Grandroid2:grandroid-ble:latest-version'
	}
```
**Add this line to AndroidManifest.**
```
	<service android:name="com.grasea.grandroid.ble.BluetoothLeService"></service>
```
**Init at Activity or Application onCreate().**
```
        GrandroidBle.init(this, new GrandroidConnectionListener() {
            @Override
            public void onGattDiscovered(BleDevice controller) {
                super.onGattDiscovered(controller);
            }

            @Override
            public void onDeviceReady(@Nullable BleDevice controller) {
                //On connected device.
                ArrayList<GattServiceChannelHandler> serviceHandlers = controller.getServiceHandlers();
                address = controller.getAddress();
                handShakeProtocol = new HandShakeProtocol(address, userCode);
                GrandroidBle.getInstance().getDeviceScanner().stopScan();
                GrandroidBle.getInstance().bindName(handShakeProtocol);
                GrandroidBle.getInstance().bind(address, protocolObject);
            }

            @Override
            public void onDeviceDisconnected(@Nullable BleDevice controller) {
                GrandroidBle.getInstance().getDeviceScanner().startScan();
            }

            @Override
            public void onFailed(int errorCode) {
            }
        });
```


**Scan device**
```
        GrandroidBle.getInstance().getDeviceScanner()
                .setRetry(10)
                .filterName("Device Name")
                .setScanResultHandler(new AutoConnectScanResultHandler(60000) {
                    @Override
                    public void onDeviceFailed(int errorCode) {
                        Config.logi("onDeviceScan Filed:" + errorCode);
                    }
                }).startScan();
   
        // Then do something after scan at GrandroidConnectionListener if GrandroidBle found device you want.
```
**Listen notify characteristic**
```
    public class BleProtocol {
        public static final String serviceUUID = "49535343-fe7d-4ae5-8fa9-9fafd205e455";
        public static final String rUUID = "49535343-1E4D-4BD9-BA61-23C647249616";
        public static final String wUUID = "49535343-8841-43f4-a8d4-ecbe34729bb3";

        public BleProtocol(String deviceAddress) {
            GrandroidBle.getInstance().bind(deviceAddress, this);
        }

        @OnBleDataReceive(serviceUUID = serviceUUID, characteristicUUID = rUUID)
        public void onReceiveData(byte[] data, String fromWhatCharacteristicUUID) {
            //Single characteristic.
        }

        @OnBleDataReceive(serviceUUID = serviceUUID, characteristicUUIDs = {rUUID, wUUID})
        public void onReceiveData(byte[] data, String fromWhatCharacteristicUUID) {
            //Mutiple characteristics.
        }
    }
```
**Send data.**
```
        try {
            GrandroidBle.with(deviceAddress).findService(serviceUUID).getChannel(wUUID).send(loadUserDataProtocol);
        } catch (NullPointerException e) {
            // ...
        }
```
or
```
        try {
            GrandroidBle.with(deviceAddress).send(serviceUUID, wUUID, loadUserDataProtocol);
        } catch (NullPointerException e) {
	    // ...
        }
```
### ORM Database引用了
1. grandroid-core

```
	dependencies {
	        implementation 'com.github.claireliu14.Grandroid2:grandroid-database:latest-version'
	}
```
