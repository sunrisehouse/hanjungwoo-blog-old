---
layout: post
title:  "Android Basic [3-5] Intent and Intent filter"
date:   2018-12-21 08:00
categories: android
---

# Intent 기본 사용 사례

1. 액티비티 시작.
2. 서비스 시작.
3. 브로드캐스트 전달.

# Intent 유형

1. 명시적(explicit) 인텐트 : 시작할 Component 의 이름을 지정한다.
2. 암시적(implicit) 인텐트 : 특정 Component 의 이름을 지정하진 않지만 그 대신 수행할 일반적일 작업을 선언하여 또 다른 앱의 Component 가 이를 처리할 수 있도록 해준다.
    - 암시적 인텐트를 생성하면 Android 시스템이 시작시킬 적절한 Component 를 찾는다.
    - 인텐트의 내용과 기기에 있는 다른 여러 앱의 manifest 파일에서 선언한 intent filter 와 비교하는 방법으 사용.
    - 호환되는 intent filter 가 여러 개인 경우, 시스템은 대화상자를 표시하여 사용자가 어느 앱을 사용할지 직접 선택하게 한다.
    - 앱의 보안을 위해 service 는 항상 명시적 인텐트만 사용하기. 서비스에 대한 intent filter 선언 X.

# Intent 구축

- Component 가 작업을 적절히 수행하기 위해 사용할 정보들
    1. Component 이름
        - 시작할 Component 이름
        - Intent 를 명시적인 것으로 만들어 주는 중요한 정보. 있으면 명시적, 없으면 암시적
    2. Work
        - 수행할 일반적인 작업을 나타내는 문자열.
        - 본인의 앱 내에 있는 Intent 가 사용할 작업을 직접 지정할 수도 있지만, 보통은 Intent Class 나 다른 Framework class 가 정의한 상수를 써야 한다.
        - 나름의 작업을 직접정의하는 경우, 앱의 패키지 이름을 앞에 포함시켜야 한다. 
            - ex) 
            ```java
                static final String ACTION_TIMETRAVEL = "com.example.action.TIMETRAVEL"; 
            ```
    3. Data
        - 작업을 수행할 때 필요한 데이터 또는 해당 데이터의 MIME 유형을 참조하는 URI
        - Intent 의 작업에 따라서 제공된 데이터의 유형이 달라진다. ( ex: Intent 작업이 ACTION_EDIT 인 경우 데이터에는 편집할 문서의 URI 가 들어있어야 한다. )
        - 데이터의 MIME 유형을 지정해 두면 Android 시스템이 Intent 를 수신할 최상의 Component 를 찾는데 도움이 된다. 하지만 MIME 유형이 URI 에서 추론되는 경우도 종종 있다.
    4. Category
        - Intent 처리할 Component 의 종류에 관한 추가 정보를 담은 문자열
        - Component 이름, 작업, 데이터, 카테고리 로 어느 앱 Component 를 시작해야 할지 확인할 수 있다.
    5. Extra
        - 요청한 작업을 수행하기 위해 필요한 추가 정보를 담고 있는 키-값 쌍.
        - putExtra() 를 사용해 extra data 를 추가
        - Bundle 객체 생성해 putExtras() 에 넣어도 됨.
    6. Flag
        - Intent 의 meta data 같은 기능
        - ex) 액티비티 시작 방법에 대한 지침 주기, 액티비티 시작한 다음에 어떻게 처리해야 하는지 등

## 명시적 Intent 예시

- Intent 객체에 대한 Component 이름을 정의 해야 한다. 다른 Intent 속성은 모두 선택 사항
    ``` java
            // Executed in an Activity, so 'this' is the Context
            // The fileUrl is a string URL, such as "http://www.example.com/image.png"
            Intent downloadIntent = new Intent(this, DownloadService.class);
            downloadIntent.setData(Uri.parse(fileUrl));
            startService(downloadIntent);
    ```
- Intent(Context, Class) 생성자가 앱에 Context 를 제공하고 구성 요소에 Class 객체를 제공.

## 암시적 Intent 예시

- 작업을 지정하여 해당 기기에서 해당 작업을 수행할 수 있는 모든 앱을 호출할 수 있도록 한다.
    ```java
            // Create the text message with a string
            Intent sendIntent = new Intent();
            sendIntent.setAction(Intent.ACTION_SEND);
            sendIntent.putExtra(Intent.EXTRA_TEXT, textMessage);
            sendIntent.setType("text/plain");

            // Verify that the intent will resolve to an activity
            if (sendIntent.resolveActivity(getPackageManager()) != null) {
                startActivity(sendIntent);
            }
    ```
- startActivity 로 전송한 암시적 인텐트를 처리할 앱이 사용자에게 전혀 표시되지 않을 수도 있고 그러면 호출이 실패하고 앱이 작동 중단된다.
- 어느 액티비티든 이 인텐트를 수신하도록 확실히 하려면, Intent 객체의 resolveActivity() 를 호출합니다. 결과가 null 이 아닌 경우, 인텐트를 처리할 수 있는 앱이 최소한 하나는 있다는 뜻이며 startActivity() 를 호출해도 안전.
- startActivity() 가 호출되면 시스템이 설치된 앱을 모두 살펴보고 이런 종류의 인텐트를 처리할 수 있는 앱이 어느 것인지 알아본다.

## 앱 선택기 강제 적용

- 앱이 여러 개이고 사용자가 매번 다른 앱을 사용하기를 원할 수도 있는 경우 선택기 대화상자 명시적으로 표시하게 하기
- createChooser() 사용해 Intent 만들고 startActivity 에 전달

# 암시적 인텐트 수신

- 본인의 앱이 수신할 수 있는 암시적 인텐트가 어느 것인지 알리려면, 각 앱 Component 에 대한 하나 이상의 인턴트 필터를 manifest 파일에 선언한다.
- 한 Component 에 두 개 이상의 intent-filter 가 있을 수 있다.
- <.intent-filter> 내부 세 가지 요소
    1. action : name 특성에서 허용된 인텐트 작업을 선언합니다. 이 값은 어떤 작업의 리터럴 문자열 값이어야 하며, 클래스 상수가 아닙니다.
    2. data : 허용된 데이터 유형 선언. 이때 데이터 URI(scheme, host, port, path 등)와 MIME 유형의 여러 가지 측면을 나타내는 하나 이상의 속성을 사용하는 방법을 씁니다.
    3. category : name 특성에서 허용된 인텐트 카테고리를 선언합니다. 이 값은 어떤 작업의 리터럴 문자열 값이어야 하며, 클래스 상수가 아닙니다.

- ex
    ```xml
        <activity android:name="ShareActivity">
            <intent-filter>
                <action android:name="android.intent.action.SEND"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
            </intent-filter>
        </activity>
    ```
# 구성 요소로의 액세스 제한

- 인텐트 필터 안전 X -> 오직 자신의 앱만 Comonent 중 하나를 시작할 수 있도록 하는 것이 중요한 경우, 해당 Component 에 대해 exported 특성을 false 로 설정하면 된다.

# PendingIntent 사용

- PendingIntent 객체는 Intent 객체 주변을 감싸는 wrapper.
- 기본 목적 : 외부 application 권한을 허가하여 안에 들어 있는 Intent 르 마치 본인 app 의 자체 process 에서 실행하는 것처럼 사용하게 하는 것.
- 사례
    1. Notification 으로 작업을 수행할 때 Intent 가 실행되도록 선언 ( NotificationManager 가 Intent 를 실행 )
    2. app widget 으로 작업을 수행할 때 Intent 가 실행되도록 선언 ( 메인 화면 app 이 Intent 를 실행 )
    3. 향후 지정된 시간에 인텐트가 실행되도록 선언 ( AlarmManager 가 Intent 를 실행 )

# Intent 확인

- 시스템이 액티비티를 시작하라는 암시적 인텐트를 수신하면, 시스템은 해당 인텐트에 대한 최선의 액티비티를 검색.
- 세 가지 측면을 근거로 인텐트를 인텐트 필터에 비교
    1. 인텐트 작업
    2. 인텐트 데이터 (URI 와 데이터유형 둘 다)
    3. 인텐트 카테고리

## 작업 테스트

## 카테고리 테스트

## 데이터 테스트

## 인텐트 일치