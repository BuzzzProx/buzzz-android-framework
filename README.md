# Buzzz framework for Android

## Table of Contents

- [Introduction](#introduction)
- [Requirements](#requirements)
- [Installation](#installation)
- [How to set up your project](#how-to-set-up-your-project)
  - [Configure manifest file](#configure-manifest-file)
  - [How to create push notification permission of your project](#how-to-create-push-notification-permission-of-your-project)
  - [Configure main gradle file](#how-to-create-push-notification-permission-of-your-project)
- [How to set up your code](#how-to-set-up-your-code)
  - [Usage example](#usage-example)
- [Framework interfaces](#framework-interfaces)
- [Framework state codes](#framework-state-codes)
  
## Introduction
  A Java based Android framework that communicates to our server via RESTFul API.

## Requirements:

- Android 5.1+
- Gradle 2.3.0
- Google-services 3.0.0


## Installation:
 - Example of the `build.gradle` in app module
 
        apply plugin: 'com.android.application'
    
        android {
            compileSdkVersion 22
            buildToolsVersion '25.0.0'
            defaultConfig {
                compileSdkVersion 26
                defaultConfig {
                    applicationId "com.example.youAppName"
                    minSdkVersion 21
                    targetSdkVersion 22
                    versionCode 1
                    versionName "1.0"
                    multiDexEnabled = true
        
                    testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
                }
                buildTypes {
                    release {
                        minifyEnabled false
                        proguardFiles fileTree(dir: "cproguardfiles", include: ["*.pro"]).asList().toArray()
                    }
                }
                productFlavors {
                }
                repositories {
                    flatDir {
                        dirs 'libs'
                    }
                }
            }
            dependencies {
                compile 'com.squareup.okhttp:okhttp:2.4.0'
                compile 'com.google.code.gson:gson:1.7.2'
                compile 'joda-time:joda-time:2.8'
                compile 'com.android.support:appcompat-v7:22.2.0'
                compile 'com.amazonaws:aws-android-sdk-core:2.2.+'
                compile 'com.amazonaws:aws-android-sdk-s3:2.2.+'
                compile 'com.amazonaws:aws-android-sdk-ddb:2.2.+'
                compile 'com.amazonaws:aws-android-sdk-cognitoidentityprovider:2.2.+'
                compile 'com.amazonaws:aws-android-sdk-cognito:2.2.+'
                compile(name: 'bscanner-release', ext: 'aar')
                compile 'com.android.support:design:22.2.1'
            }
            apply plugin: 'com.google.gms.google-services'
        }
- Download the file *[bscanner](/bscanner-release.aar?at=master)*.
     
- Move library in folder _/libs_ in your project.
## How to set up your project

#### Configure manifest file
 - include the following code into manifest section, for allow access to bluetooth and internet.
    
    
            <!-- Bluetooth permissions and features -->
            <uses-permission android:name="android.permission.BLUETOOTH"/>
            <uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
        
            <uses-feature
                android:name="android.hardware.bluetooth_le"
                android:required="true"/>
            <!-- Permissions to fetch user data over network, contact the proximity beacon service,
                  and access location -->
            <uses-permission android:name="android.permission.INTERNET"/>
            <uses-permission android:name="android.permission.NETWORK"/>
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        
    
    
 - for configure notification theme color and default icon paste the following code in
     application section
     
     
              <meta-data
                  android:name="com.google.firebase.messaging.default_notification_icon"
                  android:resource="@drawable/your_icon" />
              <meta-data
                  android:name="com.google.firebase.messaging.default_notification_color"
                  android:resource="@color/your_color" />
    
     
#### How to create push notification permission of your project
        
   - Open *[console](https://console.firebase.google.com/)*
   - Create project or use existing.
   - After create project, open it and click _add new application_, and download the file `google-services.json`
   - If you have and existing project and application open project and go to project setting at section `your apps` download file `google-services.json`
   - Move the downloaded file to folder with your app
        
   _**Notice** for completely working with firebase notification need to set server key at app config tab on portal.buzzzme.io,
   server key you can see at tab CLOUD MESSAGING on firebase console._
#### Configure main gradle file
   For configure the `build.gradle` in your project, paste/merge the following code in dependencies section 
    
         classpath 'com.android.tools.build:gradle:2.3.0'
         classpath 'com.google.gms:google-services:3.0.0'      
           


##How to set up your code 

1. Import classes `BCP` and `Notifications` from module  `com.example.bscanner`
2. Create and object of library. 
3. Set up notification
4. Launch an object of class `BCP` as fragment 
5. Call the method `init` for set you clientId, and client secret and also framework state handlers. 
 
### Usage example

* import classes
    

         import com.example.bscanner.BCP;
         import com.example.bscanner.Notifications;
         
* create an object of framework
       
     
         private BCP bcp = new BCP();
         
* configure notification and framework at method `onCreate` of your activity 
   
     
     
           Notifications.getInstance(this);
                getFragmentManager().beginTransaction()
                            .add(0, bcp)
                             .commit();
            bcp.init("your_clientID", "your_clientSecret", FrameworkHandler, ScanCB, this);
The code above start framework as activity fragment, and configure framework params.
   
## Framework interfaces

The `ScanCB` an implementation of interface `bcpScanCb`which has 2 callback  

- call  -  is calling after each scan process, and is getting an ArrayLlist of `BcpBeacon` object if
 nothing was found the ArrayLlist will have size equals to 0
 
- startAutoscan - is calling each time when autoscan was started

The `FrameworkHandler` an implementation of interface `BCPCallback` which has overloaded callbacks `call`.
Needed for receiving framework state. The definition of callbacks:

- Has no params - for futures usage
- Has params (int) code, (String) message  - is using for send framework state. The message can take values
    * success - the section was successfully checked, or scanning was end
    * processing, progress, waiting - section is checking, or scanning is working 
    * Other value - is meant error, the message have info about error
- Has param (String) message - is using for send common error messages

## Framework state codes
``` 
1.  Bluetooth section 
2.  Internet connection 
3.  Auth state 
4.  AWS S3 state 
5.  Firebase state 
7.  Scanning state 
```