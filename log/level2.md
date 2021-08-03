# 체스 미션
- [LOG1](https://github.com/woowacourse/jwp-chess/pull/266#issuecomment-825085890)
- [LOG2](https://github.com/woowacourse/jwp-chess/pull/323#issuecomment-864888216)
  <br>

## GET과 POST요청 성공과 실패에 따른 응답코드
- GET의 경우 성공 시 200 (ok) 상태 코드와 리소스의 표현을 반환
- POST의 경우 성공 시 201 (created) 상태 코드와 Location 헤더에 리소스를 포함
- 성공한 경우는 별다른 고민 없이 떠올릴 수 있었다.
- 하지만 요청에 실패한 경우 어떤 응답 코드와 body를 반환해야 할지 고민이 됐다.
- 페어와 의논한 결과 400 (bad request) 상태 코드를 반환하기로 하였다.
- https://docs.microsoft.com/ko-kr/azure/architecture/best-practices/api-design
- 그러던 중 위의 링크를 보게 되었고, GET 요청에 실패했을 경우 자원을 찾을 수 없다는 의미로 404 (not found)를 반환하는 것은 상당히 타당해 보인다.
- 그렇지만 POST 요청에 실패하는 경우 여러 이유가 있을 수 있기 떄문에
- 클라이언트에서 보낸 데이터의 문제였다면 400 (bad request)를 반환하는 게 좋아보이고
- 서버에서의 문제라면 50x에러를 반환하는게 좋아보인다.

<br>

## DTO의 사용 계층
- DTO와 도메인의 변환을 어느 계층에서 할지 고민하였다.
- 우리가 일반적으로 사용하는 DTO는 클라이언트와 서버간 전송하는 데이터를 담는 객체라고 생각했고, 우리가 만드는 앱의 API에 의존적 이라고 생각한다.
- 그러한 이유로 Web 요청과 응답을 담당하는 Controller에서 도메인 변환을 하는게 가장 적절하다는 생각이 들었고, 그렇게 만든다면 하나의 Service를 여러 Controller가 사용할 수 있다는 장점이 있어 보였다.
- 하지만 Controller에서 도메인 변환이 이루어지면 도메인의 존재를 몰라도 되는 Controller도 도메인의 존재를 알게 되는 불안한 점이 존재한다. 도메인을 노출하기 싫다면 DTO의 필드들을 하나하나 풀어서 Service 메서드로 전달하면 되겠지만 인자의 수가 많아지면 컬렉션에 넣어서 전달한다든지 불편한 방법을 사용해야 할 것 같다.
- 이번 미션에서는 위의 이유에 무게를 두어 Service 레이어에서 DTO와 도메인을 변환하였다.

<br>

## DTO에서 Enum 사용하기
- 웹 요청을 DTO에 매핑할 때 String 필드를 사용하고 Domain에서는 EnumType 객체를 사용하는 경우가 많다.
  <br>
  예시) 체스의 기물을 나타내는 Piece라는 모델

```java
class PieceDto {
    String team = “white”;
    …
}

class Piece {
    Team team = Team.WHITE;
    …
}
```
- 이런 경우 보통 PieceDto -> Piece로 변환하는 코드를 실행할 때 Team.from(“white”) 같은 메서드를 실행하여 문자열 값을 EnumType으로 변환하여 사용한다.
- 위에서 이야기한 구조 대신 **DTO에서 Enum 필드를 직접 사용**하게 만들 수 있는데, 몇 가지 설정을 추가하면 가능하다. 그렇게 하면 웹 요청에서 받은 String을 Enum으로 변환하는 작업을 명시적으로 수행하지 않아도 자동으로 수행된다.
- Enum을 여러 Domain에서 사용하는 경우를 가정하면 Enum으로 변환하는 중복 코드가 생기게 된다. DTO에서 Enum을 직접 가지게 하면 이런 중복을 없앨 수 있다.
```java
class PieceDto {
    Team team = Team.WHITE;
    …
}
```
- Enum은 보통 도메인을 취급하는 계층에서 사용되고 일종의 객체이기 때문에 DTO에서 도메인 또는 객체를 직접 가지고 있다고 생각하여 거부감을 느낄 수 있다.
- 하지만 나의 경우는 Enum 객체는 조금 특별하게 생각하여 여러 계층에서 사용해도 된다는 생각을 가지고 있다. Enum 객체는 일종의 싱글턴 객체이므로 변하는 필드를 가지면 안 되고, 그런 이유로 일반적인 객체와 다르게 생각한다. Enum은 타입을 표현하는 용도로만 사용하고 만약 로직이 필요하다면 Enum 필드를 포함하는 또 다른 클래스를 만들고 그곳에 로직을 작성하는 편이다.

**- 요청 DTO 에서 사용 (String -> Enum)**
1. 요청 DTO에서 Enum 사용 [(MoveResponseDto.java)](https://github.com/ggyool/jwp-chess/blob/step2/src/main/java/chess/web/dto/game/move/MoveRequestDto.java)
2. Custom Converter 생성 [(StringToTeamConverter.java)](https://github.com/ggyool/jwp-chess/blob/step2/src/main/java/chess/utils/StringToTeamConverter.java)
3. WebConfiguration 클래스에 추가 [(WebConfig.java)](https://github.com/ggyool/jwp-chess/blob/step2/src/main/java/chess/config/WebConfig.java#L22)
4. Controller 에서 사용시 자동 변환 [(GameApiController.java)](https://github.com/ggyool/jwp-chess/blob/step2/src/main/java/chess/web/apicontroller/GameApiController.java#L46)

**- 응답 DTO 에서 사용 (Enum -> String)**
1. 응답 DTO에서 Enum 사용 [(MoveResponseDto.java)](https://github.com/ggyool/jwp-chess/blob/step2/src/main/java/chess/web/dto/game/move/MoveResponseDto.java)
2. Controller 에서 사용시 자동 변환 [(GameApiController.java)](https://github.com/ggyool/jwp-chess/blob/step2/src/main/java/chess/web/apicontroller/GameApiController.java#L45)

참고 링크: https://www.baeldung.com/spring-enum-request-param
<br>
<br>

# 노선도 미션
- [LOG1](https://github.com/woowacourse/atdd-subway-map/pull/73#issuecomment-833660841)
- [LOG2](https://github.com/woowacourse/atdd-subway-map/pull/151#issuecomment-865454019)

## PUT에 성공한 경우 HTTP Status Code
- 요청에 성공한 경우 OK이라는 이름에 이끌려 200을 응답했다.
- 하지만 200 OK를 응답받는 클라이언트는 일반적으로 body를 기대하고 있다.
- 따라서 아래처럼 204 NO CONTENT로 응답하도록 수정했다.
- PUT 요청에 성공한 경우 무조건 204가 아니라 body의 유무가 중요하다.
- 아래 요청과 다르게 PUT요청 성공시 수정한 결과를 body에 전달해 주고 싶다면 200 OK로 응답하는게 좋겠다.
```java
@PutMapping("/{id}")
public ResponseEntity<Void> updateLine(@PathVariable Long id, @RequestBody LineRequest lineRequest) {
    lineService.updateLine(id, lineRequest);
    return ResponseEntity.noContent().build();
}
```
- https://developer.mozilla.org/ko/docs/Web/HTTP/Status
  <br>

## DataAccessUtils.singleResult
- DB에서 데이터 하나를 가져올 경우 queryForObject는 데이터가 없으면 Exception이 발생하므로 query를 사용하여 아래처럼 처리하였다.
```java
public Optional<Line> findById(final Long id) {
    final String sql = "SELECT * FROM line WHERE id = ?";
    final List<Line> lines = jdbcTemplate.query(sql, lineRowMapper, id);
    if (lines.isEmpty()) {
        return Optional.empty();
    }
    return Optional.ofNullable(lines.get(0));
}
```
- DataAccessUtils 를 알게 되어 사용하였다.
```java
public Optional<Line> findById(final Long id) {
    final String sql = "SELECT * FROM line WHERE id = ?";
    final List<Line> lines = jdbcTemplate.query(sql, lineRowMapper, id);
    return Optional.ofNullable(DataAccessUtils.singleResult(lines));
}
```
- DataAccessUtils.singleResult 내부
```java
@Nullable
public static <T> T singleResult(@Nullable Collection<T> results) throws IncorrectResultSizeDataAccessException {
    if (CollectionUtils.isEmpty(results)) {
        return null;
    }
    if (results.size() > 1) {
        throw new IncorrectResultSizeDataAccessException(1, results.size());
    }
    return results.iterator().next();
}
```
<br>

## 리스트를 테스트할 때 특정 필드만을 비교하는 테스트
```java
@DisplayName("전체 지하철역을 조회한다.")
@Test
void findAll() {
    final List<String> stationNames = Arrays.asList("잠실역", "강남역", "건대입구역");
    stationNames.stream()
        .map(Station::new)
        .forEach(stationDao::save);

    assertThat(stationDao.findAll()).extracting("name").isEqualTo(stationNames);
}
```
<br>

## 특정 필드만 제외하고 비교하는 테스트
```java
@DisplayName("구간을 생성한다.")
@Test
void save() {
    final Line createdLine = lineDao.save(new Line("2호선", "black"));
    final Section section = new Section(createdLine.getId(), 2L, 4L, 10);
    final Section createdSection = sectionDao.save(section);

    assertThat(createdSection)
        .usingRecursiveComparison()
        .ignoringFields("id")
        .isEqualTo(section);
}
```
- 테스트, assertj, usingRecursiveComparison, ignoringFields
  <br>

## NamedParameterJdbcTemplate 사용
- JdbcTemplate에서  쿼리문의 인자에 ‘?’ 를 사용하는데 NamedParameterJdbcTemplate를 사용하면 파라미터명을 사용하여 작성할 수 있다.
- **파라미터의 순서에 의존하지 않기 때문**에 유지보수성 측면에서 더 좋은 코드가 됨
- 추가로 **SqlParameterSource**를 인자로 받는 템플릿 메서드를 제공하기 때문에 그 구현체들을 사용할 수 있다.
- 가독성과 간결성 측면에서 더 좋은 코드가 된다.

```java
public void updateByJdbcTemplate(Line line) {
    String sql = "UPDATE line SET name = ?, color = ? WHERE id = ?";
    jdbcTemplate.update(sql, line.getName(), line.getColor(), line.getId());
}
```
``` java
public void updateByNamedParameterJdbcTemplate(Line line) {
    final String sql = "UPDATE line SET name = :name, color = :color WHERE id = :id";
    namedParameterJdbcTemplate.update(sql, new BeanPropertySqlParameterSource(line));
}
```
<br>

## @Valid 어노테이션을 이용하여 DTO에서 값 검증
- 스프링에서 제공하는 Validation 기능을 사용하면 어노테이션을 이용하여 쉽게 유효성 검증을 할 수 있다.
  이번 미션에서는 Controller에서 DTO에 값이 매핑될 때 검증 작업을 수행했지만, Service나 Bean 에서도 사용할 수 있다.
- 검증 실패 시 MethodArgumentNotValidException이 발생하므로 적절히 처리하자.

- DTO에서 유효성 검증하는 흐름
1. 요청 DTO에 검증 조건을 설정하는 어노테이션과 예외 메시지 추가 [(LineRequest.java)](https://github.com/ggyool/atdd-subway-map/blob/step2/src/main/java/wooteco/subway/line/LineRequest.java)
2. Contoller에 @Valid 어노테이션 추가 [(LineController.java)](https://github.com/ggyool/atdd-subway-map/blob/step2/src/main/java/wooteco/subway/line/LineController.java#L30)
3. 유효성 검증 실패 시 ControllerAdvice에서 처리 [(SpecificExceptionHandler.java)](https://github.com/ggyool/atdd-subway-map/blob/step2/src/main/java/wooteco/subway/SpecificExceptionHandler.java#L20)

- 참고 링크
  https://www.baeldung.com/javax-validation
  https://woowacourse.github.io/javable/post/2020-09-20-validation-in-spring-boot/
  https://meetup.toast.com/posts/223
  <br>

## 빌더 사용
- 이번 미션에서 Section 도메인의 경우 아래처럼 많은 필드를 가지고 있다.
```java
Section section = new Section(id, lineId, upStationId, downStationId, distance);
```
- Section 도메인은 Long Type의 ID가 4개나 되는데 이런 경우 순서에 의존하여 위와 같이 일반 생성자를 사용하면 실수하기 쉽다.
  또한 경우에 따라 선택적인 필드만으로 객체를 생성해야 하는 경우가 있는데 이러한 경우 생성자의 수가 많아진다.
- 미션을 진행하며 리뷰어님께서 빌더를 사용해보라는 조언을 주셨고, 직접 구현해보았다.


- Builder를 구현한 과정

1. 생성할 객체의 클래스에 생성자를 만든다. 생성할 때 필요한 인자로 이루어진 생성자를 private으로 만든다.
2. 해당 클래스 안에 정적 내부 클래스로 Builder 클래스를 만든다.
3. 빌더 클래스의 필드를 외부 클래스 생성자의 인자들로 한다.
4. 이때 기본값이 필요한 필드는 초기화한다.
5. 빌더 클래스 내부에 각각 필드를 set 하는 메서드를 만든다.
6. 이때 핵심은 this를 return 하여 외부에서 빌더를 사용할 때 체이닝 하며 필드를 초기화하도록 만든다.
7. 외부 클래스에 Builder 클래스를 생성하는 정적 메서드를 만든다. ( builder() )
8. 빌더 클래스에 외부 클래스의 객체를 생성하는 메서드를 만든다. ( build() )

- 예시 코드: 이번 미션에서 빌더를 구현한 [(Section.java)](https://github.com/ggyool/atdd-subway-map/blob/step2/src/main/java/wooteco/subway/line/section/Section.java)
  <br>
  <br>

# 경로 조회
- [LOG](https://github.com/woowacourse/atdd-subway-path/pull/78#issuecomment-866237055)

## Interceptor
- 인터셉터는 주로 컨트롤러에서 요청을 핸들링 하기 전이나 후에 동작하는 객체이다.
- 여러 요청에서 공통 처리가 필요한 경우에 사용하면 좋다.
- 지하철 경로 미션에서 로그인 요청 시 토큰 검증 용도로 사용했다.
- HandlerInterceptor 인터페이스를 구현하여 인터셉터를 정의한다.
- 인터페이스 내부에는 PreHandle, PostHandle, afterCompletion default 메서드가 있다. 필요한 메서드를 오버라이드 한다.
```
preHandle : 핸들러 실행 이전에 동작하는 메서드
postHandle : 핸들러 실행 이후에 동작하는 메서드
afterCompletion: 뷰가 렌더링 된 후에 동작하는 메서드
```

- 지하철 경로 미션에서 인터셉터 사용
1. HandlerInterceptor를 구현하는 interceptor 클래스 생성
2. 필요한 메서드 오버라이드 [(LoginInterceptor.java)](https://github.com/ggyool/atdd-subway-path/blob/step2/src/main/java/wooteco/subway/auth/ui/LoginInterceptor.java#L28)
3. WebMvcConfigurer를 구현하는 Configuration 클래스에 addInterceptors 메서드를 오버라이드 하여 인터셉터 등록 및 경로 지정 [(AuthenticationPrincipalConfig.java)](https://github.com/ggyool/atdd-subway-path/blob/step2/src/main/java/wooteco/subway/config/AuthenticationPrincipalConfig.java#L25)
   <br>

## CORS 이슈 해결
- LoginInterceptor가 수행되는 요청 시 CORS 예외가 발생했다. preHandle 메서드에 Authorization 헤더에서 토큰을 받아 검증하는 코드가 있었고, 해당 헤더가 없거나 유효한 토큰이 아니면 Exception이 발생하는 상황이었다.
- Preflight 요청시에는 Authorization 헤더가 없으므로 예외가 발생하여 CORS 문제가 발생했다.
- CORS 문제를 해결하려면 preflight 요청의 응답에 아래 헤더에 대한 내용들을 채워서 클라이언트로 응답해야 한다.
  "Access-Control-Allow-Origin", "Access-Control-Allow-Methods", "Access-Control-Allow-Headers"
- 처음에는 인터셉터의 preHandle에서 prelight 요청인 경우 대충 아래 흐름으로 직접 header를 설정하고 false를 반환했지만
```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

    if (isPreflighted(request)) {
        response.setHeader( "Access-Control-Allow-Origin", "http://localhost:8081");
        response.setHeader( "Access-Control-Allow-Methods", "GET, POST, PUT, DELETE");
        response.setHeader( "Access-Control-Allow-Headers", "Authorization");
        return false;
    }
    String accessToken = AuthorizationExtractor.extract(request);
    jwtTokenProvider.validateToken(accessToken);
    return true;
}
```
- true를 반환하면 직접 header를 설정하지 않고 알아서 채워지는 것을 발견하고 적용했다.
```java
if (isPreflighted(request)) {
    return true;
}
```
- true를 반환했을 시 prelight 요청에 대한 응답 header 일부
  ![image](https://user-images.githubusercontent.com/12393067/118314776-91d9ea00-b52f-11eb-8fa4-d5e72fcaa03a.png)
- WebConfg.java 정의한 부분 때문에 알아서 잘 된다고 추측하고 있다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**").allowedMethods("*").allowedOriginPatterns("*");
    }
}
- 추후에 CorsUtils.isPreFlightRequest(request) 메서드의 존재를 알게 됐지만 기존 코드와 유사하여 그대로 유지했다.
```

## ArgumentResolver
- Controller 메서드의 인자를 검사하여 조건에 만족하는 경우 값을 가공하여 인자를 전달해 준다.
- Controller 파라미터에 대해 공통 처리가 필요한 경우 사용하면 좋다.
- 지하철 경로 미션에서 로그인 유저의 정보를 가져오는데 사용했다.
- HandlerMethodArgumentResolver 구현하는 ArgumentResolver 생성
- 필요한 메서드를 오버라이드
```
supportsParameter: Controller 메서드의 인자마다 해당 메서드를 실행하여 true이면 resolveArgument를 실행한다. (ex. 인자에 특정 어노테이션이 붙어있으면 true)
resolveArgument: 인자로 전달될 값을 가공한다.
```

- 지하철 경로 미션에서 아규먼트 리졸버 사용
1. 인자에 붙일 어노테이션 생성 [(AuthenticationPrincipal.java)](https://github.com/ggyool/atdd-subway-path/blob/step2/src/main/java/wooteco/subway/auth/domain/AuthenticationPrincipal.java)
2. HandlerMethodArgumentResolver를 구현하는 ArgumentResolver 클래스 생성 [(AuthenticationPrincipalArgumentResolver.java)](https://github.com/ggyool/atdd-subway-path/blob/step2/src/main/java/wooteco/subway/auth/ui/AuthenticationPrincipalArgumentResolver.java)
3. ArgumentResolver 빈 등록 및 WebMvcConfigurer를 구현하는 Configuration 클래스에 addArgumentResolvers 메서드를 오버라이드하여 리졸버 등록  [(AuthenticationPrincipalConfig.java)](https://github.com/ggyool/atdd-subway-path/blob/step2/src/main/java/wooteco/subway/config/AuthenticationPrincipalConfig.java)
4. Controller 메서드의 인자에서 어노테이션 사용 [(MemberController.java)](https://github.com/ggyool/atdd-subway-path/blob/step2/src/main/java/wooteco/subway/member/ui/MemberController.java#L57)
   <br>

## 토큰 기반 로그인, JWT
- 대략적인 흐름
1. 클라이언트에서 서버로 인증에 필요한 정보와 함께 로그인 요청
2. 서버 측에서 정보를 검증하여 토큰 발급
3. 클라이언트에서 토큰을 받음. 이후 요청에 활용
4. 서버 측에서 토큰 인증이 필요한 요청에 대해 토큰 검증 후 응답

- 기존 세션 기반 인증의 단점이라고 할 수 있는 서버 확장이 어렵다는 점을 개선
- 토큰을 저장할 별도의 저장 공간이 필요하지 않다는 점 때문에 확장성이 좋다.
- 모바일 환경에서도 웹과 같은 방식으로 동작한다.

- **JWT 토큰**
  웹 표준(RFC7519)에 등록되어 있는 토큰 형태이다.
- Header.Payload.Signature 의 구조로 되어있다.
- 예시
  eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9```.```eyJpc3MiOiJ2ZWxvcGVydC5jb20iLCJleHAiOiIxNDg1MjcwMDAwMDAwIiwiaHR0cHM6Ly92ZWxvcGVydC5jb20vand0X2NsYWltcy9pc19hZG1pbiI6dHJ1ZSwidXNlcklkIjoiMTEwMjgzNzM3MjcxMDIiLCJ1c2VybmFtZSI6InZlbG9wZXJ0In0```.```WE5fMufM0NDSVGJ8cAolXGkyB5RmYwCto1pQwDIqo2w
```
Header: 암호화 할 알고리즘의 종류와 토큰의 타입을 지정 (Base64 인코딩)
Payload: 토큰에 담을 정보 (Base64 인코딩)
Signature: Header와 Payload를 합친 후 서버의 비밀키로 해싱
```

- 참고 링크
- https://velopert.com/2389
- https://woowacourse.github.io/javable/post/2021-05-22-cookie-session-jwt/
  <br>
  <br>

# 협업 미션
- [LOG](https://github.com/woowacourse/atdd-subway-fare/pull/51#issuecomment-866328747)

## 예외 핸들링
- 레벨2 기간 동안 여러 가지 방식으로 예외 핸들링을 시도해봤고, 이상적이라고 생각하는 흐름을 기록해 두려고 한다.
- 정답이 아닌 개인적인 선호이다. 개발자마다 선호하는 방식이 다른 이슈라고 생각한다.

- 예시 코드
    - [지하철 미션의 exception 패키지](https://github.com/ggyool/atdd-subway-fare/tree/step1/src/main/java/wooteco/subway/exception)
    - [예외 핸들링 Controller Advice](https://github.com/ggyool/atdd-subway-fare/blob/step1/src/main/java/wooteco/subway/SubwayControllerAdvice.java)

- 커스텀 예외의 계층을 만든다.
```java
- SubwayException: 핸들링하길 원하는 가장 상위 예외 
    - exception.web 패키지: Http 기준으로 묶은 예외
        - exception.application 패키지: 앱 전반적으로 사용하는 포괄적인 예외
        - 각 도메인 패키지 내부 예외: 포괄적인 예외로 표현하기 어렵다고 생각하는 예외
```

- 가능하면 포괄적인 예외를 만든다. 예외 메시지에 자세한 내용을 담는다.
```
- ObjectNotFoundException (선호)
- LineNotFoundException, StationNotFoundException, SectionNotFoundException (비선호)
```
<br>

## Mock 테스트, BDDMockito
- Mock 테스트
    - 실제 객체 대신 가짜 객체를 사용하는 테스트

- Mock 객체를 사용하면 좋은 상황
    - 테스트 할 타겟에서 의존하고 있는 객체의 동작 여부와 상관없이 테스트를 만들고 싶은 경우. 즉 작은 단위 테스트를 만들고 싶은 경우
    - 의존성의 depth가 깊어지는 경우
    - 제어할 수 없는 영역 테스트 (ex. Random)

- 장점
    - 스프링의 도움 없이 테스트를 구성하는 경우 속도가 빠르다.
    - 테스트하고 싶은 포인트에 맞춰 상황을 손쉽게 만들 수 있다.

- 단점
    - 테스트를 만들 때 내부 구현을 잘 알아야 한다.

- BDDMockito
    - 자바에서 가장 범용적으로 사용되는 Mocking 프레임워크인 Mockito 프레임워크에 포함되어 있는 클래스로 Mockito 클래스 보다 테스트를 작성하는 사고에 친화적인 API라는 생각.
```
- given some preconditions (Arrange)
- when an action occurs (Act)
- then verify the output (Assert)
```

- 예시 코드: [(LineServiceTest.java)](https://github.com/ggyool/atdd-subway-fare/blob/step1/src/test/java/wooteco/subway/line/application/LineServiceTest.java)

- 참고 링크
  https://www.baeldung.com/bdd-mockito

