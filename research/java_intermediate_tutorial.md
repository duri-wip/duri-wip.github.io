# Java 기초 학습 가이드 (Chapter 4~6)

> 실습 위주로 배우는 Java 입문서 - 제어문과 객체지향

---

## Chapter 4: 조건문과 반복문

### 4.1 조건문이란?

조건문은 **조건에 따라 다른 코드를 실행**하게 해줍니다.

### 4.2 if문

```java
public class IfExample {
    public static void main(String[] args) {
        int score = 85;
        
        // 기본 if문
        if (score >= 60) {
            System.out.println("합격입니다!");
        }
        
        // if-else문
        if (score >= 60) {
            System.out.println("합격");
        } else {
            System.out.println("불합격");
        }
        
        // if-else if-else문
        if (score >= 90) {
            System.out.println("A등급");
        } else if (score >= 80) {
            System.out.println("B등급");
        } else if (score >= 70) {
            System.out.println("C등급");
        } else if (score >= 60) {
            System.out.println("D등급");
        } else {
            System.out.println("F등급");
        }
    }
}
```

### 4.3 중첩 if문

```java
public class NestedIf {
    public static void main(String[] args) {
        int score = 85;
        boolean isAttended = true;
        
        if (score >= 60) {
            if (isAttended) {
                System.out.println("합격! 출석 인정");
            } else {
                System.out.println("점수는 통과했지만 출석 미달");
            }
        } else {
            System.out.println("점수 미달");
        }
    }
}
```

### 📝 실습 4-1: 로그인 시스템

```java
import java.util.Scanner;

public class LoginSystem {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        String correctId = "admin";
        String correctPw = "1234";
        
        System.out.print("아이디: ");
        String inputId = sc.nextLine();
        
        System.out.print("비밀번호: ");
        String inputPw = sc.nextLine();
        
        if (inputId.equals(correctId)) {
            if (inputPw.equals(correctPw)) {
                System.out.println("로그인 성공!");
            } else {
                System.out.println("비밀번호가 틀렸습니다.");
            }
        } else {
            System.out.println("존재하지 않는 아이디입니다.");
        }
        
        sc.close();
    }
}
```

### 4.4 switch문

여러 값 중 하나를 선택할 때 if-else if보다 깔끔합니다.

```java
public class SwitchExample {
    public static void main(String[] args) {
        int month = 8;
        String season;
        
        switch (month) {
            case 3: case 4: case 5:
                season = "봄";
                break;
            case 6: case 7: case 8:
                season = "여름";
                break;
            case 9: case 10: case 11:
                season = "가을";
                break;
            case 12: case 1: case 2:
                season = "겨울";
                break;
            default:
                season = "잘못된 월";
        }
        
        System.out.println(month + "월은 " + season + "입니다.");
    }
}
```

**Java 14+ 향상된 switch (화살표 문법):**

```java
public class EnhancedSwitch {
    public static void main(String[] args) {
        int day = 3;
        
        String dayName = switch (day) {
            case 1 -> "월요일";
            case 2 -> "화요일";
            case 3 -> "수요일";
            case 4 -> "목요일";
            case 5 -> "금요일";
            case 6, 7 -> "주말";
            default -> "잘못된 입력";
        };
        
        System.out.println(dayName);  // 수요일
    }
}
```

### 📝 실습 4-2: 간단한 계산기 (switch)

```java
import java.util.Scanner;

public class Calculator {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        System.out.print("첫 번째 숫자: ");
        double num1 = sc.nextDouble();
        
        System.out.print("연산자 (+, -, *, /): ");
        char op = sc.next().charAt(0);
        
        System.out.print("두 번째 숫자: ");
        double num2 = sc.nextDouble();
        
        double result = 0;
        boolean valid = true;
        
        switch (op) {
            case '+':
                result = num1 + num2;
                break;
            case '-':
                result = num1 - num2;
                break;
            case '*':
                result = num1 * num2;
                break;
            case '/':
                if (num2 != 0) {
                    result = num1 / num2;
                } else {
                    System.out.println("0으로 나눌 수 없습니다!");
                    valid = false;
                }
                break;
            default:
                System.out.println("잘못된 연산자입니다.");
                valid = false;
        }
        
        if (valid) {
            System.out.printf("%.2f %c %.2f = %.2f%n", num1, op, num2, result);
        }
        
        sc.close();
    }
}
```

### 4.5 for문

**정해진 횟수**만큼 반복할 때 사용합니다.

```java
public class ForExample {
    public static void main(String[] args) {
        // 기본 for문: 1부터 5까지 출력
        for (int i = 1; i <= 5; i++) {
            System.out.println(i);
        }
        
        // 역순: 5부터 1까지
        for (int i = 5; i >= 1; i--) {
            System.out.println(i);
        }
        
        // 2씩 증가: 짝수만
        for (int i = 2; i <= 10; i += 2) {
            System.out.println(i);
        }
        
        // 1부터 10까지의 합
        int sum = 0;
        for (int i = 1; i <= 10; i++) {
            sum += i;
        }
        System.out.println("합계: " + sum);  // 55
    }
}
```

### 📝 실습 4-3: 구구단 출력

```java
public class MultiplicationTable {
    public static void main(String[] args) {
        int dan = 7;
        
        System.out.println("===== " + dan + "단 =====");
        for (int i = 1; i <= 9; i++) {
            System.out.printf("%d × %d = %d%n", dan, i, dan * i);
        }
    }
}
```

### 4.6 중첩 for문

```java
public class NestedFor {
    public static void main(String[] args) {
        // 구구단 전체 출력
        for (int dan = 2; dan <= 9; dan++) {
            System.out.println("===== " + dan + "단 =====");
            for (int i = 1; i <= 9; i++) {
                System.out.printf("%d × %d = %2d%n", dan, i, dan * i);
            }
            System.out.println();
        }
    }
}
```

### 📝 실습 4-4: 별 찍기

```java
public class StarPattern {
    public static void main(String[] args) {
        int n = 5;
        
        // 패턴 1: 직각삼각형
        // *
        // **
        // ***
        // ****
        // *****
        System.out.println("=== 패턴 1 ===");
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
        
        // 패턴 2: 역삼각형
        // *****
        // ****
        // ***
        // **
        // *
        System.out.println("\n=== 패턴 2 ===");
        for (int i = n; i >= 1; i--) {
            for (int j = 1; j <= i; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
        
        // 패턴 3: 피라미드
        //     *
        //    ***
        //   *****
        //  *******
        // *********
        System.out.println("\n=== 패턴 3 ===");
        for (int i = 1; i <= n; i++) {
            // 공백 출력
            for (int j = 1; j <= n - i; j++) {
                System.out.print(" ");
            }
            // 별 출력
            for (int j = 1; j <= 2 * i - 1; j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```

### 4.7 while문

**조건이 참인 동안** 반복합니다.

```java
public class WhileExample {
    public static void main(String[] args) {
        // 1부터 5까지 출력
        int i = 1;
        while (i <= 5) {
            System.out.println(i);
            i++;
        }
        
        // 숫자의 자릿수 구하기
        int num = 12345;
        int count = 0;
        int temp = num;
        
        while (temp > 0) {
            temp /= 10;
            count++;
        }
        System.out.println(num + "의 자릿수: " + count);  // 5
    }
}
```

### 📝 실습 4-5: 숫자 맞추기 게임

```java
import java.util.Scanner;
import java.util.Random;

public class NumberGuessing {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Random random = new Random();
        
        int answer = random.nextInt(100) + 1;  // 1~100
        int attempts = 0;
        int guess;
        
        System.out.println("1부터 100 사이의 숫자를 맞춰보세요!");
        
        while (true) {
            System.out.print("추측: ");
            guess = sc.nextInt();
            attempts++;
            
            if (guess == answer) {
                System.out.println("정답! " + attempts + "번 만에 맞췄습니다!");
                break;
            } else if (guess < answer) {
                System.out.println("더 큰 숫자입니다.");
            } else {
                System.out.println("더 작은 숫자입니다.");
            }
        }
        
        sc.close();
    }
}
```

### 4.8 do-while문

**최소 1번은 실행** 후 조건을 검사합니다.

```java
import java.util.Scanner;

public class DoWhileExample {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int input;
        
        // 메뉴 선택 (0 입력 시 종료)
        do {
            System.out.println("\n===== 메뉴 =====");
            System.out.println("1. 게임 시작");
            System.out.println("2. 설정");
            System.out.println("3. 도움말");
            System.out.println("0. 종료");
            System.out.print("선택: ");
            
            input = sc.nextInt();
            
            switch (input) {
                case 1 -> System.out.println("게임을 시작합니다!");
                case 2 -> System.out.println("설정 화면입니다.");
                case 3 -> System.out.println("도움말 화면입니다.");
                case 0 -> System.out.println("프로그램을 종료합니다.");
                default -> System.out.println("잘못된 입력입니다.");
            }
        } while (input != 0);
        
        sc.close();
    }
}
```

### 4.9 break와 continue

```java
public class BreakContinue {
    public static void main(String[] args) {
        // break: 반복문 즉시 탈출
        System.out.println("=== break 예제 ===");
        for (int i = 1; i <= 10; i++) {
            if (i == 5) {
                break;  // 5에서 종료
            }
            System.out.print(i + " ");
        }
        System.out.println();  // 1 2 3 4
        
        // continue: 현재 반복 건너뛰기
        System.out.println("=== continue 예제 ===");
        for (int i = 1; i <= 10; i++) {
            if (i % 2 == 0) {
                continue;  // 짝수는 건너뛰기
            }
            System.out.print(i + " ");
        }
        System.out.println();  // 1 3 5 7 9
    }
}
```

### 📝 실습 4-6: 소수 판별기

```java
import java.util.Scanner;

public class PrimeChecker {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        System.out.print("숫자를 입력하세요: ");
        int num = sc.nextInt();
        
        boolean isPrime = true;
        
        if (num <= 1) {
            isPrime = false;
        } else {
            for (int i = 2; i <= Math.sqrt(num); i++) {
                if (num % i == 0) {
                    isPrime = false;
                    break;
                }
            }
        }
        
        if (isPrime) {
            System.out.println(num + "은(는) 소수입니다.");
        } else {
            System.out.println(num + "은(는) 소수가 아닙니다.");
        }
        
        sc.close();
    }
}
```

---

## Chapter 5: 배열 (Array)

### 5.1 배열이란?

배열은 **같은 타입의 데이터를 연속적으로 저장**하는 자료구조입니다.

```java
// 개별 변수 vs 배열
int score1 = 90;
int score2 = 85;
int score3 = 80;

int[] scores = {90, 85, 80};  // 배열이 더 효율적!
```

### 5.2 배열의 선언과 생성

```java
public class ArrayBasic {
    public static void main(String[] args) {
        // 방법 1: 선언 후 생성
        int[] numbers;
        numbers = new int[5];  // 크기 5인 배열 생성
        
        // 방법 2: 선언과 생성 동시에
        int[] scores = new int[3];
        
        // 방법 3: 초기값과 함께 생성
        int[] ages = {20, 25, 30, 35};
        
        // 방법 4: new 키워드와 초기값
        String[] names = new String[]{"김철수", "이영희", "박민수"};
    }
}
```

### 5.3 배열의 인덱스와 길이

```java
public class ArrayIndex {
    public static void main(String[] args) {
        int[] arr = {10, 20, 30, 40, 50};
        
        // 인덱스는 0부터 시작!
        System.out.println(arr[0]);  // 10 (첫 번째)
        System.out.println(arr[2]);  // 30 (세 번째)
        System.out.println(arr[4]);  // 50 (마지막)
        
        // 배열 길이
        System.out.println("길이: " + arr.length);  // 5
        
        // 마지막 요소
        System.out.println("마지막: " + arr[arr.length - 1]);  // 50
        
        // 값 변경
        arr[1] = 200;
        System.out.println(arr[1]);  // 200
    }
}
```

⚠️ **주의:** 배열 범위를 벗어나면 `ArrayIndexOutOfBoundsException` 발생!

### 5.4 배열과 반복문

```java
public class ArrayLoop {
    public static void main(String[] args) {
        int[] scores = {85, 90, 78, 92, 88};
        
        // 방법 1: 일반 for문
        System.out.println("=== 일반 for문 ===");
        for (int i = 0; i < scores.length; i++) {
            System.out.println("scores[" + i + "] = " + scores[i]);
        }
        
        // 방법 2: 향상된 for문 (for-each)
        System.out.println("\n=== 향상된 for문 ===");
        for (int score : scores) {
            System.out.println("점수: " + score);
        }
        
        // 합계와 평균
        int sum = 0;
        for (int score : scores) {
            sum += score;
        }
        double avg = (double) sum / scores.length;
        
        System.out.printf("\n합계: %d, 평균: %.2f%n", sum, avg);
    }
}
```

### 📝 실습 5-1: 성적 관리 프로그램

```java
import java.util.Scanner;

public class GradeManager {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        System.out.print("학생 수를 입력하세요: ");
        int n = sc.nextInt();
        
        int[] scores = new int[n];
        
        // 점수 입력
        for (int i = 0; i < n; i++) {
            System.out.print((i + 1) + "번 학생 점수: ");
            scores[i] = sc.nextInt();
        }
        
        // 통계 계산
        int sum = 0;
        int max = scores[0];
        int min = scores[0];
        
        for (int score : scores) {
            sum += score;
            if (score > max) max = score;
            if (score < min) min = score;
        }
        
        double avg = (double) sum / n;
        
        // 결과 출력
        System.out.println("\n===== 성적 통계 =====");
        System.out.println("총점: " + sum);
        System.out.printf("평균: %.2f%n", avg);
        System.out.println("최고점: " + max);
        System.out.println("최저점: " + min);
        
        sc.close();
    }
}
```

### 5.5 배열의 복사

```java
import java.util.Arrays;

public class ArrayCopy {
    public static void main(String[] args) {
        int[] original = {1, 2, 3, 4, 5};
        
        // 방법 1: 반복문
        int[] copy1 = new int[original.length];
        for (int i = 0; i < original.length; i++) {
            copy1[i] = original[i];
        }
        
        // 방법 2: System.arraycopy()
        int[] copy2 = new int[original.length];
        System.arraycopy(original, 0, copy2, 0, original.length);
        
        // 방법 3: Arrays.copyOf()
        int[] copy3 = Arrays.copyOf(original, original.length);
        
        // 방법 4: clone()
        int[] copy4 = original.clone();
        
        // 출력
        System.out.println(Arrays.toString(copy3));  // [1, 2, 3, 4, 5]
    }
}
```

### 5.6 Arrays 유틸리티 클래스

```java
import java.util.Arrays;

public class ArraysUtility {
    public static void main(String[] args) {
        int[] arr = {5, 2, 8, 1, 9, 3};
        
        // 배열 출력
        System.out.println(Arrays.toString(arr));  // [5, 2, 8, 1, 9, 3]
        
        // 정렬
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));  // [1, 2, 3, 5, 8, 9]
        
        // 이진 탐색 (정렬된 배열에서만!)
        int index = Arrays.binarySearch(arr, 5);
        System.out.println("5의 위치: " + index);  // 3
        
        // 배열 채우기
        int[] filled = new int[5];
        Arrays.fill(filled, 7);
        System.out.println(Arrays.toString(filled));  // [7, 7, 7, 7, 7]
        
        // 배열 비교
        int[] a = {1, 2, 3};
        int[] b = {1, 2, 3};
        System.out.println(Arrays.equals(a, b));  // true
    }
}
```

### 5.7 String 배열

```java
public class StringArray {
    public static void main(String[] args) {
        String[] fruits = {"사과", "바나나", "오렌지", "포도"};
        
        // 출력
        for (String fruit : fruits) {
            System.out.println(fruit);
        }
        
        // 특정 문자열 찾기
        String target = "바나나";
        boolean found = false;
        
        for (int i = 0; i < fruits.length; i++) {
            if (fruits[i].equals(target)) {
                System.out.println(target + "은(는) " + i + "번 인덱스에 있습니다.");
                found = true;
                break;
            }
        }
        
        if (!found) {
            System.out.println(target + "을(를) 찾지 못했습니다.");
        }
    }
}
```

### 5.8 2차원 배열

```java
public class TwoDimensionalArray {
    public static void main(String[] args) {
        // 2차원 배열 선언
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        // 특정 요소 접근
        System.out.println(matrix[0][0]);  // 1 (0행 0열)
        System.out.println(matrix[1][2]);  // 6 (1행 2열)
        
        // 전체 출력
        System.out.println("=== 행렬 출력 ===");
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                System.out.print(matrix[i][j] + " ");
            }
            System.out.println();
        }
        
        // 향상된 for문 사용
        System.out.println("\n=== for-each 사용 ===");
        for (int[] row : matrix) {
            for (int value : row) {
                System.out.print(value + " ");
            }
            System.out.println();
        }
    }
}
```

### 📝 실습 5-2: 2차원 배열 - 성적표

```java
public class GradeTable {
    public static void main(String[] args) {
        // 3명의 학생, 4개 과목
        String[] students = {"김철수", "이영희", "박민수"};
        String[] subjects = {"국어", "영어", "수학", "과학"};
        
        int[][] scores = {
            {85, 90, 78, 92},  // 김철수
            {88, 85, 95, 80},  // 이영희
            {90, 88, 82, 88}   // 박민수
        };
        
        // 성적표 출력
        System.out.println("===== 성적표 =====");
        System.out.print("이름\t");
        for (String subject : subjects) {
            System.out.print(subject + "\t");
        }
        System.out.println("평균");
        System.out.println("-".repeat(40));
        
        for (int i = 0; i < students.length; i++) {
            System.out.print(students[i] + "\t");
            
            int sum = 0;
            for (int j = 0; j < subjects.length; j++) {
                System.out.print(scores[i][j] + "\t");
                sum += scores[i][j];
            }
            
            double avg = (double) sum / subjects.length;
            System.out.printf("%.1f%n", avg);
        }
        
        // 과목별 평균
        System.out.println("-".repeat(40));
        System.out.print("과목평균\t");
        for (int j = 0; j < subjects.length; j++) {
            int sum = 0;
            for (int i = 0; i < students.length; i++) {
                sum += scores[i][j];
            }
            System.out.printf("%.1f\t", (double) sum / students.length);
        }
        System.out.println();
    }
}
```

### 📝 실습 5-3: 로또 번호 생성기

```java
import java.util.Arrays;
import java.util.Random;

public class LottoGenerator {
    public static void main(String[] args) {
        int[] lotto = new int[6];
        Random random = new Random();
        
        int count = 0;
        while (count < 6) {
            int num = random.nextInt(45) + 1;  // 1~45
            
            // 중복 체크
            boolean isDuplicate = false;
            for (int i = 0; i < count; i++) {
                if (lotto[i] == num) {
                    isDuplicate = true;
                    break;
                }
            }
            
            if (!isDuplicate) {
                lotto[count] = num;
                count++;
            }
        }
        
        // 정렬
        Arrays.sort(lotto);
        
        // 출력
        System.out.println("이번 주 로또 번호");
        System.out.println(Arrays.toString(lotto));
    }
}
```

---

## Chapter 6: 객체지향 프로그래밍 I

### 6.1 객체지향이란?

객체지향 프로그래밍(OOP)은 **현실 세계의 사물을 객체로 모델링**하는 프로그래밍 방식입니다.

**핵심 개념:**
- **클래스(Class)**: 객체를 만들기 위한 설계도
- **객체(Object)**: 클래스로부터 만들어진 실체
- **인스턴스(Instance)**: 특정 클래스의 객체

```
클래스(설계도)  →  객체(실체)
    자동차     →  내 차, 친구 차, 택시...
```

### 6.2 클래스와 객체

```java
// 클래스 정의 (설계도)
class Dog {
    // 속성 (필드)
    String name;
    int age;
    String breed;
    
    // 기능 (메서드)
    void bark() {
        System.out.println(name + "가 멍멍 짖습니다!");
    }
    
    void eat(String food) {
        System.out.println(name + "가 " + food + "을(를) 먹습니다.");
    }
}

// 객체 생성 및 사용
public class DogExample {
    public static void main(String[] args) {
        // 객체 생성
        Dog myDog = new Dog();
        
        // 속성 설정
        myDog.name = "바둑이";
        myDog.age = 3;
        myDog.breed = "진돗개";
        
        // 메서드 호출
        myDog.bark();           // 바둑이가 멍멍 짖습니다!
        myDog.eat("사료");       // 바둑이가 사료을(를) 먹습니다.
        
        // 다른 객체 생성
        Dog friendDog = new Dog();
        friendDog.name = "초코";
        friendDog.age = 2;
        friendDog.bark();       // 초코가 멍멍 짖습니다!
    }
}
```

### 📝 실습 6-1: 학생 클래스

```java
class Student {
    // 필드 (속성)
    String name;
    int studentId;
    int grade;
    double gpa;
    
    // 메서드 (기능)
    void introduce() {
        System.out.println("안녕하세요, " + name + "입니다.");
        System.out.println("학번: " + studentId);
        System.out.println("학년: " + grade + "학년");
        System.out.printf("학점: %.2f%n", gpa);
    }
    
    void study(String subject) {
        System.out.println(name + "이(가) " + subject + "을(를) 공부합니다.");
    }
    
    boolean isPassing() {
        return gpa >= 2.0;
    }
}

public class StudentExample {
    public static void main(String[] args) {
        Student s1 = new Student();
        s1.name = "김철수";
        s1.studentId = 20230001;
        s1.grade = 1;
        s1.gpa = 3.5;
        
        s1.introduce();
        s1.study("자바 프로그래밍");
        System.out.println("학사 경고: " + (s1.isPassing() ? "아니오" : "예"));
    }
}
```

### 6.3 변수의 종류

```java
class VariableTypes {
    // 클래스 변수 (static): 모든 인스턴스가 공유
    static int classCount = 0;
    
    // 인스턴스 변수: 각 인스턴스마다 별도
    String instanceName;
    
    void method() {
        // 지역 변수: 메서드 내에서만 유효
        int localVar = 10;
        System.out.println(localVar);
    }
}

public class VariableExample {
    public static void main(String[] args) {
        // 클래스 변수는 클래스명으로 접근
        System.out.println(VariableTypes.classCount);
        
        VariableTypes v1 = new VariableTypes();
        v1.instanceName = "첫번째";
        VariableTypes.classCount++;
        
        VariableTypes v2 = new VariableTypes();
        v2.instanceName = "두번째";
        VariableTypes.classCount++;
        
        // 클래스 변수는 공유됨
        System.out.println(VariableTypes.classCount);  // 2
    }
}
```

### 6.4 메서드

```java
class Calculator {
    // 반환값 없는 메서드
    void printMessage(String msg) {
        System.out.println(msg);
    }
    
    // 반환값 있는 메서드
    int add(int a, int b) {
        return a + b;
    }
    
    double divide(double a, double b) {
        if (b == 0) {
            System.out.println("0으로 나눌 수 없습니다.");
            return 0;
        }
        return a / b;
    }
    
    // 여러 값 계산 후 배열로 반환
    int[] calculate(int a, int b) {
        int[] results = new int[4];
        results[0] = a + b;
        results[1] = a - b;
        results[2] = a * b;
        results[3] = (b != 0) ? a / b : 0;
        return results;
    }
}

public class MethodExample {
    public static void main(String[] args) {
        Calculator calc = new Calculator();
        
        calc.printMessage("계산기 시작!");
        
        int sum = calc.add(10, 20);
        System.out.println("합: " + sum);
        
        double result = calc.divide(10, 3);
        System.out.printf("나눗셈: %.2f%n", result);
        
        int[] all = calc.calculate(10, 3);
        System.out.println("덧셈: " + all[0]);
        System.out.println("뺄셈: " + all[1]);
        System.out.println("곱셈: " + all[2]);
        System.out.println("나눗셈: " + all[3]);
    }
}
```

### 6.5 오버로딩 (Overloading)

**같은 이름의 메서드를 매개변수만 다르게** 여러 개 정의하는 것입니다.

```java
class PrintUtil {
    // 오버로딩: 같은 이름, 다른 매개변수
    void print(int n) {
        System.out.println("정수: " + n);
    }
    
    void print(double d) {
        System.out.println("실수: " + d);
    }
    
    void print(String s) {
        System.out.println("문자열: " + s);
    }
    
    void print(int a, int b) {
        System.out.println("두 정수: " + a + ", " + b);
    }
}

public class OverloadingExample {
    public static void main(String[] args) {
        PrintUtil util = new PrintUtil();
        
        util.print(10);          // 정수: 10
        util.print(3.14);        // 실수: 3.14
        util.print("Hello");     // 문자열: Hello
        util.print(1, 2);        // 두 정수: 1, 2
    }
}
```

### 6.6 생성자 (Constructor)

**객체가 생성될 때 자동으로 호출**되는 특별한 메서드입니다.

```java
class Person {
    String name;
    int age;
    
    // 기본 생성자
    Person() {
        name = "이름없음";
        age = 0;
        System.out.println("기본 생성자 호출");
    }
    
    // 매개변수가 있는 생성자
    Person(String name) {
        this.name = name;  // this: 현재 객체를 가리킴
        this.age = 0;
        System.out.println("이름 생성자 호출");
    }
    
    // 모든 필드를 초기화하는 생성자
    Person(String name, int age) {
        this.name = name;
        this.age = age;
        System.out.println("전체 생성자 호출");
    }
    
    void introduce() {
        System.out.println("이름: " + name + ", 나이: " + age);
    }
}

public class ConstructorExample {
    public static void main(String[] args) {
        Person p1 = new Person();               // 기본 생성자
        Person p2 = new Person("김철수");        // 이름만
        Person p3 = new Person("이영희", 25);    // 이름, 나이
        
        p1.introduce();  // 이름: 이름없음, 나이: 0
        p2.introduce();  // 이름: 김철수, 나이: 0
        p3.introduce();  // 이름: 이영희, 나이: 25
    }
}
```

### 6.7 this 키워드

```java
class Account {
    String owner;
    int balance;
    
    // this(): 같은 클래스의 다른 생성자 호출
    Account() {
        this("무명", 0);  // 다른 생성자 호출
    }
    
    Account(String owner) {
        this(owner, 0);
    }
    
    Account(String owner, int balance) {
        this.owner = owner;      // this.필드 = 매개변수
        this.balance = balance;
    }
    
    // this를 반환 (메서드 체이닝용)
    Account deposit(int amount) {
        balance += amount;
        return this;
    }
    
    Account withdraw(int amount) {
        if (balance >= amount) {
            balance -= amount;
        }
        return this;
    }
    
    void showBalance() {
        System.out.println(owner + "의 잔액: " + balance + "원");
    }
}

public class ThisExample {
    public static void main(String[] args) {
        Account acc = new Account("김철수", 10000);
        
        // 메서드 체이닝
        acc.deposit(5000)
           .withdraw(3000)
           .deposit(1000)
           .showBalance();  // 김철수의 잔액: 13000원
    }
}
```

### 📝 실습 6-2: 자동차 클래스

```java
class Car {
    // 필드
    String brand;
    String model;
    int year;
    int speed;
    boolean isRunning;
    
    // 생성자
    Car(String brand, String model, int year) {
        this.brand = brand;
        this.model = model;
        this.year = year;
        this.speed = 0;
        this.isRunning = false;
    }
    
    // 메서드
    void start() {
        if (!isRunning) {
            isRunning = true;
            System.out.println(brand + " " + model + " 시동을 겁니다.");
        } else {
            System.out.println("이미 시동이 걸려있습니다.");
        }
    }
    
    void stop() {
        if (isRunning) {
            isRunning = false;
            speed = 0;
            System.out.println("시동을 끕니다.");
        } else {
            System.out.println("이미 시동이 꺼져있습니다.");
        }
    }
    
    void accelerate(int amount) {
        if (isRunning) {
            speed += amount;
            if (speed > 200) speed = 200;  // 최대 속도 제한
            System.out.println("가속! 현재 속도: " + speed + "km/h");
        } else {
            System.out.println("먼저 시동을 걸어주세요.");
        }
    }
    
    void brake(int amount) {
        if (isRunning) {
            speed -= amount;
            if (speed < 0) speed = 0;
            System.out.println("감속! 현재 속도: " + speed + "km/h");
        }
    }
    
    void showInfo() {
        System.out.println("\n===== 차량 정보 =====");
        System.out.println("브랜드: " + brand);
        System.out.println("모델: " + model);
        System.out.println("연식: " + year);
        System.out.println("현재 속도: " + speed + "km/h");
        System.out.println("시동 상태: " + (isRunning ? "ON" : "OFF"));
    }
}

public class CarExample {
    public static void main(String[] args) {
        Car myCar = new Car("현대", "소나타", 2023);
        
        myCar.showInfo();
        
        myCar.accelerate(50);  // 시동 안 걸림
        
        myCar.start();
        myCar.accelerate(50);
        myCar.accelerate(30);
        myCar.brake(20);
        
        myCar.showInfo();
        
        myCar.stop();
    }
}
```

### 📝 실습 6-3: 은행 계좌 관리 시스템

```java
import java.util.Scanner;

class BankAccount {
    private String accountNumber;
    private String owner;
    private int balance;
    
    BankAccount(String accountNumber, String owner) {
        this.accountNumber = accountNumber;
        this.owner = owner;
        this.balance = 0;
    }
    
    BankAccount(String accountNumber, String owner, int initialBalance) {
        this.accountNumber = accountNumber;
        this.owner = owner;
        this.balance = initialBalance;
    }
    
    void deposit(int amount) {
        if (amount > 0) {
            balance += amount;
            System.out.println(amount + "원이 입금되었습니다.");
        } else {
            System.out.println("유효하지 않은 금액입니다.");
        }
    }
    
    void withdraw(int amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            System.out.println(amount + "원이 출금되었습니다.");
        } else if (amount > balance) {
            System.out.println("잔액이 부족합니다. (현재 잔액: " + balance + "원)");
        } else {
            System.out.println("유효하지 않은 금액입니다.");
        }
    }
    
    void transfer(BankAccount target, int amount) {
        if (amount > 0 && amount <= balance) {
            this.balance -= amount;
            target.balance += amount;
            System.out.println(target.owner + "님에게 " + amount + "원을 이체했습니다.");
        } else {
            System.out.println("이체할 수 없습니다.");
        }
    }
    
    void showInfo() {
        System.out.println("\n===== 계좌 정보 =====");
        System.out.println("계좌번호: " + accountNumber);
        System.out.println("예금주: " + owner);
        System.out.println("잔액: " + balance + "원");
    }
    
    int getBalance() {
        return balance;
    }
}

public class BankSystem {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        // 계좌 생성
        BankAccount account1 = new BankAccount("1234-5678", "김철수", 100000);
        BankAccount account2 = new BankAccount("9876-5432", "이영희", 50000);
        
        // 메뉴
        int choice;
        do {
            System.out.println("\n===== 은행 시스템 =====");
            System.out.println("1. 잔액 조회");
            System.out.println("2. 입금");
            System.out.println("3. 출금");
            System.out.println("4. 이체");
            System.out.println("0. 종료");
            System.out.print("선택: ");
            choice = sc.nextInt();
            
            switch (choice) {
                case 1:
                    account1.showInfo();
                    break;
                case 2:
                    System.out.print("입금액: ");
                    account1.deposit(sc.nextInt());
                    break;
                case 3:
                    System.out.print("출금액: ");
                    account1.withdraw(sc.nextInt());
                    break;
                case 4:
                    System.out.print("이체금액: ");
                    account1.transfer(account2, sc.nextInt());
                    account1.showInfo();
                    account2.showInfo();
                    break;
                case 0:
                    System.out.println("이용해주셔서 감사합니다.");
                    break;
                default:
                    System.out.println("잘못된 선택입니다.");
            }
        } while (choice != 0);
        
        sc.close();
    }
}
```

---

## 정리: Chapter 4~6 핵심 요약

### Chapter 4 - 조건문과 반복문
- `if`, `if-else`, `if-else if-else`: 조건 분기
- `switch`: 다중 선택 (Java 14+: 화살표 문법)
- `for`: 정해진 횟수 반복
- `while`: 조건이 참인 동안 반복
- `do-while`: 최소 1회 실행 후 반복
- `break`: 반복문 탈출
- `continue`: 현재 반복 건너뛰기

### Chapter 5 - 배열
- 배열: 같은 타입 데이터의 연속 저장소
- 인덱스는 0부터 시작
- `length`: 배열 길이
- 향상된 for문: `for (타입 변수 : 배열)`
- `Arrays` 클래스: `toString()`, `sort()`, `copyOf()`
- 2차원 배열: `int[][] arr`

### Chapter 6 - 객체지향 I
- 클래스: 객체의 설계도 (필드 + 메서드)
- 객체: 클래스로부터 생성된 실체
- 생성자: 객체 생성 시 초기화 담당
- `this`: 현재 객체 참조
- 오버로딩: 같은 이름, 다른 매개변수의 메서드

---

## 추가 연습 문제

1. **가위바위보 게임**: 컴퓨터와 대결하는 프로그램 (배열로 승/패/무 기록)
2. **도서 관리 시스템**: Book 클래스 (제목, 저자, 가격, 재고)를 만들고 배열로 관리
3. **학생 성적 관리**: Student 클래스에 점수 배열을 포함하여 평균 계산
