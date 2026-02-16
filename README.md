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

    <uses-permission android:name="android.permission.CAMERA"/>
    <uses-permission android:name="android.permission.FLASHLIGHT"/>
    <uses-permission android:name="android.permission.VIBRATE"/>
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

    <application
        android:allowBackup="true"
        android:label="Shake Flashlight Pro"
        android:theme="@android:style/Theme.DeviceDefault.NoActionBar">

        <receiver
            android:name=".BootReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
            </intent-filter>
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
