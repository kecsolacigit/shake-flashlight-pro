rootProject.name = "ShakeFlashlightPro"
include ':app'
# shake-flashlight-pro
buildscript {
    repositories {
        google()
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:8.2.2'
    }
}

allprojects {
    repositories {
        google()
        mavenCentral()
    }
}
plugins {
    id 'com.android.application'
}

android {
    namespace 'com.shake.flashlight'
    compileSdk 34

    defaultConfig {
        applicationId "com.shake.flashlight"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }

    buildTypes {
        release {
            minifyEnabled false
        }
    }
}

dependencies {
}
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.shake.flashlight">
package com.shake.flashlight;

import android.app.Service;
import android.content.Context;
import android.content.Intent;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.hardware.camera2.CameraManager;
import android.os.IBinder;
import android.os.Vibrator;

public class ShakeService extends Service implements SensorEventListener {

    private SensorManager sensorManager;
    private Sensor accelerometer;
    private CameraManager cameraManager;
    private String cameraId;
    private boolean flashlightOn = false;
    private static final float SHAKE_THRESHOLD = 15.0f;

    @Override
    public void onCreate() {
        super.onCreate();

        sensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
        accelerometer = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
        sensorManager.registerListener(this, accelerometer, SensorManager.SENSOR_DELAY_NORMAL);

        cameraManager = (CameraManager) getSystemService(Context.CAMERA_SERVICE);

        try {
            cameraId = cameraManager.getCameraIdList()[0];
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        return START_STICKY;
    }

    @Override
    public void onSensorChanged(SensorEvent event) {
        float x = event.values[0];
        float y = event.values[1];
        float z = event.values[2];

        float acceleration = (float) Math.sqrt(x*x + y*y + z*z);

        if (acceleration > SHAKE_THRESHOLD) {
            toggleFlashlight();
        }
    }

    private void toggleFlashlight() {
        try {
            flashlightOn = !flashlightOn;
            cameraManager.setTorchMode(cameraId, flashlightOn);

            Vibrator vibrator = (Vibrator) getSystemService(VIBRATOR_SERVICE);
            if (vibrator != null) {
                vibrator.vibrate(100);
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }
}

    <uses-permission android:name="android.permission.CAMERA"/>
    <uses-permission android:name="android.permission.FLASHLIGHT"/>
    <uses-permission android:name="android.permission.VIBRATE"/>
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

    <applicationpackage com.shake.flashlight;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;

public class BootReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        if (Intent.ACTION_BOOT_COMPLETED.equals(intent.getAction())) {
            Intent serviceIntent = new Intent(context, ShakeService.class);
            context.startService(serviceIntent);
        }
    }
}

        android:allowBackup="true"
        android:label="Shake Flashlight Pro"
        android:theme="@android:style/Theme.DeviceDefault.NoActionBar">

        <receiver
            android:name=".BootReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
            </intent-filter>name: Android Build

on:
  push:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Grant permission
        run: chmod +x ./gradlew

      - name: Build Debug APK
        run: ./gradlew assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: app-debug
          path: app/build/outputs/apk/debug/app-debug.apk

        </receiver>

        <service
            android:name=".ShakeService"
            android:enabled="true"
            android:exported="false"/>

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>

    </application>

</manifest>
package com.shake.flashlight;

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;

public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        Button button = new Button(this);
        button.setText("Szolgáltatás indítása");

        button.setOnClickListener(v -> {
            startService(new Intent(this, ShakeService.class));
        });

        setContentView(button);
    }
}
