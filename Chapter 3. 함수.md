## 개요

---

- 어떤 프로그래밍이든 가장 기본적인 단위가 함수다.
- 길이가 길 뿐 아니라 중복된 코드에, 괴상한 문자열에, 낮설고 모호한 자료 유형이 많을 경우 해당 함수를 이해하기 어려워진다.

## 작게 만들어라

---

- 함수를 만드는 규칙은 작게 만드는 것이다.

- 함수를 작게 분리하면 해당 함수가 무슨 일을 하는지 더 명확히 알수 있으며(Naming), 구조를 더 명확하게 이해할 수 있다.

- 아래의 코드는 페이지 랜더링 하는 함수를 리펙토링한 코드이다.

  ```java
  public static String renderPageWithSetupAndTeardowns(
  	PageData pageData, boolean isSuite) throws Exception{
      if(isTestPage(pageData))
          includeSetupAndTeardownPages(pageData, isSuite);
      return pageData.getHtml();
  }
  ```

### 블록과 들여쓰기

- if else문 또는 while문 등에 들어가는 블록은 한 줄이어야 한다는 의미다. 대개 거기서 함수를 호출한다.
- 그러면 바깥을 감싸는 함수가 작아질 뿐 아니라, 블록 안에서 호출하는 함수 이름을 적절히 짓는다면, 코드를 이해하기도 쉬워진다.
- 즉, 중첩 구조가 생길만큼 함수가 커져서는 안 된다는 뜻이다.

## 한 가지만 해라

---

- 함수는 한 가지를 해야한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야 한다.
- 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한 가지 작업만 한다.
- 단순히 다른 표현이 아니라 의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 셈이다.
- 함수 부분중 선언 부분, 초기화 부분 또한 함수 개념으로 나뉠 수 있다.

## 함수 당 추상화 수준은 하나로

---

- 함수가 확실히 한 가지 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.
- 한 함수 내에 추상화 수준을 섞으면 코드를 읽는 사람이 헷깔린다. 특정 표현이 근본 개념인지 아니면 세부사항인지 구분하기 어려운 탓이다.
  - getHtml() 함수는 추상화 수준이 아주 높다.
  - String pagePathName = PathParser.render(pagepath)는 추상화 수준이 중간이다.
  - .append("\n")와 같은 코드는 추상화 수준이 아주 낮다.
- 근본 개념과 세부사항을 뒤섞기 시작하면 깨어진 창문처럼 사람들이 함수에 세부사항을 점점 더 추가한다.

### 내려가기 규칙

- 코드는 위에서 아래로 이야기처럼 읽혀야 좋다.
- 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 단계씩 낮아진다.
- 다르게 표현하면, 일련의 TO 문단을 읽듯이 프로그램이 읽혀야 한다는 의미다.
  - EX) : TO 설정페이지와 해제 페이지를 포함하려면, 설정 페이지를 표함하고, 테스트 페이지 내용을 포함하고, 해체 페이지를 포함한다.
    - TO 설정 페이지를 포함하려면, 슈트이면 슈트 설정 페이지를 포함한 후 일반 설정 페이지를 포함한다.
    - TO 슈트 설정페이지를 포함하려면, 부모 계층에서 "SuiteSteUp"페이지를 찾아 include문과 페이지 경로를 추가한다.
- TO 문단을 읽어내려 가듯이 코드를 구현하면 추상화 수준을 일관되게 유지하기가 쉬워진다.

## Switch 문

---

- Switch 문은 작게 만들기 어렵다.(if/else 여러 구문도 포함)

- 본질적으로 switch 문은 N가지를 처리한다. switch 문을 다형성(polymophism)을 이용해 저차원 클래스에 숨기고 절대로 반복하지 않는 방법은 있다.

- ```java
  public Money calculatePay(Employee e) throws InvalidEmployeeType{
      switch (e.type) {
          case COMMISSIONED :
              return calculateCommissionedPay(e);
          case HOURLY :
              return calculateHourlyPay(e);
          case SALARIED : 
              return calculateSalariedPay(e);
          default :
              throw new InvalidEmployeeType(e.type);
      }
  }
  ```

  위 함수는 다음의 문제가 있다.

  1. 함수가 길며, 경우가 늘어나면 더 길어질 수 있다.
  2. '한 가지' 작업만 수행하지 않는다.
  3. SRP(Single Responsibility Principle, 단일 책임 원칙)을 위반한다. 코드를 변경할 이유가 여럿이기 때문이다.
  4. OCP(Open Closed Principle, 개방 폐쇄 원칙)을 위반한다. 세 직원 유형을 추가할 때마다 코드를 변경하기 때문이다.
  5. **위 함수와 구조가 동일한 함수가 무한정 존재할 수 있다**
     - isPayday(Employee e, Date date) 또는 
       deliverPay(Employee e, Money pay); 이 여러개일 수 있다.

## 서술적인 이름을 사용하라.

---

- testableHtml을 SetupTearDownIncluder로 변경하는 것이 의미 표현에 있어 더 좋다.
- 함수는 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다.
- 길고 서술적인 이름이 짧고 어려운 이름보다, 또한 주석보다 더 좋다.
- 함수 이름을 정할 때, 여러 단어가 쉽게 읽히는 명명법을 사용한다. 그 다음, 여러 단어를 사용해 함수 기능을 잘 표현하는 이름을 선택한다.
- EX) : includeSetupAndTeardownPages, includeSetupPages 등

## 함수 인수

---

- 함수에서 인수의 개수는 적을수록 좋다. 삼항부터 피하는 편이 좋다.
- StringBuffer 같은 인수는 인수보다 인스턴스 변수로 선언하는 것이 바랍직하다.
- 출력 인수는 입력 인수보다 이해가 어렵다. 기본적으로 함수에 인수로 입력을 넘기고 반환값으로 출력을 받는다는 개념에 익숙하기 때문이다.
- 최선은 입력 인수가 없는 경우이며, 차선은 입력 인수가 1개뿐인 경우다.

### 단항 함수

- 인수 1개를 넘기는 이유는 주로 2가지이다.
  1. 인수에 질문을 던지는 경우
     ex): boolean fileExists("MyFile");
  2. 인수를 어떤 것으로 변환해 결과를 반환하는 경우.
     ex): InputStream fileOpen("MyFile");
- 또한 이벤트가 단항 함수 형식으로 쓰인다.
  - 이벤트 함수는 입력 인수만 있으며, 출력 인수는 없다.
  - ex): passwordAttemptFailedNtimes(int attempts);
- 변환 함수에서 출력 인수를 사용하면 혼란을 일으킨다.
  - StringBuffer transform(StringBuffer in)이 void transform(StringBuffer out)보다 좋다.
  - 입력 인수를 그대로 돌려주는 함수라 할지라도 변환 함수 형식을 따르는 것이 좋다. 변환 형태는 유지하기 때문이다.

### 플래그 형식

- 플래그 인수는 bool 값을 넘기는 관례로서, 쓰지 않아야 한다.
- 함수가 한꺼번에 여러가지를 처리한다고 선언하는 것이기 때문이다.
- ex): render(boolean isSuite)라는 함수는 renderForSuite()와 renderForSingleTest()라는 함수로 나눠야 한다. 

### 이항 함수

- 인수가 2개인 함수는 1개인 함수보다 이해하기 어렵다.
- 좌표처럼 이항이 두개가 기본인 경우도 있다.
- 하지만 대부분 인수 간 순서가 없기 때문에, 하나로 변환하도록 해야 한다.
- 함수를 하나 더 생성하거나 인수 하나를 인스턴스 변수로 변환한다

### 인수 객체

- 인수가 2~3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 수 있다.
  - ex): Circle makeCircle(double x, double y, double radius);
  - Circle makeCircle(Point center, double radius);
- 변수를 묶어 넘기려면 이름을 붙여야 하므로 결국은 개념을 포현하게 된다.

## 정리 및 느낀점

---

- 함수를 나누는 것은 목적을 하나만 두고 코드를 짜는 것.
- declarations, initializations, sieve 별로 함수로 나눌 수 있다.
- boolean 형식을 인수로 쓸바엔, 함수를 두개로 나눠라.
- 인수를 줄이려면 객체를 더 생성하거나 인스턴스 변수를 생성한다.