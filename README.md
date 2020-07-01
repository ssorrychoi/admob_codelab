[CodeLabs in Flutter]

# [Adding AdMob ads to a Flutter app](https://codelabs.developers.google.com/codelabs/admob-ads-in-flutter/#0)

## 1. Introduction

이번 codelab에선 *AdMob banner*, *전면광고*, **Awesome Drawing Quiz**라고 불리는 앱에 리워드주는 광고, 그리는 것의 이름을 플레이어가 맞추는 게임을 추가하는 것을 안내한다.

☑️만약 codelab을 통해 일을 하면서 어떤 이슈가 발생한다면, 아래 링크에 있는 **Report a mistake**를 클릭해서 제보해주세요.

### What you'll learn

​	✅ Flutter앱을 위해 Firebase project를 셋업하는 방법

​	✅ Firebase AdMob plugin을 구성하는 방법

​	✅ 배너, 전면광고, 리워드 광고를 플러터 앱에서 사용하는 방법

### What you'll need

- Android Studio 3.6 혹은 그 이상
- Xcode



## 2. Set up the development environment

### Set up the Flutter development environment

1. Flutter 설치	
2. Android Studio를 열어서 Flutter와 Dart plugin 설치

### Download the [code](https://codelabs.developers.google.com/codelabs/admob-ads-in-flutter/#1)

코드를 다운받은 후, zip file을 추출한다. 혹은,  command line에서

> ```
> $ git clone https://github.com/googlecodelabs/admob-ads-in-flutter
> ```
>
> GitHub repository를 내려받을 수 있다.

두개의 폴더가 있는데, `starter` 와 `complete` 이다.

### import the starter project

1. Android Studio를 연다.

2. 웰컴 스크린에서, `Open an existing Android Studio project` 를 클릭.

3. starter폴더를 연다.

4. 아래부분에 `Dart Analysis`탭을 눌러보면, 에러가 많이 있는 것을 볼 수 있다. 이 에러는 Android Studio에 존재하는 프로젝트를 import 했을때 딱 한번 발생하는것인데, 이 패키지들이 아직 다운로드 되어있지 않기 때문이다. 

5. 에러를 해결하기 위해선, 위에 보이는 `Get dependencies` 버튼을 클릭하면 된다. 이것은 모든 패키지를 샘플 프로젝트에 의존해서 다운로드 받는 것이다.

   ![image](https://user-images.githubusercontent.com/43080040/86254241-810ed700-bbf0-11ea-8327-e4069e803bcd.png)

6. 다운로드가 완료된다면, `Dart Analysis` 탭에서 에러가 없는 것을 볼 수 있고, 이제 개발 할 준비가 된 것이다.



## 3. Set up a Firebase project

AdMob ads를 게시하려면 Firebase AdMob Plugin을 사용해서 Firebase project를 셋업해야 할 필요가 있다.

### Create a Firebase project

1. Firebase console에 들어가서 `Add project` 를 클릭한다.
2. 프로젝트 이름을 입력한다.
3. Enable Google Analytics for this project를 허용하고 continue를 클릭한다.
4. Create project를 클릭한다.
5. 완료된다.

### Register the app with Firebase

#### 📱Android app

1. Android icon을 클릭한다.

2. ![image](https://user-images.githubusercontent.com/43080040/86256252-101cee80-bbf3-11ea-84e4-590c13c8cf7a.png)

3. 맥 기준 (sha-1서명 명령어) => 선택

   ```
   keytool -list -v \
   -alias androiddebugkey -keystore ~/.android/debug.keystore
   ```

4. `com.codelab.awesomedrawingquiz` 를 package name에 넣는다.

5. App nickname 필드에는 `Awesome Drawing Quiz (Android)`라고 넣는다. (선택)

6. `Register app` 을 클릭한다.

7. `google-services.json` 파일을 다운로드 한다.



####📱iOS app

1. iOS icon을 클릭한다.
2. ![image](https://user-images.githubusercontent.com/43080040/86256355-33479e00-bbf3-11ea-82da-a26c66161cdd.png)
3. iOS bundle ID 필드에 `com.codelab.aweesomedrawingquiz` 라고 넣는다.
4. App nickname 필드에 `Awesome Drawing Quiz(ios)` 라고 넣는다. (선택)
5. `Register app` 을 클릭한다.
6. `GoogleService-Info.plist` 를 다운받는다.

### Add a firebase configuration file

### 📱Android 

1. `[flutter project] / android / app` 안에 넣는다.

![image](https://user-images.githubusercontent.com/43080040/86256788-b537c700-bbf3-11ea-9f35-4bc067028be9.png)

2. `[flutter proejct] / Android / build.gradle` 파일에 Google Services Gradle plugin을 추가한다.

```
buildscript {

    repositories {
      // Check that you have the following line (if not, add it):
      google()  // Google's Maven repository
    }

    ...

    dependencies {
      ...

      // TODO: Add Google Services plugin
      classpath 'com.google.gms:google-services:4.3.3'
    }
}

allprojects {
    ...

    repositories {
      // Check that you have the following line (if not, add it):
      google()  // Google's Maven repository
      ...
    }
}
```

3. `[flutter project]/android/app/build.gradle` 파일에 Google Services Gradle 플러그인을 추가한다.

```
...

// TODO: Apply google-services plugin
apply plugin: 'com.google.gms.google-services'

android {
  ...
}
```



####📱iOS

1. Xcode를 반드시 열어서 추가해야한다.
2. `Runner/Runner` 에서 Add Files to "Runner"... 버튼을 클릭해서 `GoogleService-Info.plist` 파일을 추가한다.
3. ![image](https://user-images.githubusercontent.com/43080040/86258126-6c810d80-bbf5-11ea-8dc3-af25da693a61.png)
4. ![image](https://user-images.githubusercontent.com/43080040/86258337-ad792200-bbf5-11ea-8b9f-32968f47912b.png)



## 4. Set up the AdMob app and ad units

Flutter는 멀티 플랫폼 SDK이기 때문에, AdMob에서 Android와 iOS둘다 ad units와 앱을 추가할 필요가 있다. 시작하기전에, AdMob 용어집에 익숙해져야한다.

**Ad unit**

Ad unit은 하나의 AdMob 광고 코드의 결과로 표시되는 광고 세트이다. AdMob 계정에서 ad unit을 만들고 설정할 수 있다. 

각 ad unit에는 고유식별자 ID가 있다. 앱에서 새로운 ad unit을 구현할 때 ID를 참조해서 광고 네트워크에 요청시 광고를 보낼 위치를 알려준다.

**Banner ad**

배너광고는 기기 화면의 상단  또는 하단에 있는 앱 레이아웃 내에서 자리를 차지한다. 사용자가 앱을 사용하는 동안 화면에 유지되며 일정 시간이 지나면 새로 고칠 수 있다.

**Interstitial ad(전면광고)**

전면광고는 호스트 앱의 인테페이스를 다루는 전체 화면 광고이다. 일반적으로 활동간 또는 게임 레벨 간 일시중지와 같이 앱 흐름에서 자연스러운 전환 지점에 표시된다.

**Rewarded ad**

리워드 광고는 인앱 보상 대신 상호작용할 수 있는 옵션이다.



### Set up for Android

1. [Admob Console](apps.admob.com ) 에서 계정을 생성한다.
2. App name 필드에 `Awesome Drawing Quiz` 라고 기입하고, 플랫폼은 Android를 선택한다.
3. 새로운 App ID가 만들어졌다.(**ca-app-pub-xxxxxxxxxxxxxxxx~yyyyyyyyyy**) 

### Create ad units

AdMob에서 `광고 단위 만들기` 를 클릭한다.

![image](https://user-images.githubusercontent.com/43080040/86260184-e7e3be80-bbf7-11ea-9091-48569a63048a.png)

다음과 같은 화면이 나오게 된다. 

### Set up for iOS

iOS도 동일하게 실행한다.

### **Android**

Test AdMob IDs for Android:

| **Item**     | **app ID/ad unit ID**                    |
| ------------ | ---------------------------------------- |
| AdMob app ID | `ca-app-pub-3940256099942544~4354546703` |
| Banner       | `ca-app-pub-3940256099942544/8865242552` |
| Interstitial | `ca-app-pub-3940256099942544/7049598008` |
| Rewarded     | `ca-app-pub-3940256099942544/8673189370` |

### **iOS**

Test AdMob IDs for iOS:

| **Item**     | **app ID/ad unit ID**                    |
| ------------ | ---------------------------------------- |
| AdMob app ID | `ca-app-pub-3940256099942544~2594085930` |
| Banner       | `ca-app-pub-3940256099942544/4339318960` |
| Interstitial | `ca-app-pub-3940256099942544/3964253750` |
| Rewarded     | `ca-app-pub-3940256099942544/7552160883` |



## 5. Add the Firebase AdMob Flutter plugin

[firebase_admob](https://pub.dev/packages/firebase_admob) 플러그인을 설치한다.

Flutter는 다중 플랫폼 SDK이므로 firebase_admob 플러그인은 iOS와 Android 둘다 적용이 가능하다.

### Adding the Firebase AdMob plugin as a dependency

1. `pubspec.yaml` 파일에 추가한다.

```yaml
...

dependencies:
  flutter:
    sdk: flutter
  google_fonts: ^0.3.9

  # Add the following line
  firebase_admob: ^0.9.3

...
```

2. `flutter pub get` 명령어 혹은 `Packages get` 버튼을 클릭하여 플러그인을 다운로드 받는다.


### Update AndroidManifest.xml (Android)

1. `android/app/src/main/AndroidManifest.xml` 파일을 열어 수정한다.
2. AdMob app ID를 추가한다.

```xml
<manifest>
    ...
    <application>
       ...
        <meta-data
            android:name="com.google.android.gms.ads.APPLICATION_ID"
            android:value="ca-app-pub-3940256099942544~3347511713"/>
    </application>
    
</manifest>
```

### Update Info.plist (iOS)

1. `ios/Runner/Info.plist` 파일을 열어 수정한다ㅏ.
2. AdMob app ID를 추가한다.

```plist
...
<key>GADApplicationIdentifier</key>
<string>ca-app-pub-3940256099942544~1458002511</string>
...
```



## 6. Add a helper class for ads

`lib` 디렉토리안에 `ad_manager.dart` 라는 이름의 새로운 파일을 생성하고 다음 코드를 추가한다.

```dart
import 'dart:io';

class AdManager {

  static String get appId {
    if (Platform.isAndroid) {
      return "<YOUR_ANDROID_ADMOB_APP_ID>";
    } else if (Platform.isIOS) {
      return "<YOUR_IOS_ADMOB_APP_ID>";
    } else {
      throw new UnsupportedError("Unsupported platform");
    }
  }

  static String get bannerAdUnitId {
    if (Platform.isAndroid) {
      return "<YOUR_ANDROID_BANNER_AD_UNIT_ID";
    } else if (Platform.isIOS) {
      return "<YOUR_IOS_BANNER_AD_UNIT_ID>";
    } else {
      throw new UnsupportedError("Unsupported platform");
    }
  }

  static String get interstitialAdUnitId {
    if (Platform.isAndroid) {
      return "<YOUR_ANDROID_INTERSTITIAL_AD_UNIT_ID>";
    } else if (Platform.isIOS) {
      return "<YOUR_IOS_INTERSTITIAL_AD_UNIT_ID>";
    } else {
      throw new UnsupportedError("Unsupported platform");
    }
  }

  static String get rewardedAdUnitId {
    if (Platform.isAndroid) {
      return "<YOUR_ANDROID_REWARDED_AD_UNIT_ID>";
    } else if (Platform.isIOS) {
      return "<YOUR_IOS_REWARDED_AD_UNIT_ID>";
    } else {
      throw new UnsupportedError("Unsupported platform");
    }
  }
}
```



## 7. Initialize the AdMob SDK

광고가 로딩되기 전에, AdMob SDK를 초기화 할 필요가 있다. `lib/home_route.dart`파일 을 열고 `_initAdMob()` 함수를 게임 시작하기 전에 SDK가 초기화 되도록 수정한다.

```dart
// TODO: Import ad_manager.dart
import 'package:awesome_drawing_quiz/ad_manager.dart';

import 'package:awesome_drawing_quiz/app_theme.dart';

// TODO: Import firebase_admob.dart
import 'package:firebase_admob/firebase_admob.dart';

import 'package:flutter/material.dart';

...

class _HomeRouteState extends State<HomeRoute> {

  ...

  Future<void> _initAdMob() {
    // TODO: Initialize AdMob SDK
    return FirebaseAdMob.instance.initialize(appId: AdManager.appId);
  }
}
```



## 8. Add a banner ad

이번 section에서는, 배너광고를 게임스크린 상단에 보여주도록 추가한다.

✅  `lib/game_route.dart` 파일에서 `ad_manager.dart` 와 `firebase_admob.dart`를 import 한다.

```dart
...

// TODO: Import ad_manager.dart
import 'package:awesome_drawing_quiz/ad_manager.dart';

// TODO: Import firebase_admob.dart
import 'package:firebase_admob/firebase_admob.dart';

class GameRoute extends StatefulWidget {
  ...
}
```

✅  다음으로, `_GameRouteState` 클래스에서, member와 method를 추가한다. 

`lib/game_route.dart`

```
class _GameRouteState extends State<GameRoute> implements QuizEventListener {
  
  ...

  // TODO: Add _bannerAd
  BannerAd _bannerAd;

  ...

  // TODO: Implement _loadBannerAd()
  void _loadBannerAd() {
    _bannerAd
      ..load()
      ..show(anchorType: AnchorType.top);
  }

  ...
}
```

✅  `initState()`에서, BannerAd광고를 로드한다. 배너에는 320x50 (`AdSize.banner`)가 표시된다.

```dart
@override
void initState() {
  ...
 
  // TODO: Initialize _bannerAd
  _bannerAd = BannerAd(
      adUnitId: AdManager.bannerAdUnitId,
      size: AdSize.banner,
  );

  // TODO: Load a Banner Ad
  _loadBannerAd();
}
```

✅  마지막으로, `_bannerAd?.dispose()` 를 호출하여 연관된 자원들을 해제한다.

```dart
@override
void dispose() {
  // TODO: Dispose BannerAd object
  _bannerAd?.dispose();

  ...

  super.dispose();
}
```

