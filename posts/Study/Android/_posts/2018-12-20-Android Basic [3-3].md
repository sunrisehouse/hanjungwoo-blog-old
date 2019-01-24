---
layout: post
title:  "Android Basic [3-3] Broadcasts"
date:   2018-12-20 08:00
---
# Broadcasts

- publish-subscribe 디자인 패턴과 유사하다.
- 안드로이드 시스템은 다양한 이벤트들(boots up, device starts charging 등)이 발생했을 때 broadcasts 를 send 한다.
- 어플리케이션 또한 custom broadcasts 를 send 할 수 있다. (ex: 다운로드 완료 됐는지)
- App 은 특정 broadcasts 를 받기 위해 등록할 수 있다. broadcasts 가 보내졌을 때 system 은 자동적으로 broadcasts 를 앱에 보낸다. (특정 타입의 broadcast 를 받기 위해 구독한 app 에)
- normal user flow 밖에서 사용되는 messaging system
- 남용 X -> 느려진다

## Changes to system broadcasts

1. Android 9
    - NETWORK_STATE_CHANGED_ACTION broadcast 가 사용자의 위치나 개인 신상 data 에 대한 정보를 받지 못한다.
    - Android 9 이상이 돌아가는 device 에 깔린 app 이면 broadcast from wifi 는 ssid 와 bssid, connection information, scan result 를 포함하지 않는다. 대신 getConnectionInfo() 쓴다.
2. Android 8.0
    - manifest-declared receivers 에 부가적인 restrictions 를 걸었다.
    - 가장 implicit 한 broadcast 에 대한 receiver 를 manifest 에 선언할 수 없다. 대신 context-registered receiver 를 여전히 사용할 수 있다.
3. Android 7.0
    - ACTION_NEW_PICTURE 과 ACTION_NEW_VIDEO 이 두 system broadcasts 를 더이상 보내지 안흔다.
    - 반드시 CONNECTIVITY_ACTION broadcast 을 등록한다. registerReceiver(BroadcastReceiver,IntentFilter) 를 이용해서

# Receiving Broadcasts

## Manifest-declared receivers

- API level 26 or higher 에서 implicit broadcasts 를 위한 receiver 는 manifest declare 를 이용할 수 없다. ( 몇몇 implicit broadcasts 는 가능 )
- (???)대부분의 경우 scheduled jobs 를 대신 사용할 수 있다.
- 선언 방법
    1. manifest 에 receiver element를 명시한다.
        - intent filters 는 너가 subscribes 하고자 하는 broadcast actions 을 나타낸다.
        ```xml
        <receiver android:name=".MyBroadcastReceiver"  android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="android.intent.action.INPUT_METHOD_CHANGED" />
            </intent-filter>
        </receiver>
        ```
    2. BroadcastReceiver 를 extends 하고 onReceive(Context, Intent) 를 implement 한다.
        ```java
        public class MyBroadcastReceiver extends BroadcastReceiver {
            private static final String TAG = "MyBroadcastReceiver";
            @Override
            public void onReceive(Context context, Intent intent) {
                StringBuilder sb = new StringBuilder();
                sb.append("Action: " + intent.getAction() + "\n");
                sb.append("URI: " + intent.toUri(Intent.URI_INTENT_SCHEME).toString() + "\n");
                String log = sb.toString();
                Log.d(TAG, log);
                Toast.makeText(context, log, Toast.LENGTH_LONG).show();
            }
        }
        ```
        - 시스템은 각 Broadcast 를 handle 할 BroadcastReceiver Component object 를 만든다.
        - 이 객체는 onReceive(Context,Intent) 에 대한 call 이 지속되는 동안에만 valid 하다.
        - 이 메소드를 반환하고 나면 시스템은 이 Component 가 더 이상 active 하지 않다고 고려한다.
        - app 이 설치되면 
            - system package manager 가 receiver 를 등록한다.
            - 그 receiver는 새로운 시작 포인트가 된다.
            - app 이 현재 running 중이 아니더라도 broadcast 를 전달할 수 있다.

## Context-registered receivers

- context 에서 receiver 를 등록하는 방법
    1. Create an instance of BroadcastReceiver.
        ```java
        BroadcastReceiver br = new MyBroadcastReceiver();
        ```
    2. Create an IntentFilter and register the receiver by calling registerReceiver(BroadcastReceiver,IntentFilter)
        ```java
        IntentFilter filter = new IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION);
        filter.addAction(Intent.ACTION_AIRPLANE_MODE_CHANGED);
        this.registerReceiver(br, filter);
        ```
        - receiver는 context 가 valid 하는 한 brodcasts 를 받을 것이다.
        - receiver를 Activity context 에 등록했으면 activity 가 destroy 되기 전까지 broadcasts 를 받을것이고 application context 에 등록했으면 app 이 running 하는 동안 receive 할 것이다.
    3. To stop receiving broadcasts, call unregisterReceiver(amdroid.content.BroadcastReceiver)
        - onCreate 에 activity context를 사용해 receiver 를 register 했다면 onDestroy()에 unregister 시켜야 한다. (leaking 을 막기 위해)
        - onResume 에 register 하면 onPause 에서 unregister (to prevent registering it multiple times)
        - onSaveInstaceState(Bundle) 에서 unregister 시키지 말아라 (만약 user 가 history stack 으로 move back 하면 호출 안되기 때문에)

## Process state 에 대한 효과

- BroadcastReceiver 의 State 는 해당 prpcess 의 state 에 영향을 미친다. (system 이 이 process 를 kill 할 가능성에 영향 미친다.)
- 예) process 가 receiver 를 실행시킬 때 (onReceive() 메소드 안에 코드가 실행될 때) foreground procee 가 되는 것으로 간주된다. -> 시스템은 process 를 계속 running 시킨다 (extrem memory pressure 가 아니라면)
- 하지만 onReceive() 가 return 되고 나면 BroadcastReceiver 는 더 이상 active 하지 않는다.
- receiver 의 host process 가 다른 app component 만큼의 중요도를 갖게 된다.
- 만약 그 process 가 오직 한 manifest declared receiver 를 host 하면 ( 한번도 또는 최근에 interacted 한적 없는 앱에 대해서 흔한 상황 ), then upon returning from onReceive(), 시스템은 이 process 를 낮은 운선순위의 process 로 간주한다. 그리고 다른 더 중요한 process 가 이용가능한 resource 를 만들기 위한 죽일 것이다.
- 이 때문에 너는 broadcast receiver 에서 긴 background threads 를 running 하면 안된다.
- onReceive() 후에, 시스템은 언제나 process 를 kill 할 수 있다. (memory 를 reclaim 하기 위해 그리고 spawned 된 thread 를 종료 시킨다. (???))
- 이것을 피하기 위해 goAsync() 를 불러야 한다. (background thread 에서 broadcast 를 처리하기위해 만약 약간의 시간이 더 필요하다면)
- 또는 JobService 를 schedule 해야 한다. (JobScheduler 를 사용하고 있는 receiver 로 부터)
- 그래서 시스템은 process 가 active work 를 수행하는 것을 계속한다는 것을 안다.
- Processes and Application Live Cycle 을 더 찾아봐라 (https://developer.android.com/guide/components/activities/process-lifecycle)
- onReceive() 후에 끝내는 데 더 시간이 필요하다는 flag 세우기 위해 goAsync() 를 사용한 BroadcastReceiver 는 완전하다.
- 이 것은 특히 onReceive() 안에 너가 끝내기를 원하는 work 가 UI thread 가 frame 을 잃을 많큼 충분히 긴 시간( > 16ms )이라면 유용하다 , making it better suited for a background thread.

# Sending Broadcasts

1. sendOrderedBroadcast(Intent,String)
    - 한 번에 한 receiver 한테 broadcasts 를 보낸다.
    - 각 receiver 가 차례로 수행될 때, 다음 receiver 에 결과를 전달할 수 있다. 또는 broadcast 를 완전하게 중단시킬 수 있다. 다른 broadcast 에 전달되지 않게
    - receiver 의 order 는 해당 intent-filter 의 android:priority 특성으로 controll 된다. (같은 우선순위면 임의대로 실행 됨)
2. sendBroadcast(Intent)
    - undefined order 로 모든 receiver 한테 broadcasts 를 보낸다.
    - Normal Broadcast
    - 더 효율적
    - 하지만 receiver 가 다른 receiver 의 결과를 읽을 수 없다. broadcast 로 부터 받은 data 를 전파할 수 없다. broadcast 를 중단할 수 없다.
3. LocalBroadcastManager.sendBroadcast
    - 같은 앱에 있는 receiver 에 broadcasts 를 보낸다.
    - app 들 간에 broadcasts 를 보낼 필요가 없다면 local braodcast를 이용한다.
    - 훨씬 더 효율적 (프로세스간 communication이 필요하지 않기 때문)
    - broadcast 를 받을 수 있는 다른 앱과 관련된 보안 이슈를 걱정할 필요 없다.

- ex)
    ```java
    Intent intent = new Intent();
    intent.setAction("com.example.broadcast.MY_NOTIFICATION");
    intent.putExtra("data","Notice me senpai!");
    sendBroadcast(intent);
    ```
    - Broadcast message 는 Intent object 로 wrapped 되어 있다.
    - Intent action string 은 반드시 app의 Java package (sytax 를 name 하고 uniquely broadcast event 를 식별할 수 있는 ) 를 제공한다.
    - 부가적인 정보를 putExtra 를 통해 붙일 수 있다.
    - Intent 의 setPackage 를 호출해서 같은 organizaion 안에 있는 app 들의 모임에만 broadcast 를 제한 할 수 있다.

# Restricting broadcasts with permissions

- Permissions 는 특정 permissions 을 가지고 있는 app 들의 집합에만 broadcast 를 제한한다.
- restricions 를 broadcast 의 sender 와 receiver 둘 다에 enforce 시킬 수 있다.

## 송신 Sending with permissions

- permission parameter 를 명시할 수 있다.
- 오직 manifest 에 그 permission 을 요청한 receiver 만 broadcast 를 받을 수 있다.
- ex
    ```java
    sendBroadcast(new Intent("com.example.NOTIFY"),
              Manifest.permission.SEND_SMS);
    ```
- 이 broadcast 를 수신하기 위해 수신하는 app 은 아래 permission 을 request 해야한다. 
    ```xml
    <uses-permission android:name="android.permission.SEND_SMS"/>
    ```

## 수신 receiving with permissions

- 만약 receiver 등록할 때 permission parameter 를 specify 했다면 오직 그 permission 을 request 한 broadcaster 만이 이 receiver 에 Intent 를 보낼 수 있다.
- ex
    ```xml
    <receiver android:name=".MyBroadcastReceiver"
            android:permission="android.permission.SEND_SMS">
        <intent-filter>
            <action android:name="android.intent.action.AIRPLANE_MODE"/>
        </intent-filter>
    </receiver>
    ```
- ex
    ```java
    IntentFilter filter = new IntentFilter(Intent.ACTION_AIRPLANE_MODE_CHANGED);
    registerReceiver(receiver, filter, Manifest.permission.SEND_SMS, null );
    ```    

# Security considerations and best practices

- 주의 사항

    1. 다른 app 의 component 에 broadcast 를 보낼 것이 아니라면 local broadcast 이용하기

        - LocalBroadcastManager 는 훨씬 더 효율적.
        - 보안 신경 안써도 됨

    2. 많은 앱들이 같은 broadcast 를 받도록 등록되어 있다면 시스템은 많은 앱들을 실행시킬 것이다. (이것은 device performance 나 user experiece 에 매우 안좋은 영향)

        - context registraion 을 더 이용하자
        - 때때로 android system 은 context-registered receivers 의 사용을 권장한다. (CONNECTIVITY_ACTION broadcast 는 오직 contex-registered receivers 로 만 전달된다.)

    3. implicit intent 를 이용해 민감한 정보를 broadcast 시키지 마라

        - 누가 내 broadcast 를 받나 control 하는 세가지 방법
            1. broadcast 를 보낼 때 specify a permission 하기
            2. Android 4.0 + 에서 broadcast 보낼 때 setPackage(String) 이용해 package 를 specify 하기. system 은 일치하는 package 를 갖는 app 들로만 broadcast 되도록 제한할 것이다.
            3. local broadcast 사용하기

    4. 너가 receiver 를 등록할 때, 어떤 app 은 잠재적으로 악의있는 broadcast 를 너의 app 의 receiver 에 보낼 것이다.

        - app 이 받는 broadcast 제한하는 방법 세가지
            1. broadcast receiver 등록할 때 specify a permission 하기
            2. manifest-declared receiver 에 android:exported 특성을 false 로 설정하기. app 밖 소스로부터 오는 broadcast 를 받지 않게 한다.
            3. local broadcast 사용하기

    5. namespace for broadcast actions 은 global 이다.

    6. receiver 의 onReceive(Context, Intent) 는 main thread 에서 run 하기 때문에 실행하고 리턴하는 것을 빠르게 해야한다.

        - 만약 긴 작업이 필요하다면 spawning thread 또는 starting background services 를 주의해라 -> 시스템이 전체 process 를 죽일 수 있기 때문에
        - 추천하는 방법 : onReceive() 에서 goAsync() (비동기 작업) 하거나 BroadcastReceiver.PendingResult 를 bakcground thread 에 전달 해라.  
            - broadcast 가 onReceive() 로 부터 return 되도 active 하게 한다.
            - 하지만 심지어 이런 방법도 system 은 너가 broadcast 를 매우 빠르게 끝내기를 기대한다. (under 10 sec)
            - 시스템이 빨리 끝내길 기대하는 것은 너가 다른 thread 로 작업을 옮기는 것을 허용한다. (어지러운 메인쓰레드를 피하기 위해)

    7. broadcast receiver 로부터 activity 를 start 하지 마라.  