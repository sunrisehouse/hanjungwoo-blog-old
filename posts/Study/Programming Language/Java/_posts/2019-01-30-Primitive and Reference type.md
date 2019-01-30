---
layout: post
title:  "Java 에서 Primitive type 과 Reference type"
date:   2019-01-30 08:00
---

# Primitive type 과 Reference type

* javascript 와 java 둘 다 비슷한 것 같다.

## 원시 타입과 참조 타입

1. 원시 타입 (Primitive type)
    * int, double, float, long, short, byte, char, boolean 8개
    * 프로그래머가 정의 불가능
    * 변수에 할당될 때 메모리 상에 고정된 크기로 저장되고 해당 변수가 값을 보관

2. 참조 타입 (Reference type)
    * class
    * 프로그래머가 정의 가능
    * 변수에 할당될 때 데이터 크기 정해져 있지 않고 변수에는 값을 저장하는게 아닌 reference 만 저장된다.
    * 변수의 값은 heap 메모리에 저장하고 그 메모리 주소를 변수에 저장

## Wrapper 클래스

* 목적 : 원시 타입 값을 참조 타입으로 만들어서 편리하게 다루기 위해. (안에 있는 method 등을 사용해서)(원시 타입은 method 가 없다.)
* 종류

    * byte -> java.lang.Byte
    * short -> java.lang.Short
    * int -> java.lang.Integer
    * long -> java.lang.Long
    * float -> java.lang>Float
    * double -> java.lang.Double
    * boolean -> java.lang.Boolean
    * char -> java.lang.Character

* 예

```java
    public static void main(String[] args) {
        int a = 10;
        Integer obj = new Integer(20);
    
        // 개발자가 명시적으로 랩퍼 객체에서 값을 꺼내는 코드
        int b = obj.intValue();
        int c = obj; // auto-unboxing : obj는 주소이지만 랩퍼 객체에서 값을 자동으로 추출해준다.
    
        // 개발자가 명시적으로 랩퍼 객체를 만들어 값을 담기
        Integer obj2 = new Integer(a);
        Integer obj3 = a; // auto-boxing : 원시 타입의 값을 자동으로 랩퍼 객체를 만들어 담는다.

    }
```