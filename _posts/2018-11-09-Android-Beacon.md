---
layout: post
title:  "Beacon Error Test"
date:   2018-11-12 08:00
categories: Beacon
---
# 사용한 비콘 스캔 라이브러리

    implementation 'org.altbeacon:android-beacon-library:2.15.2'

# 안드로이드 어플리케이션 정보

    compileSdkVersion 28
    minSdkVersion 21
    targetSdkVersion 28

# 비콘 라이브러리 활용 정보

    regionBootstrap 을 사용.

    beaconManager.getBeaconParsers().add(new BeaconParser().setBeaconLayout("m:2-3=0215,i:4-19,i:20-21,i:22-23,p:24-24"));
    beaconManager.setForegroundScanPeriod(1100L);
    beaconManager.setBackgroundScanPeriod(1100L);
    beaconManager.setForegroundBetweenScanPeriod(0);
    beaconManager.setBackgroundBetweenScanPeriod(0);
    beaconManager.enableForegroundServiceScanning(notificationcompatBuilder_Scannging.build(),456);
    beaconManager.setEnableScheduledScanJobs(false);

# 에러

## Enter/Exit 에러 
    
* 상황 : 백그라운드 비콘 스캔을 해 놓은 상태에서 비콘과 스마트폰은 가만히 있는데 시간이 지나면서 가끔 혼자서 Enter/Exit 가 반복된다.
    
### 에러 테스트 1 
( Exit 발생 시키는 시간 : 10초 // 스캔 한 싸이클 1.1초 // 스캔 싸이클 사이 시간 0초 )

1. 스마트폰 1개 / 비콘 2개 (비콘 A,B)

    * 비콘 2개를 켜놓은 상태로 스캔을 계속 해 보았다.
    * <img src="/resource/img/beacon_error1.PNG" width="800px" height="150px">
    * 비콘 A 신호가 59초에 한 번 그리고 10초에 한 번 감지 됐다.
    * 비콘 B 신호는 그 사이에 감지가 계속 되고 있다.             * 비콘 신호 중 한 개만 10초 이상 감지되지 않는다.
    * 한 비콘 신호는 계속 감지 되는 것으로 보아 비콘 스캔은 꺼지지 않은 것으로 보인다.
    * 그러면 비콘이 신호를 쏘지 않는 것인가??

2. 스마트폰 2개(스마트폰1,2) / 비콘 2개 (비콘A,B)

    * 똑같은 어플리케이션을 스마트폰 2대에 설치하고 비콘 2개를 켜놓은 상태로 스캔을 해 보았다.
    * 스마트폰 1 <img src="/resource/img/beacon_error2.1.PNG" width="800px" height="150px">
    * 스마트폰 2 <img src="/resource/img/beacon_error2.2.PNG" width="800px" height="150px">
    * 스마트폰1 에서 32초에 비콘A 가 읽히고 11초 뒤인 43초에 비콘 A 가 다시 읽혔다. 중간에 비콘 B 는 계속해서 감지되고 있다. 
    * 스마트폰2 에서 32초에 비콘A 가 읽혔다. 하지만 비콘 신호는 32초와 43초 사이에 계속해서 감지되고 있다.
    * 비콘이 신호를 쏘지 않는 것은 아니었다.
    * 그저 한 신호를 10초 이상 감지하지 못하는 상황이 있다. 이유가 무엇일까
