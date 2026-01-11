# Java 기초 학습 가이드 (Chapter 1~3)

> 실습 위주로 배우는 Java 입문서

---

## Chapter 1: 자바를 시작하기 전에

### 1.1 자바(Java)란?

자바는 1995년 Sun Microsystems에서 개발한 객체지향 프로그래밍 언어입니다.

**자바의 핵심 특징:**
- **플랫폼 독립적**: "Write Once, Run Anywhere" - 한 번 작성하면 어디서든 실행
- **객체지향**: 코드를 재사용 가능한 객체 단위로 구성
- **자동 메모리 관리**: 가비지 컬렉터가 메모리를 자동으로 관리

### 1.2 JVM (Java Virtual Machine)

```
[Java 소스코드] → [컴파일러] → [바이트코드] → [JVM] → [실행]
   Hello.java       javac        Hello.class     java     결과출력
```

JVM은 바이트코드를 각 운영체제에 맞게 해석하여 실행합니다. 이것이 자바가 플랫폼 독립적인 이유입니다.

### 1.3 첫 번째 프로그램 작성하기

```java
// Hello.java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

**코드 설명:**
- `public class Hello`: Hello라는 이름의 클래스 선언 (파일명과 동일해야 함)
- `public static void main(String[] args)`: 프로그램의 시작점 (메인 메서드)
- `System.out.println()`: 화면에 출력하고 줄바꿈

**실행 방법:**
```bash
javac Hello.java    # 컴파일 → Hello.class 생성
java Hello          # 실행
```

### 📝 실습 1-1: 나만의 첫 프로그램

아래 코드를 작성하고 실행해보세요.

```java
public class MyFirstProgram {
    public static void main(String[] args) {
        System.out.println("안녕하세요!");
        System.out.println("저는 Java를 배우고 있습니다.");
        System.out.println("화이팅!");
    }
}
```

### 1.4 자주 발생하는 에러

| 에러 메시지 | 원인 | 해결 방법 |
|------------|------|----------|
| `cannot find symbol` | 변수명/메서드명 오타 | 철자 확인 |
| `';' expected` | 세미콜론 누락 | 문장 끝에 `;` 추가 |
| `class not found` | 클래스명과 파일명 불일치 | 파일명 = 클래스명.java |

---

## Chapter 2: 변수 (Variable)

### 2.1 변수란?

변수는 **데이터를 저장하는 공간**입니다. 메모리에 값을 저장하고 이름으로 접근합니다.

```java
int age = 25;        // 정수형 변수
double height = 175.5; // 실수형 변수
String name = "홍길동"; // 문자열 변수
```

### 2.2 변수의 선언과 초기화

```java
// 선언: 메모리 공간 확보
int number;

// 초기화: 값 저장
number = 10;

// 선언과 초기화 동시에
int count = 0;
```

### 2.3 변수 명명 규칙

```java
// ✅ 올바른 변수명
int studentAge;      // 카멜케이스 (권장)
int student_age;     // 스네이크케이스
int _count;          // 언더스코어로 시작 가능
int $price;          // 달러 기호로 시작 가능

// ❌ 잘못된 변수명
int 1stPlace;        // 숫자로 시작 불가
int my-name;         // 하이픈 사용 불가
int class;           // 예약어 사용 불가
```

### 2.4 기본형 (Primitive Type)

Java의 8가지 기본 데이터 타입:

```java
// 논리형
boolean isStudent = true;    // true 또는 false

// 문자형
char grade = 'A';            // 단일 문자 (작은따옴표)

// 정수형
byte b = 127;                // -128 ~ 127 (1byte)
short s = 32767;             // -32,768 ~ 32,767 (2byte)
int i = 2147483647;          // 약 ±21억 (4byte) ★가장 많이 사용
long l = 9223372036854775807L; // 매우 큰 정수 (8byte, L 접미사)

// 실수형
float f = 3.14f;             // 소수점 약 7자리 (4byte, f 접미사)
double d = 3.141592653589;   // 소수점 약 15자리 (8byte) ★기본형
```

### 📝 실습 2-1: 자기소개 변수

```java
public class SelfIntroduction {
    public static void main(String[] args) {
        // 여러분의 정보를 변수에 저장해보세요
        String name = "홍길동";
        int age = 25;
        double height = 175.5;
        boolean isStudent = true;
        char bloodType = 'A';
        
        System.out.println("이름: " + name);
        System.out.println("나이: " + age + "살");
        System.out.println("키: " + height + "cm");
        System.out.println("학생 여부: " + isStudent);
        System.out.println("혈액형: " + bloodType + "형");
    }
}
```

### 2.5 상수와 리터럴

```java
// 상수: 값이 변하지 않는 변수 (final 키워드)
final double PI = 3.14159;
final int MAX_SIZE = 100;

// PI = 3.14;  // ❌ 컴파일 에러! 상수는 변경 불가
```

**리터럴(Literal)**: 소스코드에 직접 작성된 값

```java
int num = 100;       // 100이 정수 리터럴
double d = 3.14;     // 3.14가 실수 리터럴
String s = "Hello";  // "Hello"가 문자열 리터럴
```

### 2.6 형식화된 출력 - printf()

```java
public class PrintfExample {
    public static void main(String[] args) {
        String name = "김철수";
        int age = 20;
        double score = 95.5;
        
        // printf: 형식 지정 출력 (줄바꿈 없음)
        System.out.printf("이름: %s%n", name);        // %s: 문자열
        System.out.printf("나이: %d세%n", age);       // %d: 정수
        System.out.printf("점수: %.1f점%n", score);   // %.1f: 소수점 1자리
        System.out.printf("합격률: %d%%%n", 85);      // %%: % 출력
        
        // 여러 값 동시 출력
        System.out.printf("%s님은 %d세이고, 점수는 %.2f입니다.%n", 
                          name, age, score);
    }
}
```

**주요 형식 지정자:**

| 지정자 | 설명 | 예시 |
|--------|------|------|
| `%d` | 10진수 정수 | `123` |
| `%f` | 실수 (기본 소수점 6자리) | `3.140000` |
| `%.2f` | 실수 (소수점 2자리) | `3.14` |
| `%s` | 문자열 | `"Hello"` |
| `%c` | 문자 | `'A'` |
| `%n` | 줄바꿈 | |
| `%5d` | 5자리 확보 (우측 정렬) | `  123` |
| `%-5d` | 5자리 확보 (좌측 정렬) | `123  ` |

### 2.7 Scanner로 입력받기

```java
import java.util.Scanner;  // Scanner 클래스 import

public class ScannerExample {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("이름을 입력하세요: ");
        String name = scanner.nextLine();    // 문자열 입력
        
        System.out.print("나이를 입력하세요: ");
        int age = scanner.nextInt();         // 정수 입력
        
        System.out.print("키를 입력하세요: ");
        double height = scanner.nextDouble(); // 실수 입력
        
        System.out.printf("%s님, %d세, %.1fcm%n", name, age, height);
        
        scanner.close();  // 스캐너 닫기
    }
}
```

**Scanner 주요 메서드:**

| 메서드 | 반환 타입 | 설명 |
|--------|----------|------|
| `nextLine()` | String | 한 줄 전체 |
| `next()` | String | 공백 전까지 |
| `nextInt()` | int | 정수 |
| `nextDouble()` | double | 실수 |
| `nextBoolean()` | boolean | true/false |

### 📝 실습 2-2: 간단한 계산기

```java
import java.util.Scanner;

public class SimpleCalculator {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        System.out.print("첫 번째 숫자: ");
        int num1 = sc.nextInt();
        
        System.out.print("두 번째 숫자: ");
        int num2 = sc.nextInt();
        
        int sum = num1 + num2;
        int diff = num1 - num2;
        int product = num1 * num2;
        double quotient = (double) num1 / num2;  // 형변환
        
        System.out.println("===== 계산 결과 =====");
        System.out.printf("%d + %d = %d%n", num1, num2, sum);
        System.out.printf("%d - %d = %d%n", num1, num2, diff);
        System.out.printf("%d × %d = %d%n", num1, num2, product);
        System.out.printf("%d ÷ %d = %.2f%n", num1, num2, quotient);
        
        sc.close();
    }
}
```

### 2.8 형변환 (Type Casting)

```java
public class TypeCasting {
    public static void main(String[] args) {
        // 자동 형변환 (작은 타입 → 큰 타입)
        int intVal = 100;
        long longVal = intVal;     // int → long (자동)
        double doubleVal = intVal; // int → double (자동)
        System.out.println(doubleVal);  // 100.0
        
        // 강제 형변환 (큰 타입 → 작은 타입)
        double pi = 3.14159;
        int intPi = (int) pi;      // double → int (강제)
        System.out.println(intPi);  // 3 (소수점 버림)
        
        // 정수 나눗셈 vs 실수 나눗셈
        int a = 5, b = 2;
        System.out.println(a / b);           // 2 (정수 나눗셈)
        System.out.println((double) a / b);  // 2.5 (실수 나눗셈)
    }
}
```

**형변환 순서 (작은 → 큰):**
```
byte → short → int → long → float → double
         ↑
        char
```

---

## Chapter 3: 연산자 (Operator)

### 3.1 연산자란?

연산자는 연산을 수행하는 기호입니다.

```java
int result = 10 + 20;  // +가 연산자, 10과 20이 피연산자
```

### 3.2 산술 연산자

```java
public class ArithmeticOperator {
    public static void main(String[] args) {
        int a = 10, b = 3;
        
        System.out.println("a + b = " + (a + b));  // 13 (덧셈)
        System.out.println("a - b = " + (a - b));  // 7  (뺄셈)
        System.out.println("a * b = " + (a * b));  // 30 (곱셈)
        System.out.println("a / b = " + (a / b));  // 3  (나눗셈 - 몫)
        System.out.println("a % b = " + (a % b));  // 1  (나머지)
    }
}
```

**나머지 연산자(%) 활용:**

```java
int num = 17;

// 짝수/홀수 판별
if (num % 2 == 0) {
    System.out.println("짝수");
} else {
    System.out.println("홀수");
}

// 3의 배수 판별
if (num % 3 == 0) {
    System.out.println("3의 배수");
}
```

### 3.3 증감 연산자 (++, --)

```java
public class IncrementOperator {
    public static void main(String[] args) {
        int a = 5;
        
        // 전위 연산자: 먼저 증가 후 사용
        System.out.println(++a);  // 6 (a를 먼저 증가시킨 후 출력)
        
        // 후위 연산자: 먼저 사용 후 증가
        System.out.println(a++);  // 6 (a를 출력 후 증가)
        System.out.println(a);    // 7
        
        int b = 10;
        int c = ++b;  // b=11, c=11 (먼저 증가)
        int d = b++;  // d=11, b=12 (먼저 대입)
        
        System.out.printf("b=%d, c=%d, d=%d%n", b, c, d);
    }
}
```

### 📝 실습 3-1: 증감 연산자 이해하기

```java
public class IncrementPractice {
    public static void main(String[] args) {
        int x = 5;
        
        // 각 줄의 출력 결과를 예측해보세요
        System.out.println(x);     // ?
        System.out.println(x++);   // ?
        System.out.println(x);     // ?
        System.out.println(++x);   // ?
        System.out.println(x);     // ?
    }
}
```

**정답:** 5, 5, 6, 7, 7

### 3.4 비교 연산자

```java
public class ComparisonOperator {
    public static void main(String[] args) {
        int a = 10, b = 20;
        
        System.out.println(a > b);   // false (크다)
        System.out.println(a < b);   // true  (작다)
        System.out.println(a >= 10); // true  (크거나 같다)
        System.out.println(a <= b);  // true  (작거나 같다)
        System.out.println(a == b);  // false (같다)
        System.out.println(a != b);  // true  (같지 않다)
        
        // 문자열 비교는 equals() 사용
        String s1 = "hello";
        String s2 = "hello";
        System.out.println(s1.equals(s2));  // true
    }
}
```

⚠️ **주의:** 문자열 비교 시 `==` 대신 `equals()` 메서드를 사용하세요!

### 3.5 논리 연산자

```java
public class LogicalOperator {
    public static void main(String[] args) {
        boolean a = true, b = false;
        
        // AND (&&): 둘 다 true여야 true
        System.out.println(a && b);  // false
        System.out.println(a && a);  // true
        
        // OR (||): 하나라도 true면 true
        System.out.println(a || b);  // true
        System.out.println(b || b);  // false
        
        // NOT (!): 반대로
        System.out.println(!a);      // false
        System.out.println(!b);      // true
        
        // 실제 예시
        int age = 25;
        boolean hasMoney = true;
        
        // 20세 이상이고 돈이 있으면
        if (age >= 20 && hasMoney) {
            System.out.println("입장 가능");
        }
        
        // 미성년자이거나 돈이 없으면
        if (age < 20 || !hasMoney) {
            System.out.println("입장 불가");
        }
    }
}
```

**논리 연산자 진리표:**

| A | B | A && B | A \|\| B | !A |
|---|---|--------|----------|-----|
| T | T | T | T | F |
| T | F | F | T | F |
| F | T | F | T | T |
| F | F | F | F | T |

### 3.6 대입 연산자

```java
public class AssignmentOperator {
    public static void main(String[] args) {
        int a = 10;
        
        a += 5;   // a = a + 5;  → a = 15
        a -= 3;   // a = a - 3;  → a = 12
        a *= 2;   // a = a * 2;  → a = 24
        a /= 4;   // a = a / 4;  → a = 6
        a %= 4;   // a = a % 4;  → a = 2
        
        System.out.println(a);  // 2
    }
}
```

### 3.7 삼항 연산자

```java
// 형식: 조건 ? 참일때값 : 거짓일때값

public class TernaryOperator {
    public static void main(String[] args) {
        int score = 85;
        
        // if-else를 한 줄로
        String result = (score >= 60) ? "합격" : "불합격";
        System.out.println(result);  // 합격
        
        // 최댓값 구하기
        int a = 10, b = 20;
        int max = (a > b) ? a : b;
        System.out.println("최댓값: " + max);  // 20
        
        // 절댓값 구하기
        int num = -5;
        int abs = (num >= 0) ? num : -num;
        System.out.println("절댓값: " + abs);  // 5
    }
}
```

### 3.8 연산자 우선순위

```java
// 괄호 > 산술 > 비교 > 논리 > 대입
int result = 2 + 3 * 4;      // 14 (곱셈 먼저)
int result2 = (2 + 3) * 4;   // 20 (괄호 먼저)

boolean b = 3 > 2 && 5 < 10; // true (비교 먼저, 논리 나중)
```

**우선순위 표:**

| 우선순위 | 연산자 | 설명 |
|---------|--------|------|
| 1 | `()` | 괄호 |
| 2 | `++` `--` `!` | 단항 연산자 |
| 3 | `*` `/` `%` | 곱셈, 나눗셈 |
| 4 | `+` `-` | 덧셈, 뺄셈 |
| 5 | `<` `>` `<=` `>=` | 비교 |
| 6 | `==` `!=` | 등가 비교 |
| 7 | `&&` | 논리 AND |
| 8 | `||` | 논리 OR |
| 9 | `=` `+=` `-=` 등 | 대입 |

### 📝 실습 3-2: 성적 등급 계산기

```java
import java.util.Scanner;

public class GradeCalculator {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        System.out.print("점수를 입력하세요 (0-100): ");
        int score = sc.nextInt();
        
        // 삼항 연산자로 등급 계산
        char grade = (score >= 90) ? 'A' :
                     (score >= 80) ? 'B' :
                     (score >= 70) ? 'C' :
                     (score >= 60) ? 'D' : 'F';
        
        // 합격 여부 (60점 이상)
        String passOrFail = (score >= 60) ? "합격" : "불합격";
        
        System.out.printf("점수: %d점%n", score);
        System.out.printf("등급: %c%n", grade);
        System.out.printf("결과: %s%n", passOrFail);
        
        sc.close();
    }
}
```

### 📝 실습 3-3: 종합 연습문제

```java
import java.util.Scanner;

public class ComprehensivePractice {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        // 1. 두 수를 입력받아 사칙연산 수행
        System.out.print("첫 번째 정수: ");
        int x = sc.nextInt();
        System.out.print("두 번째 정수: ");
        int y = sc.nextInt();
        
        System.out.println("\n===== 사칙연산 =====");
        System.out.printf("%d + %d = %d%n", x, y, x + y);
        System.out.printf("%d - %d = %d%n", x, y, x - y);
        System.out.printf("%d * %d = %d%n", x, y, x * y);
        System.out.printf("%d / %d = %.2f%n", x, y, (double)x / y);
        System.out.printf("%d %% %d = %d%n", x, y, x % y);
        
        // 2. 비교 연산
        System.out.println("\n===== 비교 연산 =====");
        System.out.printf("%d > %d : %b%n", x, y, x > y);
        System.out.printf("%d == %d : %b%n", x, y, x == y);
        
        // 3. 논리 연산 응용
        System.out.println("\n===== 분석 =====");
        boolean bothPositive = (x > 0) && (y > 0);
        boolean eitherEven = (x % 2 == 0) || (y % 2 == 0);
        int max = (x > y) ? x : y;
        int min = (x < y) ? x : y;
        
        System.out.printf("둘 다 양수인가? %b%n", bothPositive);
        System.out.printf("하나라도 짝수인가? %b%n", eitherEven);
        System.out.printf("최댓값: %d, 최솟값: %d%n", max, min);
        
        sc.close();
    }
}
```

---

## 정리: Chapter 1~3 핵심 요약

### Chapter 1
- Java는 플랫폼 독립적인 객체지향 언어
- JVM이 바이트코드를 해석하여 실행
- `public static void main(String[] args)`가 프로그램 시작점

### Chapter 2
- 변수는 데이터를 저장하는 공간
- 8가지 기본형: boolean, char, byte, short, int, long, float, double
- `final`로 상수 선언
- `Scanner`로 사용자 입력 받기
- 형변환: 자동(작은→큰), 강제(큰→작은)

### Chapter 3
- 산술: `+` `-` `*` `/` `%`
- 증감: `++` `--` (전위/후위)
- 비교: `>` `<` `>=` `<=` `==` `!=`
- 논리: `&&` `||` `!`
- 대입: `=` `+=` `-=` `*=` `/=` `%=`
- 삼항: `조건 ? 참 : 거짓`

---

## 추가 연습 문제

1. **온도 변환기**: 섭씨를 입력받아 화씨로 변환 (공식: F = C × 9/5 + 32)
2. **윤년 판별기**: 연도를 입력받아 윤년인지 판별 (4의 배수이면서 100의 배수가 아니거나, 400의 배수)
3. **BMI 계산기**: 키(m)와 몸무게(kg)를 입력받아 BMI와 비만도 출력

```java
// 힌트: 윤년 조건
boolean isLeapYear = (year % 4 == 0 && year % 100 != 0) || (year % 400 == 0);
```
