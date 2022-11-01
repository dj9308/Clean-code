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
     - isPayday(Employee e, Date date)가 여럿 존재할 수 있다.

## 정리 및 느낀점

---

- 함수를 나누는 것은 목적을 하나만 두고 코드를 짜는 것.
- declarations, initializations, sieve 별로 함수로 나눌 수 있다.