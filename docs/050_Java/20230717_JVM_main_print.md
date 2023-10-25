---
layout: default
title: JVM, main, print
parent: Java
nav_order: 1
---

# Java

## 운영체제와 프로그램이란?

### 프로그램 (Program)
- 컴퓨터에서 실행될 때 특정 작업 (specific task)을 수행하는 일련의 명령어들의 모음(집합)

### 운영체제 (Operating System, OS)
- 시스템 하드웨어를 관리할 뿐 아니라 응용 소프트웨어를 실행하기 위하여 하드웨어 추상화 플랫폼과 공통 시스템 서비스를 제공하는 시스템 소프트웨어

## 컴퓨터의 자료표현

### 비트 (Bit)
- Binary Digit

### 바이트 (Byte)
- 4 bit

### 2진수 (Binary)
00001001 = 9
10001001 = -119
음수 표현 방식: 2의 보수법 -128~127까지 표현

## 자바 가상 머신 (JVM, Java Virtual Machine)이란?
- 자바 바이트코드를 실행할 수 있는 주체
- 자바 바이트코드는 플랫폼에 독립적이며 모든 JVM은 자바 가상 머신 규격에 정의된 대로 자바 바이트코드를 실행
- JVM은 OS에 의존적이다.
- Java 바이트코드는 운영체제와 상관없지만 이를 실행하기 위한 JVM은 운영체제와 연관이 있다.
- JRE: Java Runtime Environment, 자바 실행 환경
- JDK: Java Development Kit = JRE + 필요한 도구 (문서, 컴파일러 포함)

### main method
- 실행 명령인 Java를 실행 시 가장 먼저 호출되는 부분
- 만약, Application에서 main()가 없다면 실행될 수 없다.
- Application의 시작 -> 특정 클래스의 main() 실행
- 형태 (고정된 형태)
- public static void main(String[] args) {}

```java
import java.lang.*;

public class Hello {
	public static void main(String[] args) {
		System.out.println("Hello WORLD");
	}
}
```

### 주석 (Comment)

```java
// 주석 //
/* 주석 */
/** Document API를 위한 주석 처리 **/
```

### 출력문
- print: 줄바꿈 X
- println: 줄바꿈
- printf (format)
    - %d: 정수
    - %f: 실수
    - %c: 문자
    - %s: 문자열

```java
package java01.intro;

public class Intro03_PrintTest {
	public static void main(String[] args) {
		// print 를 써보자.
		System.out.print("Hello World");
		// \n 이라는것을 사용하면 줄이 바뀐다.
		// println을 써보자.
		System.out.println("Hello World!!!");
		System.out.println("Hello World!!!");

		System.out.println("\\");
		System.out.println("\"");

		System.out.printf("%d \n", 10); // 정수 (10진수)
		System.out.printf("%o \n", 10); // 정수 (8진수)
		System.out.printf("%X \n", 10); // 정수 (16진수)
		System.out.printf("%x \n", 10); // 정수 (16진수)

		System.out.printf("%4d\n", 10); // 4칸을 확보한뒤 오른쪽부터 차지
		System.out.printf("%-4d\n", 10); // 4칸을 확보한뒤 왼쪽부터 차지
		System.out.printf("%04d\n", 10); // 4칸을 확보한뒤 오른쪽부터 차지(빈칸 0을채운다.)

		System.out.printf("%f\n", 10.1); // 실수
		System.out.printf("%.2f\n", 10.1); // 실수 (소수점 둘째자리까지)

		System.out.printf("%s\n", "헤헤헤");
		System.out.printf("%c\n", 'o');
	}
}
```