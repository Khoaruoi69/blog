---
layout: post
title: Action Token in Keycloak
categories: SPI
---

### Action Token in Keycloak

- *link: https://www.janua.fr/action-token-in-keycloak/*

- *https://blog.please-open.it/action-token/*

- Mã thông báo hành động (Action Token) trong Keycloak có thể rất hữu ích nhưng cũng khá khó thực hiện. Bạn có thể tìm thấy một bài viết từ đối tác của chúng tôi, Please Open It, về việc triển khai và các trường hợp sử dụng của nó dưới đây.

## What is an action token ?

- Action tokens là một loại mã thông báo đặc biệt nhằm cho phép người dùng chưa xác thực thực hiện một hành động cụ thể và hạn chế. Sử dụng phổ biến nhất của mã thông báo hành động là để xác nhận email hoặc đặt lại thông tin đăng nhập.

- Tuy nhiên, việc sử dụng của nó không bị hạn chế trong một tập hợp hành động cụ thể; trong Keycloak, bạn có thể triển khai mã thông báo hành động tùy chỉnh. Điều này cho phép bạn thực hiện bất cứ điều gì bạn muốn và phù hợp với quy trình của mã thông báo hành động.

- Action tokens  là một phiên bản đặc biệt của JSON Web Token (JWT) cho phép người nắm giữ thực hiện một số hành động, ví dụ như đặt lại mật khẩu hoặc xác thực địa chỉ email. Chúng thường được gửi đến người dùng dưới dạng liên kết chỉ đến một điểm cuối xử lý mã thông báo hành động cho một realm cụ thể.

- Keycloak cung cấp bốn loại mã thông báo cơ bản cho phép người nắm gi

    + Đặt lại thông tin đăng nhập
    + Xác nhận địa chỉ email
    + Thực hiện các hành động yêu cầu
    + Xác nhận liên kết tài khoản với tài khoản trong nhà cung cấp danh tính bên ngoài

- Ngoài ra, có thể triển khai bất kỳ chức năng nào khởi tạo hoặc sửa đổi phiên đăng nhập sử dụng SPI mã thông báo hành động… 

## Anatomy of an action token ( Cấu trúc của mã thông báo hành động)

```java
{
  "exp": 1608657051,
  "iat": 1608656751,
  "jti": "200a261f-05d2-487e-8676-a9a72f22c2bd",
  "iss": "http://localhost:8080/auth/realms/test",
  "aud": "http://localhost:8080/auth/realms/test",
  "sub": "213a4e35-fbc8-41d2-b28c-128a5ad90a3f",
  "typ": "external-app-notification",
  "nonce": "200a261f-05d2-487e-8676-a9a72f22c2bd",
  "app-id": "123",
  "asid": "account"
}
```
- Trường « typ » sẽ xác định cách xử lý mã thông báo.
- Trường « app-id » là trường tùy chỉnh, chúng tôi sẽ sử dụng trường này trong ví dụ sau để xác định phạm vi nào sẽ được sử dụng để tạo mã thông báo truy cập.


## Trao đổi mã thông báo giả danh trực tiếp (Direct Naked Impersonation)

- Trước khi đi sâu vào chủ đề chính của bài viết này, chúng ta cần có một cái nhìn nhanh về cơ chế giả danh trực tiếp. Cơ chế này sẽ được sử dụng để trao đổi mã thông báo hành động của chúng tôi với một mã thông báo truy cập tiêu chuẩn nhưng hạn chế, nhằm cho phép truy cập vào một bài viết cụ thể trong bản tin

- Giả danh trực tiếp (Direct naked impersonation) là một cách để back-end của bạn giả danh bất kỳ người dùng nào chỉ dựa vào thông tin xác thực của client.

- Lưu ý: Các client được phép thực hiện giả danh trực tiếp rất nhạy cảm vì chúng có thể giả danh bất kỳ người dùng nào trong realm của bạn. Do đó, client_id và client_secret phải được lưu trữ đúng cách trong kho lưu trữ an toàn và không bao giờ nên được sử dụng ngoài các ứng dụng back-end của bạn trong môi trường sản xuất.

- Dưới đây là tài liệu về cách hoạt động trong Keycloak: **https://www.keycloak.org/docs/latest/securing_apps/#direct-naked-impersonation**

- Vì vậy, giả danh trực tiếp chỉ là một luồng xác thực cho phép back-end của bạn lấy mã thông báo truy cập của bất kỳ người dùng nào trong realm của bạn. Do đây không phải là một cách xác thực tiêu chuẩn và có phần kém an toàn hơn, chúng tôi khuyến nghị mạnh mẽ việc tạo ra mã thông báo truy cập có giới hạn cao thông qua luồng này. Trong trường hợp của chúng tôi, chúng tôi sẽ sử dụng nó để tạo mã thông báo truy cập giới hạn cho bài viết được tham chiếu trong bản tin.

- Ví dụ dưới đây cho thấy cách lấy một mã thông báo hạn chế như vậy trong trình xử lý mã thông báo hành động, điều này sẽ được trình bày sau trong bài viết này:


```java
        Client client = ClientBuilder.newBuilder().build();
        WebTarget target = client.target("http://keycloak:8080/auth/realms/test/protocol/openid-connect/token");
        Form form = new Form()
                .param("grant_type", "urn:ietf:params:oauth:grant-type:token-exchange")
                .param("client_id", "test")
                .param("client_secret", "21b12e78-6cde-4182-a609-77f54b136ba4")
                .param("requested_subject", token.getUserId())
                .param("scope", "newsletterscope" + token.getApplicationId())
                .param("audience", "test");

        Response response = target.request().post(Entity.form(form));
```

## Cách tùy chỉnh với Keycloak: ví dụ cho bản tin

- Trong ví dụ này, chúng ta sẽ xem cách triển khai mã thông báo hành động tùy chỉnh để nhúng nó vào bản tin và sau đó xử lý nó để lấy mã thông báo truy cập cho phép chúng ta tải phiên bản trực tuyến của một bài viết

- Mục đích của việc tạo mã thông báo truy cập là để được cấp quyền đọc bài viết trực tuyến mà không cần quản lý mã thông báo hành động trong dịch vụ bài viết. Do đó, dịch vụ bài viết có thể là một nginx đơn giản với plugin OIDC mà không cần mã cụ thể nào.

- Tất cả mã sau đây được chứa trong một plugin Keycloak.

## Triển khai mã thông báo hành động tùy chỉnh

- Bước đầu tiên là mở rộng lớp **DefaultActionToken**. Mục đích là để thêm một số trường tùy chỉnh cần thiết để xử lý mã thông báo.

- Ở đây, chúng ta sẽ đơn giản sử dụng trường **applicationId** như một phạm vi để nhận mã thông báo truy cập hạn chế.

```java
public class ExternalApplicationNotificationActionToken extends DefaultActionToken {

    public static final String TOKEN_TYPE = "external-app-notification";

    private static final String JSON_FIELD_APP_ID = "app-id";

    @JsonProperty(value = JSON_FIELD_APP_ID)
    private String applicationId;

    public ExternalApplicationNotificationActionToken(String userId, int absoluteExpirationInSecs, String authenticationSessionId, String applicationId) {
        super(userId, TOKEN_TYPE, absoluteExpirationInSecs, null, authenticationSessionId);
        this.applicationId = applicationId;
    }

    private ExternalApplicationNotificationActionToken() {
    }

    public String getApplicationId() {
        return applicationId;
    }

    public void setApplicationId(String applicationId) {
        this.applicationId = applicationId;
    }
}

```

## Cách phát hành mã thông báo hành động ( How to emit action token)

- Mã thông báo hành động thường được tạo ra trong SPI xác thực (authenticator SPI) cho các mục đích sử dụng phổ biến như xác nhận email.
- Trong trường hợp của chúng tôi, chúng tôi muốn cho phép một dịch vụ bên ngoài lấy mã thông báo truy cập và sau đó bao gồm chúng trong một bản tin
- Cách đơn giản nhất để làm điều đó là tạo một điểm cuối mới thông qua RealmResource Provider.

```java
public class ActionTokenApi implements RealmResourceProvider {

    private KeycloakSession session;

    @Context
    UriInfo uriInfo;

    public ActionTokenApi(KeycloakSession session) {
        this.session = session;
    }

    @Override
    public Object getResource() {
        return this;
    }

    @Override
    public void close() {
        // Nothing to close
    }

    @POST
    @NoCache
    @Produces(MediaType.APPLICATION_JSON)
    @Path("generate-token")
    public Output getActionToken(Input input, @Context UriInfo uriInfo) {

        KeycloakContext context = session.getContext();
        // Generate action token
        String applicationId = input.getApplicationId();

        int validityInSecs = context.getRealm().getActionTokenGeneratedByUserLifespan();
        int absoluteExpirationInSecs = Time.currentTime() + validityInSecs;
        final AuthenticationSessionModel authSession = context.getAuthenticationSession();
        final String clientId = "account";

        // Create a token used to return back to the current authentication flow
        String token = new ExternalApplicationNotificationActionToken(
          input.getUserId(),
          absoluteExpirationInSecs,
          clientId,
          applicationId
        ).serialize(
          session,
          context.getRealm(),
          uriInfo
        );

        System.out.println(token);
        System.out.println("Ok" + input.getUserId());
        return new Output(token);
    }
}
```

- Sau khi tài nguyên khu vực được triển khai, mã thông báo hành động có thể được truy xuất theo cách này:

```java

curl --location --request POST 'http://localhost:8080/auth/realms/test/newsletter/generate-token' \
--header 'Content-Type: application/json' \
--data-raw '{
    "userId": "213a4e35-fbc8-41d2-b28c-128a5ad90a3f",
    "applicationId": "123"
}'

```

## Handle the action token (Xử lý mã thông báo hành động)

- Ví dụ về Curl sau đây cho thấy cách gọi mã thông báo hành động điểm cuối keycloak. Điểm cuối này sẽ xác minh mã thông báo hành động sau đó gọi trình xử lý tương ứng.

```java

curl --location --request GET 'http://localhost:8080/auth/realms/test/login-actions/action-token?key=eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJiMWIzM2U0My0yZjRjLTQ4ZTItYTBmZS04YzQ3YmFmMDZmZWYifQ.eyJleHAiOjE2MDg2NTcwNTEsImlhdCI6MTYwODY1Njc1MSwianRpIjoiMjAwYTI2MWYtMDVkMi00ODdlLTg2NzYtYTlhNzJmMjJjMmJkIiwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo4MDgwL2F1dGgvcmVhbG1zL3Rlc3QiLCJhdWQiOiJodHRwOi8vbG9jYWxob3N0OjgwODAvYXV0aC9yZWFsbXMvdGVzdCIsInN1YiI6IjIxM2E0ZTM1LWZiYzgtNDFkMi1iMjhjLTEyOGE1YWQ5MGEzZiIsInR5cCI6ImV4dGVybmFsLWFwcC1ub3RpZmljYXRpb24iLCJub25jZSI6IjIwMGEyNjFmLTA1ZDItNDg3ZS04Njc2LWE5YTcyZjIyYzJiZCIsImFwcC1pZCI6IjEyMyIsImFzaWQiOiJhY2NvdW50IiwiYXNpZCI6ImFjY291bnQifQ.ikJYcJxYsx2Kk-yLRCVQHQHf3uVcJHmcPzVZyQw1dno'

```
- Đây là một triển khai xử lý.

- Chúng ta có thể thấy trong hàm tạo các tham số sau được truyền cho hàm tạo super() :

```java
          ExternalApplicationNotificationActionToken.TOKEN_TYPE,
          ExternalApplicationNotificationActionToken.class,
```

- Đó là cách keycloak xác định trình xử lý phù hợp tùy thuộc vào mã thông báo được cung cấp cho điểm cuối.

Nói về phương thức handToken bị ghi đè. Trong trình xử lý này, chúng tôi mạo danh người dùng được yêu cầu trong phạm vi giới hạn thông qua luồng mạo danh trực tiếp (như đã giải thích trước đó).

```java
public class ExternalApplicationNotificationActionTokenHandler extends AbstractActionTokenHander<ExternalApplicationNotificationActionToken> {

    public static final String INITIATED_BY_ACTION_TOKEN_EXT_APP = "INITIATED_BY_ACTION_TOKEN_EXT_APP";

    public ExternalApplicationNotificationActionTokenHandler() {
        super(
          ExternalApplicationNotificationActionToken.TOKEN_TYPE,
          ExternalApplicationNotificationActionToken.class,
          Messages.INVALID_REQUEST,
          EventType.EXECUTE_ACTION_TOKEN,
          Errors.INVALID_REQUEST
        );
    }

    @Override
    public Response handleToken(ExternalApplicationNotificationActionToken token, ActionTokenContext<ExternalApplicationNotificationActionToken> tokenContext) {

        System.out.println("handleToken");
        tokenContext.getAuthenticationSession().setAuthNote(INITIATED_BY_ACTION_TOKEN_EXT_APP, "true");
        tokenContext.getAuthenticationSession().getAuthenticatedUser();

        Client client = ClientBuilder.newBuilder().build();
        WebTarget target = client.target("http://keycloak:8080/auth/realms/test/protocol/openid-connect/token");
        Form form = new Form()
                .param("grant_type", "urn:ietf:params:oauth:grant-type:token-exchange")
                .param("client_id", "test")
                .param("client_secret", "21b12e78-6cde-4182-a609-77f54b136ba4")
                .param("requested_subject", token.getUserId())
                .param("scope", "newsletterscope" + token.getApplicationId())
                .param("audience", "test");

        Response response = target.request().post(Entity.form(form));
        return response;
    }
}
```