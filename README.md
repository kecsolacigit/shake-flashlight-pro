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
