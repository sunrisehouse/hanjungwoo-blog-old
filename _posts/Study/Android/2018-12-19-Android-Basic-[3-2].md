---
layout: post
title:  "Android Basic [3-2] Service"
date:   2018-12-19 08:00
---

# Service

- Service 는 백그라운드에서 오래 실행되는 작업을 수행할 수 있는 애플리케이션 구성 요소.
- no UI
- 다른 애플리케이션으로 전환하더라도 백그라운드에서 계속해서 실행됨.
- Component 를 Service 에 bind 하여 서비스와 상호작용 가능, 심지어 프로세스 간 통신(IPC) 도 수행 가능
- 사용 예
    1. 네트워크 트랜잭션
    2. 음악 재생
    3. 파일 I/O
    4. Contents Provider 와 상호작용

## Service VS Thread

- Service 는 그저 백그라운드에서 실행될 수 있는 구성 요소일 뿐 = 애플리케이션과 상호작용하지 않아도 되는 작업만 service 로 만들기.
- ex) 액티비티가 실행되는 중에만 음악 재생 하기 = service 만드는 것이 아닌 onCreate() 에서 스레드 생성하고 onStart() 에서 실행하기 시작한 후 onStop() 에서 중지 하기.
- 서비스를 사용하는 경우 기본적으로 애플리케이션의 기본 스레드에서 계속 실행되므로 서비스가 집약적이거나 차단적인 작업을 수행하는 경우 서비스 내에 새 스렏를 생성해야 한다.
    
    - ex) 서비스가 CPU 집약적인 작업 수행할 예정이거나 차단적인 작업 수행할 예정인 경우 (MP3 재생 or 네트워킹) -> 서비스 내에 새 스레드 생성하기
    - 서비스가 기본 스레드에서 실행되기도 하기 때문에 집약적이거나 차단적인 작업 수행할 경우 해당 서비스 때문에 액티비티 성능이 느려지게 된다. -> 서비스 내에 새 스레드 시작

## 서비스의 생성

- Service 의 서브클래스를 생성해야 한다. (Service 클래스를 상속받은 클래스를 만들어야 한다.)
- 가장 중요한 Service class 의 콜백 메소드
    1. onStartCommand()
        - 다른 Component 가 서비스를 startService() 할 경우 실행
        - 이 메소드가 실행 되면 서비스가 시작되고 백그라운드에서 무기한으로 실행될 수 있다.
        - 해당 서비스 중단 하는 것은 개발자 본인의 책임. stopSelf() or stopService() 호출하기
    2. onBind()
        - 다른 Component 가 서비스를 bindService() 할 경우 실행
        - 이 메서드를 구현할 때에는 클라이언트가 서비스와 통신을 주고받기 위해 사용할 인터페이스를 제공해야 한다. -> IBinder 를 반환하면 된다.
        - 항상 구현해야 한다. (바인딩을 허용하지 않고자 한다면 null 을 반환하기)
    3. onCreate()
        - ( onStartCommand() or onBind() 호출 전에 ) 서비스가 처음 생성되어 일회성 설정 정차를 수행하는 경우 호출됨
        - 서비스가 이미 실행 중인 경우, 이 메서드는 호출되지 않는다.
    4. onDestroy()
        - 해당 서비스를 더 이상 사용하지 않고 소멸 시키는 경우
        - 서비스에서 이를 구현해야 thread, listener, receiver 등 모든 리소스를 정리할 수 있다.

## Manifest 에서 Service 선언

- 시스템에서 Service에 Access 하려면 Manifest file 에서 선언해야 한다.
- <.application> 요소의 하위 항목으로 <.service> 를 추가한다.
- android:name 특성은 유일한 필수 특성 = service 클래스 이름 지정
- 앱의 보안을 보장하려면 Service 를 시작하거나 바인드할 때 항상 명시적 인텐트를 사용하고 서비스에 대한 인텐트 필터는 선언하지 않도록 한다.
- (??? 모르겠다) 어느 서비스를 시작할지 어느 정도 모호성을 허용하는 것이 중요한 경우, 서비스에 대해 인텐트 필터를 제공하고 구성 요소 이름을 Intent에서 제외시킬 수 있지만, 그러면 해당 인텐트에 대한 패키지를 setPackage()로 설정하여 대상 서비스에 대해 충분한 명확성을 제공해야 합니다.

# Service 의 시작

1. startService()
2. bindService()

# Service 의 종료

1. started Service
    - Service 내의 stopSelf 혹은 Service 밖의 다른 Component 가 stopService() 하기 전 까지 실행 중인 상태로 유지

2. binded Service
    - Service 가 모든 client 로 부터 binding 해제되면 시스템이 이를 소멸

3. Android 시스템이 서비스를 강제 중단시키는 경우
    - 사용자 포커스를 가진 액티비티를 위해 시스템 리소스를 회복해야만 하는 경우로만 국한
    - 해당 서비스가 focus 되어 있는 액티비티에 바인딩 되어 있다면 주단될 가능성이 낮고, 서비스가 foreground 에서 실행된다고 선언된 경우, 거의 절대 중단되지 않는다.
    - 그렇지 않은 경우 시간이 지나면서 시스템이 백그라운드 작업 목록에서의 이 서비스의 위치를 점점 낮추고 서비스는 중단되기 매우 쉬워집니다.
    - 서비스가 시작되었다면 이를 시스템에 의한 재시작을 정상적으로 처리하도록 디자인해야한다.
    - 시스템이 서비스를 중단시키는 경우, 리소스를 다시 사용할 수 있게 되면 가능한 한 빨리 서비스가 다시 시작됩니다.

# Service 의 두 가지 상태

1. Started
    - startService() 로 호출
    - 백그라운드에서 무깋한으로 실행 가능 (해당 서비스를 시작한 Component 가 소멸 되었더라도 무관)
    - 결과를 호출자에게 반환 X, 작업 완료 시 알아서 중단
    - ex) 네트워크에서 파일 다운

2. Binded
    - bindService() 로 호출
    - client - server 인터페이스를 제공해 Component 와 상호작용할 수 있도록 함 (심지어 여러 프로세스에 걸쳐 프로세스 간 통신으로 수행할 수도 있다)
    - bind 된 service 는 다른 Component 가 이 service 에 바인드되어 있는 경우에만 실행됨
    - 여러 Component 가 한 service 에 한꺼번에 bind 될 수 있다. (연결된 모든 Component 가 binding 을 해제하면 해당 서비스 소멸)

# Started Service

## Started Service 의 생성

- startService() 호출하여 시작, 그 결과로 onStartCommand() 메서드가 호출되는 경우
- 서비스가 시작되면 이를 시작한 Component 와 독립된 자신만의 수명 주기 가짐 (시작한 Component 와 독립적이다.)
- startService 에 Intent 를 전달해 호출하면 된다. (onStartCommand 에서 Intent 를 받는다.)
- ex) DataBase 에 data 를 약간 저장해야 하는 경우 service 를 시작하고 저장할 Data 를 이 서비스에 전달해 service 는 이 data 를 받고, 인터넷에 연결하고, db 트랜잭션을 수행, 완료 후 서비스는 알아서 중단되고 소멸
- Started Service 생성 위한 확장 가능 클래스 두 가지
    1. Service
        - 모든 service 의 기본 클래스
        - 이 경우 서비스의 모든 작업을 수행할 새 스레드를 만드는 것이 중요. 서비스가 기본적으로 애플리케이션의 기본 스레드를 사용하기 때문. 그렇게 되면 액티비티 성능 느려지기 때문.
    2. IntentService
        - Service 의 sub class
        - worker thread 를 사용해 모든 시작 요청을 처리하되 한 번에 하나씩 처리
        - (???)서비스가 여러 개의 요청을 동시에 처리하지 않아도 되는 경우 이것이 최선의 옵션.
        - 우리가 해야 할 일 은 onHandleIntent() 구현하기 -> 각 시작 요청에 대한 인텐트를 수신하므로 개발자는 백그라운드 작업 수행 가능.

## IntentService 클래스 확장

- 대부분의 Service 는 여러 개의 요청을 동시에 처리하지 않아도 되기 때문에 IntentService 클래스를 사용하는 것이 최선의 방안일 것이다.
- IntentService 가 수행하는 작업들
    1. onStartCommand() 에 전달된 모든 인텐트를 실행하는 기본 작업자 스레드를 생성
    2. 한 번에 인텐트를 하나씩 onHandleIntent() 에 전달하는 작업 큐를 생성 => 다중 스레딩 X
    3. 시작 요청이 모두 처리된 후 서비스를 중단하므로 개발자가 stopSelf() 호출할 필요가 전혀 없다.
    4. onBind() 의 기본 구현 제공해 null 반환하도록 한다.
    5. onStartCommand() 의 기본 구현을 제공하여, 인텐트를 작업 큐로 보내고 그 다음은 onHandleIntent() 구현으로 보낸다.
- 필요한 것은 생성자 하나와 onHandleIntent()
    ```java
        public class HelloIntentService extends IntentService {

            /**
            * A constructor is required, and must call the super IntentService(String)
            * constructor with a name for the worker thread.
            */
            public HelloIntentService() {
                super("HelloIntentService");
            }

            /**
            * The IntentService calls this method from the default worker thread with
            * the intent that started the service. When this method returns, IntentService
            * stops the service, as appropriate.
            */
            @Override
            protected void onHandleIntent(Intent intent) {
                // Normally we would do some work here, like download a file.
                // For our sample, we just sleep for 5 seconds.
                try {
                    Thread.sleep(5000);
                } catch (InterruptedException e) {
                    // Restore interrupt status.
                    Thread.currentThread().interrupt();
                }
            }
        }
    ```
- 다른 콜백 메서드도 재정의 하기로 결정하는 경우 슈퍼 구현 필요 (ex: onCreate(), onStartCommand() 등등 ) -> 작업자 스레드의 수명을 적절하게 처리하기 위해
    ```java
        @Override
        public int onStartCommand(Intent intent, int flags, int startId) {
            Toast.makeText(this, "service starting", Toast.LENGTH_SHORT).show();
            return super.onStartCommand(intent,flags,startId);
        }
    ```

## Service 클래스의 확장

- Service 가 멀티스레딩을 수행해야 하는 경우 (작업 큐를 통해 시작 요청을 처리하는 대신) -> 이 때는 Service 클래스를 확장해 각 인텐트를 처리하게 해야한다.
- 요청된 Intent 를 onHandleIntent 가 아닌 onStartCommand() 로 직접 처리할 수 있기 때문에 각 요청에 대해 새 스레드를 하나씩 생성하면 여러 개의 요청을 동시에 수행할 수 있다.
- onStartCommand() 의 반환 값 : 시스템이 서비스를 중단시킨 경우 시스템이 해당 서비스를 계속하는 방법
    1. START_NOT_STICKY
        - 서비스 재생성 X
        - 서비스가 불필요하게 실행되는 일을 피할 수 있는 가장 안전한 옵션
        - 어플리케이션이 완료되지 않은 모든 작업을 단순히 재시작할 수 있을 때 좋다.
        - 보류 인텐트가 있는 경우 예외
    2. START_STICKY
        - 서비스 재생성 하고 onStartCommand() 를 호출하되 마지막 인텐트 다시 전달 X
        - 그 대신 시스템이 null 인텐트로 onStartCommand() 호출
        - 명령을 실행하지는 않지만 무기한으로 실행 중이며 작업을 기다리고 있는 미디어 플레이어(또는 그와 비슷한 서비스)에 적합
        - 보류 인텐트가 있는 경우 예외. 이 경우 이들 인텐트가 전달됨
    3. START_REDELIVER_INTENT
        - 서비스 재생성 O
        - 이 서비스에 전달된 마지막 인텐트로 onStartCommand() 호출
        - 즉시 재개되어야 하는 작업을 능동적으로 수행 중인 서비스 (ex: 파일 다운로드) 에 적합
        - 모든 보류 인텐트가 차례로 전달 됨

## Service 시작

1. Intent(시작할 서비스를 지정) startService() 에 전달
2. 서비스 아직 실행 중 아니면 onCreate() 호출
3. 서비스 실행 중이면 onStartCommand() 호출

- 전달된 Intent는 Component 와 Service 사이의 유일한 통신 방법
- 서비스가 결과를 돌려보내기를 원하면 BroadCast 사용해야함 -> PendingIntent 만들어 서비스를 시작한 Intent 내의 서비스에 전달.

## Service 중단

- 시스템이 서비스를 중단하거나 소멸시키지 않는다.
- onStartCommand() 반환 후에도 계속 실행되는 경우는 stopSelf() 를 호출해 스스로 중지시키거나 다른 Component dml stopService() 호출해 이를 중지 시켜야함.
- 서비스가 onStartCommand() 에 대한 여러 요청을 동시에 처리하는 경우
    - 시작 요청 처리를 완료한 뒤에도 서비스를 중단하면 안 된다. ( 그 이후 새 시작 요청을 받았을 수 있기 때문 = 첫 요청 종료 시에 중단하면 두 번째 요청이 종료될 수 있다.)
    - 이 경우 stopSelf(int) 를 사용해 서비스를 중단시키라는 개발자의 요청이 항상 최신 시작 요청에 기반하도록 해야한다.
    - = stopSelf(int) 를 호출할 경우 시작 요청의 ID (onStartCommand()에 전달된 startID) 를 전달, 그러면 새 시작 요청 받은 경우 ID 가 일치하지 않게 되고 서비스는 중단 되지 않는다.
- stop 해줘야 배터리 전력 소모 줄이고 리소스 낭비 피할 수 있다.

# Binded Service 생성

- bindService() 호출하여 오래 지속되는 연결을 생성
- 사용 범위
    - Activity와 애플리케이션의 다른 Component 에서 Service 와 상호작용하기를 원하는 경우 binded service 를 생성해야 한다.
    - 아니면 Application 의 기능 몇 가지를 프로세스 간 통신(IPC) 을 통해 다른 애플리케이션에 노출하고자 하는 경우에 생성해야 한다.
- 바인드된 서비스를 생성하려면 onBind() 콜백 메소드를 구현해야 하고 IBinder (service 와의 통신을 위한 인터페이스) 를 반환하도록 해야 한다.
- 다른 Component 가 bindService() 호출하면 해당 IBinder 인터페이스를 검색하고 서비스에 있는 메소드를 호출하기 시작할 수 있다.
- binded service 는 자신에게 binded 된 Componenet 에게 도움이 되기 위해서만 존재하는 것이므로 binding 된 Component 가 없으면 시스템이 이를 소멸.
- 클라이언트와 서비스가 통신하는 방법
    - IBinder 인터페이스의 구현
    - 이 IBinder 를 onBind() 콜백 메소드에서 반환해야 한다.
    - 클라이언트가 IBinder 를 수신하면 해당 인터페이스를 통해 서비스와 상호작용 가능
    - 클라이언트가 서비스와 상호작용 완료하면 unBindService() 호출해 바인딩 해제
- https://developer.android.com/guide/components/bound-services?hl=ko

# 사용자에게 알림 전송

- 토스트 알림
- 상태 표시줄 알림

# 포그라운드에서 서비스 실행

- 사용장가 능동적으로 인식하고 있으므로 메모리 부족 시에도 시스템이 중단할 후보로 고려되지 않는 서비스
- 상태 표시줄에 대한 알림을 제공해야 한다.
- startForeground(ONGOING_NOTIFICATION_ID, notification) 
- 첫번째 매개 변수는 해당 알림을 고유하게 식별하는 정수
- 두번째 매개 변수는 상태 표시줄에 해당되는 Notification

# 서비스 수명 주기 관리

- started service
- binded service

- 이 두가지 경로가 별개의 것은 아니다. -> startService 로 시작된 서비스에 바인드할 수도 있다. -> 이런 경우 stopService() 또는 stopSelf() 는 모든 클라이언트가 바인딩 해제될 때까지는 서비스를 실제로 중단시키지 않는다.

## 수명주기 콜백 구현

## 수명 주기

1. 전체 수명 주기
    - onCreate() ~ onDestroy()
2. 활성 수명 주기
    - onStartCommand() ~
    - onBind() ~ onUnbind()