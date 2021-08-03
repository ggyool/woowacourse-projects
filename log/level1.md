# 학습로그

# 로또 미션
- [LOG](https://github.com/woowacourse/java-lotto/pull/313#issuecomment-786038100)

<br>

# [JUnit] assertAll 사용 - 1
## 내용
- 하나의 테스트에서 여러 개의 assert문을 사용하는 경우, 처음 assert 문을 통과하지 못하면 테스트가 종료된다.
- assertAll()을 이용하면 앞선 assert 문을 통과하지 못해도 뒤의 assert 문도 실행된다.
## 링크
https://github.com/ggyool/java-lotto/blob/step2/src/test/java/domain/LottoMoneyTest.java

# [JCF] Linked Hash Map 사용 - 1
## 내용
- 당첨 통계에서 등수별 카운을 할 때 1등부터 차례로 저장하고 싶었다.
- HashMap을 사용하기엔 순서가 보장된다는 생각이 들지 않았다.
- 새로운 자료구조를 알게 되어 사용!
## 링크
https://github.com/ggyool/java-lotto/blob/step2/src/main/java/domain/WinningStatics.java

# [stream] Map을 stream으로 사용 - 1
## 내용
- 배열과 리스트가 아닌 자료 구조를 처음으로 스트림을 이용해봤다.
- map.entrySet().stream()
## 링크
https://github.com/ggyool/java-lotto/blob/step2/src/main/java/dto/WinningStaticsDto.java

# [JDK] Custom Exception 사용 - 1
## 내용
- RuntimeException을 상속받아 커스텀 예외를 구현해 보았음
## 링크
https://github.com/ggyool/java-lotto/tree/step2/src/main/java/exception

# [JCF] Map 에서 람다를 사용하는 API - 2
## 내용
- computeIfAbsent, computeIfPresent 사용
## 링크
https://github.com/ggyool/java-lotto/blob/step1/src/main/java/domain/LottoNumber.java
https://github.com/ggyool/java-lotto/blob/step2/src/main/java/domain/WinningStatics.java
http://tech.javacafe.io/2018/12/03/HashMap/

# [MVC] DTO 사용 - 2
## 내용
- 지난 미션에서 DTO를 처음 사용했었고, 이번 미션에서 조금 더 잘 사용한 것 같다.
- DTO에 정적 팩토리 메서드 만들기
- 도메인 안에 DTO 넣지 않기
## 링크
https://github.com/ggyool/java-lotto/tree/step2/src/main/java/dto

# [OOP] 추상 클래스와 상속 활용 - 2
## 내용
- Ticket이라는 추상 클래스를 만들고 이를 상속하여 LottoTicket과 WinningTicket을 구현하였음.
- 로또 프로그램에 상속 구조가 필요하다고 생각지는 않지만 공부할 겸 만들어 보았음
- 현재 검증과 공통 로직은 Ticket이 가지고 있고, LottoTicket과 WinningTicket은 자신에게 맞는 로직을 가지고 있음
## 링크
https://github.com/ggyool/java-lotto/tree/step1/src/main/java/domain/ticket

# [JDK] enum, 함수형 인터페이스 사용 - 2
## 내용
- 등수를 표현하는 Prize 라는 클래스를 enum으로 만들었다.
- int matching, boolean bonusMatching으로 등수를 계산하는데 다양한 방법을 사용해봤다.
1. values(), stream(), filter() 사용
2. 추상 메서드를 사용하여 정의 시 isMatched 메서드 구현
3. 2번에서 발전, 다형성을 이용하여 로직이 다른 2등, 3등만 override 하여 구현
4. 함수형 인터페이스 이용하여 필드로 biPredicate<Integer, Boolean>을 가지고 있게함
## 링크
https://github.com/ggyool/java-lotto/blob/step2/src/main/java/domain/Prize.java

# [caching] 캐싱 구현 - 3
## 내용
- LottoNumber 클래스에서 1~45를 가지는 LottoNumber를 계속 생성할 필요가 없으므로 캐싱
- LottoTicket 클래스에서 1~45의 Integer 숫자를 shuffle 하는데 계속 생성할 필요가 없으므로 캐싱
- Integer.java 를 참고하였음
- 캐시를 가지고 있는 static inner class는 현재 프로그램에 과하다고 생각이 들지만 공부할 겸 만들어 보았음
## 링크
https://github.com/ggyool/java-lotto/blob/step2/src/main/java/domain/LottoNumber.java
https://github.com/ggyool/java-lotto/blob/step2/src/main/java/domain/ticket/LottoTicket.java

<br><br>

# 블랙잭 미션
- [LOG1](https://github.com/woowacourse/java-blackjack/pull/128#issuecomment-792660878)
- [LOG2](https://github.com/woowacourse/java-blackjack/pull/215#issuecomment-798893741)

# [AssertJ] isTrue, isFalse 사용 - 1
## 내용
- isEqualTo()만 사용했었는데 isTrue를 사용하니 가독성이 좋아졌다.
- AssertJ에서 제공하는 다른 메서드들도 많으니 하루 날잡고 정리해야 겠다는 생각이 든다.
## 링크
https://joel-costigliola.github.io/assertj/assertj-core-features-highlight.html#exception-assertion

<br>

# [JCF] Collections.frequency() 사용 - 1
- map에서 특정 value의 수를 세고 싶었는데 해당 메서드를 발견해서 유용하게 사용했다.
## 링크
https://github.com/ggyool/java-blackjack/blob/431ad56dc2f062c83900ff4e9bf1e97110552005/src/main/java/blakcjack/domain/blackjackgame/BlackjackGame.java#L79

<br>

# [JDK] Custom Exception 사용 - 3
## 내용
- RuntimeException을 상속받아 커스텀 예외를 구현
- 또한 예외를 catch 하여 원하는 동작을 구현하였음
## 링크
https://github.com/ggyool/java-blackjack/blob/431ad56dc2f062c83900ff4e9bf1e97110552005/src/main/java/blakcjack/controller/BlackJackController.java

<br>

# [MVC] View의 역할에 대한 고민 - 3
- 카드를 더 뽑을지 플레이어에게 응답받는 부분을 도메인까지 넘기지 말고 View에서 처리해도 되겠다는 생각이 들었다.
- 장점
  만들고 나니 코드도 깔끔해지고 validate를 view에서 하므로, 이상한 입력이 들어온 경우 controller로 전달되지 않으므로 성능상 이득이 있다는 생각이 들었다.
- 단점
  View에서만 검증이 되고 있기 때문에 View의 코드가 잘못됐다면 치명적인 상황이 생길 수도 있다, (카드를 뽑으면 안 되는데 true를 보내준다던가)
  또한 새로운 응답이 필요하여 추가된다면 controller에서 boolean을 받는 부분을 수정해야 한다.
- 결론
  Domain에도 ReplyType 객체로 관리하여 View에서 검증한 값을 문자열로 전달하고 도메인에서 한 번 더 검증한다면 베스트일 거라는 나름의 결론을 내렸다.
## 링크
https://github.com/ggyool/java-blackjack/blob/431ad56dc2f062c83900ff4e9bf1e97110552005/src/main/java/blakcjack/View/InputView.java
https://github.com/ggyool/java-blackjack/blob/431ad56dc2f062c83900ff4e9bf1e97110552005/src/main/java/blakcjack/View/reply/AdditionalCardReply.java

# [JDK] EnumMap 사용, 캐싱 - 4
## 내용
Card와 Score에 캐시를 적용하였고, 특히 Card 캐시에 어떤 자료구조를 사용할 것인지에 대해 고민하였다.
- 1단계
- List를 사용하면 코드 자체는 깔끔할 것이라는 생각을 했으나
- 캐시를 탐색하는데 O(카드수)의 시간이 소모되므로 사용하기 싫었다. (카드수가 52장 밖에 안되잖아!! 라고 말하면 할 말이 없다.)
- CardSymbol과 CardNumber를 가지고 String 타입의 custom key를 만들어 HashMap<String, Card> 구조로 캐시를 만들었다.

- 2단계
- 내가 만든 문자열을 key로 사용하는 게 마음에 걸렸다.
- 어떤 크루에게 아이디어를 얻어 후보 2개를 생각했는데
1. Objects.hash() 메서드를 사용하여 key를 만들어내기
2. Symbol과 Number의 ordinal()을 사용하여 2차원 Array 캐시 만들기
   2번 구현이 매력적으로 다가왔었는데..
   이펙티브 자바에 ordinal 사용에 대한 단점에 관한 내용이 있음을 알게 되었고, EnumMap을 사용하기로 했다.
- 현재 코드는 EnumMap안에 EnmMap을 넣는 복잡한 구조의 캐시가 되었다.
  ```EnumMap<CardSymbol, EnumMap<CardNumber, Card>>```
  현재 미션에서는 과하다는 생각도 들지만 성능 면이나 안정성 면에서 좋은 코드라는 생각이 들었다.
  캐시 관련 코드가 많아졌지만 최대한 가독성 좋게 만들려고 노력하였다.

## 링크
https://github.com/ggyool/java-blackjack/blob/02c7f58e3ec000bc861f60fe9add659c1f0e3123/src/main/java/blakcjack/domain/card/Card.java
https://github.com/ggyool/java-blackjack/blob/02c7f58e3ec000bc861f60fe9add659c1f0e3123/src/main/java/blakcjack/domain/score/Score.java

<br>

# [JDK] Optional 사용 - 2
## 내용
- of(), ofNullable() 차이점 알기
- orElse(), orElseGet(), orElseThrow() 차이점 알기

## 링크
https://github.com/ggyool/java-blackjack/blob/02c7f58e3ec000bc861f60fe9add659c1f0e3123/src/main/java/blakcjack/domain/card/Card.java
http://www.tcpschool.com/java/java_stream_optional

<br>

# [JDK] 일급 컬렉션 사용 - 1
## 내용
- Cards 일급 컬랙션을 만들어 Participant가 가지고 있던 점수 계산하는 책임을 옮겨 줬다.
- 불변일 필요는 없다고 판단하여 add 메서드를 정의하였다.

## 링크
https://github.com/ggyool/java-blackjack/blob/02c7f58e3ec000bc861f60fe9add659c1f0e3123/src/main/java/blakcjack/domain/card/Card.java
https://woowacourse.github.io/javable/post/2020-05-08-First-Class-Collection/

<br>

# [JDK] 함수형 인터페이스 사용 - 2
## 내용
- 최근 람다와 함수형 인터페이스에 대해 공부했었다.
- Outcome(승무패)와 블랙잭 여부를 가지고 EarningRate(수익률)을 구하는 과정에서 Predicate를 사용하였다.

## 링크
https://github.com/ggyool/java-blackjack/blob/02c7f58e3ec000bc861f60fe9add659c1f0e3123/src/main/java/blakcjack/domain/money/EarningRate.java

<br>

# [DTO] DTO 사용 - 2
## 내용
- View에게 Domain을 노출하기 싫어 여러 DTO를 만들어 사용하였다.
- 이번 미션에서 정한 규칙
- DTO의 필드는 도메인 클래스를 넣지 않는다. int, String, List, Map 등 어느 언어에서나 존재할 만한 타입을 필드로 가진다. (예외로 Card는 DTO를 만들지 않았다.)
- DTO의 생성자는 private로 하고 정적 팩토리 메서드를 정의한다.

## 링크
https://github.com/ggyool/java-blackjack/tree/step2/src/main/java/blakcjack/dto

<br><br>

# 체스 미션
- [LOG1](https://github.com/woowacourse/java-chess/pull/205#issuecomment-805057093)
- [LOG2](https://github.com/woowacourse/java-chess/pull/266#issuecomment-824446761)

# [OOP] 인터페이스 사용 - 3
## 내용
- 상태 패턴이라는 것을 저번 미션에서 접했지만 적용하지는 못했었다.
  이번 미션에서는 게임 진행에 상태 패턴을 사용해보고 싶어서  State 인터페이스를 만들고 다형성을 이용했다.
  구현하며 시행착오를 겪었고, 일단 손 가는 데로 만들었지만 좋은 구조인지 잘 모르겠고 상태 패턴인지도 모르겠다.
- 현재 코드에서 정상적인 게임 흐름이라면 아래와 같이 상태가 변화한다.
  Start -> Wait -> Move -> Wait -> Move -> Wait -> Status -> Wait -> Move ... -> End

## 링크
https://github.com/ggyool/java-chess/tree/step1/src/main/java/chess/domain/state
https://github.com/ggyool/java-chess/blob/e9d85489cf2dd6e55d358d99524f8372011222dd/src/main/java/chess/controller/ChessController.java

# [JDK] 제네릭 사용 - 2
## 내용
- Start, Move 상태에서는 OutputView에서 체스판을 출력하고 Status 상태에서는 점수를 출력한다.
  View의 메서드를 호출하는 객체는 컨트롤러여야 한다고 생각했고, 각각의 State마다 실행 결과를 controller로 반환해 준다.
  체스판의 정보는 ```List<Piece> result()``` 스코어의 정보는 ```Map<Team, Double> result()``` 이기 때문에,
  ```T result()``` 이런 형태로 인터페이스에 추상 메서드를 두었다.
  만들다 보니 컨트롤러에서 형변환도 필요했고 좋은 구조인지 의문이 들었다. 이번에는 사용해본 것에 의의를 두고 싶다.

## 링크
https://github.com/ggyool/java-chess/blob/e9d85489cf2dd6e55d358d99524f8372011222dd/src/main/java/chess/domain/state/State.java
https://github.com/ggyool/java-chess/blob/e9d85489cf2dd6e55d358d99524f8372011222dd/src/main/java/chess/domain/state/Move.java
https://github.com/ggyool/java-chess/blob/e9d85489cf2dd6e55d358d99524f8372011222dd/src/main/java/chess/domain/state/Status.java
https://github.com/ggyool/java-chess/blob/e9d85489cf2dd6e55d358d99524f8372011222dd/src/main/java/chess/controller/ChessController.java

<br>

# [JDBC] JDBC API 사용 - 3
## 내용
- 데이터베이스 접속 (Connection)
- SQL 문장의 실행 (PreparedStatement)
- 실행결과 (ResultSet)
## 링크
https://github.com/ggyool/java-chess/tree/step2/src/main/java/chess/dao

# [DTO] DTO의 사용 계층 - 5
## 내용
- DTO와 도메인의 변환을 어느 계층에서 할지 고민하였다.
- 우리가 일반적으로 사용하는 DTO는 클라이언트와 서버간 전송하는 데이터를 담는 객체라고 생각했고, 우리가 만드는 앱의 API에 의존적 이라고 생각한다.
- 그러한 이유로 Web 요청과 응답을 담당하는 Controller에서 도메인 변환을 하는게 가장 적절하다는 생각이 들었고, 그렇게 만든다면 하나의 Service를 여러 Controller가 사용할 수 있다는 장점이 있어 보였다.
- 하지만 Controller에서 도메인 변환이 이루어지면 도메인의 존재를 몰라도 되는 Controller도 도메인의 존재를 알게 되는 불안한 점이 존재한다. 도메인을 노출하기 싫다면 DTO의 필드들을 하나하나 풀어서 Service 메서드로 전달하면 되겠지만 인자의 수가 많아지면 컬렉션에 넣어서 전달한다든지 불편한 방법을 사용해야 할 것 같다.
- 이번 미션에서는 위의 이유에 무게를 두어 Service 레이어에서 DTO와 도메인을 변환하였다.
