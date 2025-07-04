## 초난감 예외처리

### 예외 블랙홀

자바를 배우는 학생들에게서 많이 찾아볼 수 있는 예외처리가 있다.

```
try {
 ...
}
catch(SQLException e) {

}
```

예외를 잡고는 아무것도 하지 않는 처리이다.

예외가 발생하더라도 인지(인지하지 못할 수도 있다.)하고는 아무 처리도 하지 않고 넘어가겠다는 의지가 보이는 코드라고 할 수 있다.

따라서 이런 일이 발생하지 않도록 하나의 원칙을 지키는 것이 바람직하다. 모든 예외가 적절히 복구되던지, 작업을 중단시키고 운영자 또는 개발자에 분명하게 통보되어야 한다.

### 무의미하고 무책임한 throws

에러가 나더라도 특정 에러에 대한 처리를 하는 것이 아니라 전역적으로 모든 예외를 던지도록 하는(Exception) 처리는 좋지 않다.

정말 에러나 날 수 있어서 던진 예외인지, 아니면 습관적으로 복사한 것인지도 알 수 없다.

```
public void method1() throws Exception {
	method2();
}
public void method2() throws Exception {
	method3();
}
public void method3() throws Exception {
	...
}
```

## 자바 예외의 종류와 특징

자바에서 프로그램이 돌아가다가 문제가 생겼을 때, 그걸 예외(Exception)라고 부른다. 이 예외는 크게 세 가지로 나눌 수 있다.

### 자바에서 throw를 통해 발생시킬 수 있는 예외

#### Error (에러)

**에러**는 java.lang.Error 클래스 아래 있는 것들을 말한다.

-   **특징:** 컴퓨터 시스템에 아주 심각한 문제가 생겼을 때 발생하며, 개발자가 코드로 이 에러를 잡아서 뭘 할 수 있는 게 거의 없다. 예를 들어, **OutOfMemoryError**(메모리 부족)나 **ThreadDeath**(스레드 종료) 같은 것들이 있다.
-   **처리 방법:** 에러는 자바 가상 머신(JVM)이 알아서 발생시키는 것이고, 보통 **개발자가 직접 처리하지 않는다.** 시스템 문제이니 개발자가 신경 쓸 필요는 없다.

#### Exception과 체크 예외 (Checked Exception)

**체크 예외**는 java.lang.Exception 클래스를 상속받지만 RuntimeException은 상속받지 않은 예외들을 말한다.

-   **특징:** 개발자가 짠 프로그램에서 충분히 일어날 수 있는 '예외적인 상황'을 나타낸다. 예를 들어, 파일을 못 찾거나(FileNotFoundException) 인터넷 연결에 문제가 생겼을 때 발생할 수 있다.
-   **처리 방법:** 체크 예외는 이름처럼 **반드시 명시적으로 처리해야 하는 예외**이다.
    -   해당 예외가 발생할 수 있는 코드를 쓸 때는 **try-catch 문으로 예외를 잡거나**,
    -   아니면 **throws 키워드를 써서 현재 메서드 밖으로 예외를 던져서** 다른 곳에서 처리하도록 해야 한다.
    -   만약 처리하지 않으면 **컴파일할 때 오류가 나서** 프로그램이 실행되지 않는다.

#### RuntimeException과 언체크/런타임 예외 (Unchecked/Runtime Exception)

**언체크 예외**는 java.lang.RuntimeException 클래스를 상속받는 모든 예외들을 말한다. '런타임 예외'라고도 부른다.

-   **특징:** 주로 **프로그램 자체에 버그나 논리적인 오류가 있을 때 발생**하도록 만들어진 예외들이다.
    -   대표적으로 **NullPointerException**(객체가 없는데 쓰려고 할 때)이나 **IllegalArgumentException**(허용되지 않는 값을 사용할 때) 같은 것들이 있다.
-   **처리 방법:** 언체크 예외는 체크 예외와 다르게 **명시적으로 처리하지 않아도 컴파일 오류가 나지 않는다.** 즉, try-catch로 잡거나 throws로 던지는 것을 강제하지 않는다는 뜻이다. 하지만 프로그램 오류이니 보통은 발생하지 않도록 코드를 고치는 것이 좋다. 물론 필요하다면 try-catch로 잡아서 처리할 수도 있다.

-   **Error:** 시스템 오류이니 개발자가 직접 처리하기 어렵다.
-   **체크 예외 (Checked Exception):** 프로그램의 '예외적인 상황'이니 **반드시 처리해야 컴파일된다.**
-   **언체크 예외 (Unchecked Exception) / 런타임 예외:** 프로그램의 '코드 오류'이니 **처리하지 않아도 컴파일되지만** 버그를 고쳐야 한다.

### 예외복구

자바에서 **예외**가 발생했을 때, 이를 어떻게 다룰지는 매우 중요하다. 예외를 처리하는 방법은 크게 세 가지로 나눌 수 있다.

#### 예외처리 회피

**예외 복구**는 문제가 발생했음에도 불구하고, 이를 해결하여 프로그램을 **정상 상태로 되돌리는** 방법이다. 다양한 복구 방법이 있지만, 가장 대표적인 것이 **재시도 로직**이다.

네트워크가 불안정하여 원격 데이터베이스 서버 접속에 실패(SQLException)하는 경우를 생각해보자. 이럴 땐 아래와 같이 재시도를 통해 문제를 극복할 수 있다.

```
int maxRetry = MAX_RETRY;
while (maxRetry-- > 0) {
    try {
        // 예외가 발생할 가능성이 있는 작업 시도
        // ...
        return; // 작업 성공 시 메서드 종료
    } catch (SomeException e) {
        // 로그 출력: 어떤 예외가 발생했는지 기록
        // 정해진 시간만큼 대기 (잠시 기다렸다가 다시 시도)
    } finally {
        // 리소스 반환 및 정리 작업 (성공/실패 여부와 관계없이 항상 실행)
    }
}
throw new RetryFailedException(); // 최대 재시도 횟수를 넘기면 직접 예외 발생
```

이처럼 예외 복구는 **문제를 해결하고 작업을 완료**하려는 명확한 의도를 가질 때 사용한다.

### 2\. 예외 처리 회피 (Avoidance)

**예외 처리 회피**는 현재 메서드에서 예외를 직접 처리하지 않고, **자신을 호출한 곳(상위 호출자)으로 예외를 던져버리는** 방법이다.

이 방법은 throws 문으로 예외를 선언하여 자동으로 던지게 하거나, catch 문으로 예외를 일단 잡은 다음 로그를 남기고 다시 throw 하는 방식으로 구현할 수 있다.

**주의할 점**은 예외를 회피하려면, **반드시 다른 객체나 메서드가 이 예외를 대신 처리할 수 있다는 명확한 의도와 확신이 있어야** 한다. 예를 들어, 콜백(Callback)이나 템플릿(Template)처럼 긴밀하게 연결된 구조에서 특정 객체가 예외 처리 책임을 지도록 할 때 유용하다. 이 방법은 예외 복구처럼 의도가 분명해야 하며, 무책임하게 예외를 던지는 것이 아님을 명심해야 한다.

### 3\. 예외 전환 (Conversion/Wrapping)

**예외 전환**은 예외를 복구할 수 없을 때, 회피와 비슷하게 예외를 메서드 밖으로 던지는 방법이다. 하지만 발생한 예외를 **그대로 던지는 것이 아니라, 다른 적절한 예외로 '전환'하여 던진다**는 점이 다르다.

예외 전환은 주로 두 가지 목적으로 사용된다.

1.  **의미를 명확하게 부여:** 내부에서 발생한 예외가 상황의 의미를 제대로 전달하지 못할 때, **더 적합한 의미를 가진 예외로 바꿔서 던진다.** 예를 들어, 기술적인 저수준(low-level) 예외(DB 접속 오류 등)가 발생했지만, 사용자에게는 '데이터 조회 실패'와 같은 비즈니스적 의미를 가진 예외로 전달하고 싶을 때 사용한다.
    -   이때, 전환하는 예외에 원래 발생한 예외를 중첩 예외(Nested Exception)로 담는 것이 좋다. getCause() 메서드를 통해 원본 예외를 확인할 수 있기 때문이다.
2.  **예외 처리 단순화 (포장):** 주로 **강제적인 체크 예외(Checked Exception)를 언체크 예외(Unchecked Exception), 즉 런타임 예외로 포장하여 던질 때** 사용한다. 비즈니스 로직 관점에서 복구 불가능하거나 큰 의미가 없는 체크 예외는 굳이 try-catch로 강제 처리하기보다 런타임 예외로 감싸서 처리하는 것이 코드를 간결하게 만든다.
    -   반대로, 애플리케이션 로직상 **비즈니스적인 의미가 있거나 복구 작업이 필요한 예외**는 **체크 예외를 사용하는 것이 적절하다.** 이는 API가 던지는 예외가 아니라, 개발자가 의도적으로 던지는 예외이므로 명확한 처리가 필요하기 때문이다.

## DB 에러 코드 매핑과 DAO 인터페이스 추상화

데이터베이스(DB) 종류가 바뀌어도 Data Access Object(DAO) 코드를 수정하지 않으려면, DB 관련 예외 처리의 일관성을 확보하는 것이 중요하다. 특히 DB마다 제각각인 에러 코드와 JDBC의 SQLException 문제를 해결해야 한다.

### DB 에러 코드 매핑으로 예외의 의미를 명확화

JDBC의 SQLException에 담긴 SQL 상태 코드보다는 각 DB 업체가 제공하는 **DB 전용 에러 코드**가 훨씬 정확한 정보를 담고 있다. MySQL은 '1062', Oracle은 '1', DB2는 '803'이라는 에러 코드를 통해 '키 중복' 오류를 알린다. 이렇게 정확한 에러 코드를 파악할 수 있다면, 단순히 SQLException을 던지는 대신 **DuplicateKeyException처럼 의미가 분명한 예외로 전환**하여 던질 수 있다. 이렇게 되면 DB 종류와 상관없이 언제나 같은 의미의 예외를 받을 수 있어, 개발자가 더 효과적으로 대응할 수 있게 된다.

스프링은 이러한 문제를 해결하기 위해 영리한 방법을 사용한다. SQLException을 대체하는 **DataAccessException이라는 런타임 예외**를 정의하고, 그 아래에 수십 가지의 세분화된 예외 클래스를 만들어둔다.(SQL 문법 오류는 BadSqlGrammarException으로, 키 중복 오류는 DuplicateKeyException으로 표현.)

스프링은 개발자가 직접 DB별 에러 코드를 확인할 필요 없이, **각 DB의 에러 코드와 스프링이 정의한 예외 클래스를 미리 매핑해 둔 정보**를 활용한다. JdbcTemplate이 바로 이 기능을 담당한다. JdbcTemplate은 단순히 SQLException을 DataAccessException으로 포장하는 것을 넘어, DB 에러 코드를 분석하여 해당하는 스프링의 특정 DataAccessException 하위 클래스로 변환해 던진다. 덕분에 DB가 바뀌어도 같은 오류는 항상 같은 스프링 예외로 받게 되어 DAO 코드를 훨씬 간결하게 유지할 수 있다.

### DAO 인터페이스 추상화와 기술 독립적인 예외

DataAccessException은 단순히 JDBC의 예외를 처리하기 위해 만들어진 것이 아니다. JDBC 외에 JDO, JPA, Hibernate 등 **다양한 자바 데이터 액세스 기술에서 발생하는 예외에도 동일하게 적용**될 수 있도록 설계한다. 이는 스프링이 **데이터 액세스 기술의 종류와 관계없이 의미가 같은 예외라면 일관된 예외가 발생하도록** 추상화된 예외 계층을 제공한다는 뜻이다.

DAO를 인터페이스로 분리하고 의존성 주입(DI)을 통해 구현체를 숨기는 것은 좋은 설계 방식이다. 하지만 메서드 선언에 나타나는 예외 정보는 종종 걸림돌이 된다. JDBC 기반 DAO는 SQLException을 던지고, JPA는 PersistenceException을 던지는 등 기술마다 다른 예외를 던지기 때문에, DAO 인터페이스마저 특정 기술에 종속될 위험이 있었다.

이 문제를 해결하기 위해 스프링은 DataAccessException을 활용한다. JDO나 Hibernate, JPA 같은 최근 기술들은 대부분 체크 예외 대신 런타임 예외를 사용하므로, 별도의 throws 선언이 필요 없다. JDBC를 사용하는 DAO 또한 모든 SQLException을 스프링의 **DataAccessException과 같은 런타임 예외로 포장하여 던지면**, DAO 인터페이스의 메서드 선언부에 더 이상 예외를 명시할 필요가 없게 된다. 이렇게 되면 DAO는 어떤 데이터 액세스 기술을 사용하든 완벽하게 기술에 독립적인 인터페이스를 가질 수 있게 된다.

대부분의 데이터 액세스 예외는 애플리케이션에서 복구할 필요가 없거나 복구 불가능하다. 하지만 키 중복 오류처럼 비즈니스 로직에서 특별히 처리해야 하는 예외도 존재한다. 스프링의 DataAccessException 계층 구조는 이러한 예외들을 일관된 런타임 예외로 전환해줌으로써, 클라이언트 코드가 DAO의 내부 구현 기술에 얽매이지 않고도 필요한 예외를 효과적으로 처리할 수 있도록 돕는다. 이는 현대 자바 엔터프라이즈 환경에서 매우 중요한 부분이다.

## 정리

### 예외 처리의 기본 원칙

-   **예외 무시는 위험하다:** 단순히 예외를 잡아서 아무런 조치도 취하지 않거나, 의미 없는 throws 선언을 남발하는 행위는 애플리케이션의 안정성을 해칠 수 있다.
-   **세 가지 예외 처리 전략:** 예외가 발생하면 다음 세 가지 방법 중 하나를 명확히 선택해 처리해야 한다.
    -   **복구:** 문제를 해결하여 정상 상태로 되돌린다.
    -   **의도적 전달 (회피):** 예외 처리 책임을 자신을 호출한 상위 계층으로 넘긴다.
    -   **전환:** 발생한 예외를 다른 적절한 예외로 변경하여 던진다.

### 예외 전환의 중요성

-   **두 가지 목적의 전환:** 예외 전환은 주로 두 가지 목적으로 사용된다.
    -   **의미 부여:** 발생한 기술적인 예외에 비즈니스적 의미를 부여하는 예외로 변경한다.
    -   **강제 처리 회피:** 불필요한 catch/throws를 피하기 위해 체크 예외를 런타임 예외(언체크 예외)로 포장한다.
-   **런타임 예외로의 빠른 전환:** 복구할 수 없는 예외라면 가능한 한 빨리 런타임 예외로 전환하는 것이 코드의 간결성과 유지보수성을 높이는 데 유리하다.
-   **애플리케이션 로직 예외는 체크 예외로:** 비즈니스 로직과 관련된, 의미 있는 예외나 복구가 필요한 예외는 체크 예외로 만드는 것이 적절하다.

### JDBC 예외와 Spring의 해결책

-   **JDBC 예외의 한계:** JDBC의 SQLException은 대부분 복구할 수 없는 예외이므로, 런타임 예외로 포장하는 것이 바람직하다. 또한, SQLException의 에러 코드는 특정 DB에 종속되기 때문에 DB에 독립적인 예외로 전환될 필요가 있다.
-   **Spring의 DataAccessException:** 스프링은 이러한 JDBC 예외의 단점을 극복하기 위해 **DataAccessException이라는 추상화된 런타임 예외 계층**을 제공한다. 이는 DB 종류와 관계없이 적용 가능하며, 일관된 예외 처리 경험을 제공한다.
-   **DAO의 기술 독립성 확보:** DAO(Data Access Object)를 특정 데이터 액세스 기술로부터 독립시키려면 다음 세 가지가 필요하다.
    1.  **인터페이스 도입:** DAO 구현체를 인터페이스 뒤에 숨긴다.
    2.  **런타임 예외 전환:** 내부적으로 발생하는 체크 예외를 런타임 예외로 포장한다.
    3.  **추상화된 예외로 전환:** 스프링의 DataAccessException 계층처럼 기술에 독립적인 추상화된 예외를 사용한다.
