---
layout: post
title:  "Android Basic [3-1] Activity"
date:   2018-12-19 08:00
---

# Activity

- 눈에 보이는 UI 띄움
- 새로운 activity 시작되면 이전 activity 는 Back Stack 으로 push 되고 새로운 activity 는 사용자 focus 를 갖게 된다. (후입선출 방식)
- 한 액티비티가 새로운 액티비티의 시작으로 인해 중단된 경우, 이 상태 변경은 액티비티의 수명 주기 Call back method 를 통해 알려진다. (onCreate, onStop ...)

## Activity 생성

- Activity 의 Sub Class 를 생성 해야 한다. ( activity 를 상속받은 class 를 생성해야 한다.)
- 가장 중요한 Activity class 의 콜백 메소드
    1. onCreate
        - 반드시 구현해야 한다.
        - 초기화 진행해야 한다.
        - setContentView() 를 호출해서 activity 의 UI Layout 을 정의해야 한다.
    2. onPause
        - 사용자가 액티비티를 떠난다는 첫 번재 신호.(소멸은 아님)
        - 사용자가 돌아오지 않을 수 있기 때문에 지속되어야 하는 변경사항을 커밋 해야 하는 곳 (variable, object 등을 저장)
- 다른 콜백 메소드는 액티비티 중단이나 심지어 소멸을 초래할 수도 있는 예상치 못한 간섭을 처리하기 위해 필요 => Activity 생명주기 에서 자세히 논의

## Activity 의 UI 구현

- XML 레이아웃 파일을 사용해 디자인. 그리고 setContentView() 에 해당 레이아웃의 리소스 ID를 전달해서 activity 의 레이아웃을 설정.
- ViewGroup 클래스나 View  클래스 에서 파생된 Object 를 이용해 각 디자인 요소를 제어 (ConstraintLayout, Button, Switch 등...)

## Manifest 에서 Activity 선언

- 시스템에서 Acitivity에 Access 하려면 Manifest file 에서 선언해야 한다.
- <.application> 요소의 하위 항목으로 <.activity> 를 추가한다.
- Activity 의 레이블, 아이콘, UI 스타일용 테마와 같이 여러 특성을 지정할 수 있다. (https://developer.android.com/guide/topics/manifest/activity-element?hl=ko)
- android:name 특성은 유일한 필수 특성 = 액티비티의 클래스 이름 지정

### intent-filter 사용

- activity 요소 또한 여러 가지 인텐트 필터를 지정할 수 있다.
- 암시적 인텐트에 응답하고자 하는 곳에 사용
- 다른 애플리케이션에서 해당 activity 를 사용할 수 있게끔 하려면 intent filter 가 있어야 한다.
- intent filter 가 없으면 명시적 intent 를 사용해 직접 시작해야 한다.

# 액티비티 시작

1. startActivity()

    - 다른 액티비티를 시작하려면 startActivity() 함수에 Intent 를 담아 호출하면 된다. (startActivity(Intent))
    - 같은 application 내의 acitivity 뿐만 아니라 다른 application 내의 activity 도 시작 가능하다.

2. startActivityForResult()

    - 시작한 activity 의 결과를 받고 싶을 때 사용
    - onActivityResult() 콜백 메소드를 구현하면 Intent 형식으로 결과를 onActivityResult() 메소드에 반환한다.
    - ```java
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data){
            if (requestCode == PICK_CONTACT_REQUEST) {
                if (resultCode == RESULT_OK) {
                // A contact was picked.  Here we will just display it to the user.
                    startActivity(new Intent(Intent.ACTION_VIEW, data));
                }
            }
        }
        ```
    - 요청이 성공적이면 resultCode 가 RESULT_OK 가 되고 requestCode 가 startActivityForResult() 와 함께 전송된 두 번째 매개변수와 일치할 때 Intent 로 반환된 데이터를 쿼리하여 액티비티 결과를 처리한다.
    - 그러면 ContentResolver 가 Contents Provider 에 대한 쿼리를 수행하고, Contents Provider 는 쿼리된 데이터를 읽을 수 있게 허용하는 Cursor 를 반환한다. (무슨 뜻인지 Contents Provider 공부하고 다시 와봐야 겠다.)

# 액티비티 종료

- finish() 현 액티비티 종료
- finishActivity() 이전에 시작한 별도의 acitivity 종료
- 위에 둘 같은 액티비티의 명시적 종료는 안하는게 좋다. android 시스템이 acitivity 의 수명을 댓니 관리해 주므로 사용자 환경에 부정적인 영향을 미칠 수 있다.
- 사용자가 액티비티의 이 인스턴스에 돌아오는 것을 절대 바라지 않는 경우에만 사용하기.

# 액티비티 Life Cycle

- 콜백 메소드를 구현해 액티비티 수명 주기 관리하는 것은 대단히 중요

## Activity 의 3가지 STATE

1. 다시 시작됨 (Resumed)

    - 액티비티가 foreground 에 있고(눈에 보이고??) 사용자 포커스를 갖고 있을 때
    - "실행 중" 이라고도 함

2. 일시 정지됨 (Paused)

    - 다른 액티비티가 보이고 focus 되어 있지만 해당 액티비티도 여전히 나타나 있을 때.
    - 다른 액티비티가 전면에 보이지만 해당 액티비티가 부분적으로 투명하고나 전체 화면을 덮지 않는 상태.
    - 일시정지된 액티비티는 완전히 살아있지만(Activity Object 가 메모리에 보관되어 있고, 모든 상태 및 멤버 정보를 유지하며 창 관리자에 붙어있지만) 메모리가 극히 부족한 경우 시스템이 중단시킬 수 있다.

3. 정지됨 (Stopped)

    - 다른 액티비티에 완전히 가려진 상태. (액티비티가 이제 백그라운드에 위치)
    - 여전히 살아있기는 마찬가지 (Activity Object 가 메모리에 보관되어 있고, 모든 상태와 멤버 정보를 유지하지만 창 관리자에 붙어 있지 않음)

## 수명 주기 3가지

1. 전체 수명
    - onCreate() ~ onDestroy()
    - onCreate 에서 전체 상태(레이아웃 정의 등)의 설정을 수행한 후 나머지 리소스를 onDestroy 에서 해제하기
    - ex) 네트워크에서 데이터를 다운로드하기 위해 배경에서 실행 중인 스레드가 있는 경우 해당 스레드를 onCreate() 에서 생성한 후 onDestroy() 에서 그 스레드를 중단하기 (아직 이해 잘 안감)

2. 가시적 수명
    - onStart() ~ onStop()
    - 해당 액티비티가 화면에서 보이는 기간 (focus 없이 가려지거나 투명하게 보이더라도)
    - 해당 액티비티를 표시하는 데 필요한 리소스를 유지하면 됨
    - onStart 에서 BroadcastReceiver 를 등록해 UI 에 영향을 미치는 변화를 모니터링 하고 onStop 에서 등록을 해제하기
    - UI 변경관련 BroadcastReceiver 를 start 에 등록하고 stop 에 해제하면 될 듯.

3. 전경 수명
    - onResume() ~ onPauese()
    - 해당 액티비티에 focus 가 있는 기간
    - 데이터를 유지하기 위해 저장되지 않은 변경 사항을 커밋할 때(db 저장 등), CPU를 소모하는 기타 작업을 중단할 때 등에 사용
    - 이 두가지 메소드는 자주 호출되므로 가벼워야 한다. 그래야 전환이 느려 사용자를 기다리게 하는 일을 피할 수 있다. (절전모드가 되거나 대화 상자가 나타나면 onPause()가 호출된다. )

## Activity 의 CallBack method 들

1. onCreate()
2. onRestart()
3. onResume()
4. onPause()
5. onStop()
6. onDestroy()

## Activity 상태 저장 - onSaveInstanceState()

- Stopped 된 상태일 때(다른 액티비티가 foreground 롤 올 때) android system 에서 activity 를 소멸시켰을 경우 onSaveInstanceState 에서 저장한 State 를 Restore 한다.
- 일시적으로 중단된 경우에는 메모리 안에 그대로 보관되어 있기 때문에 원래 State 를 유지하지만 시스템이 중단된 activity 를 소멸시키는 경우 State 를 유지할 수 없다.
- 그러므로 onSaveInstanceState 를 구현하여 액티비티 상태에 관한 중요한 정보를 보존한다.
- 시스템은 onSaveInstanceState 를 호출한 후 액티비티를 소멸되기 쉽게 만든다.
- 시스템은 액티비티에 관한 정보를 이름-값 쌍으로 저장할 수 있는 Bundle 을 onSaveInstanceState에 전달하고 액티비티를 다시 생성할 때 onCreate() 와 onRestoreInstanceState 에게 전달한다. 이들을 이용해 bundle 에 저장되어 있는 상태를 추출하고 activity 를 복원
- 복구할 상태 정보가 없을 경우 Bundle == null ( activity 처음 생성되었을 경우 )
- onSaveInstanceState 구현 안 해도 일부 액티비티 상태를 복구 -> onSaveInstaceState() 가 나올때 마다 View 메서드 호출 해 각 뷰가 저장해야 하는 자체 관련 정보를 저장. ( EditText 나 CheckBox 같은 것들 알아서 저장된다.) -> 우리는 각 위젯에 고유 ID 를 제공

- UI 상태 저장에 사용 / 액티비티 떠날 때 영구적인 데이터를 저장하려면 onPause() 사용

## 구성 변경 처리 - onSaveOnstanceState()

- 화면 방향, 키보드 가용성 및 언어 이 변경되면 android 는 실행 중인 activity 를 다시 생성한다. (= onDestroy -> onCreate 호출 됨)
- 그러므로 onSaveInstanceState() 와 onRestoreInstanceState() ( 또는 onCreate() ) 를 사용해 액티비티 상태를 저장하고 복구하기.
- 런타임 변경 처리 (https://developer.android.com/guide/topics/resources/runtime-changes?hl=ko)

## Activity 조정

- Activity A 가 화면에 나와 있고 Activity B 를 시작하는 상황
    1. Activity A - onPause()
    2. Activity B - onCreate()
    3. Activity B - onStart()
    4. Activity B - onResume()
    5. Activity A - onStop()