---
layout: post
title: Enabling Keycloak login via customer number
categories: KeyCloak
---

### What is Keycloak?

Keycloak dựa trên các tiêu chuẩn OAuth 2.0 và OpenID Connect, đồng thời cho phép tích hợp lớp xác thực và ủy quyền trung tâm trong các ứng dụng. Keycloak có thể được sử dụng để quản lý tài khoản, xác thực danh tính của họ thông qua nhiều nhà cung cấp danh tính bên ngoài (IdP) khác nhau và cung cấp dịch vụ đăng nhập một lần (SSO) cho nhiều ứng dụng. Keycloak hỗ trợ nhiều phương thức xác thực khác nhau như tên người dùng/mật khẩu, Đăng nhập mạng xã hội thông qua các nền tảng như Google, Facebook và GitHub, SAML 2.0, v.v. Keycloak cũng cung cấp khả năng xác định và sử dụng các thuộc tính tùy chỉnh để lưu trữ thông tin bổ sung về tài khoản.

Một trong những điểm mạnh nhất của Keycloak là tính linh hoạt và khả năng mở rộng. Và đây chính xác là nơi bài đăng blog này xuất hiện. Theo mặc định, Keycloak chỉ cho phép đăng nhập bằng tên người dùng và/hoặc địa chỉ e-mail. Nhưng điều gì sẽ xảy ra nếu điều này vẫn chưa đủ và một tính năng độc đáo khác sẽ được sử dụng làm thông tin đăng nhập, chẳng hạn như số điện thoại hoặc số khách hàng?

### Fundamentals (Nguyên tắc cơ bản)

Keycloak cung cấp 'luồng trình duyệt' (**browser flow**) tương ứng nếu các tài khoản đăng nhập vào ứng dụng được Keycloak bảo mật thông qua trình duyệt. Luồng này trình bày 'UsernamePasswordForm' trong đó dữ liệu đăng nhập - trong trường hợp mặc định: ser name/e-mail and password – are entered and, if successful,   tài khoản được chuyển tiếp đến ứng dụng bảo mật như đã được xác thực.

Now if we want to ensure that accounts can also log in with additional unique information such as a customer number, because the project has this as a requirement due to a legacy data migration, for example, we have to add our specific logic to this ‘UsernamePasswordForm’.


### Creating test users with customer number

For the example, I first created a user via the Keycloak Admin interface and assigned a customer number to it as an attribute:

![_config.yml]({{ site.baseurl }}/images/cus-p-01.png)

- **View of the user attributes**

With this customer number we can log in after the adjustments and extensions described below. Trong một dự án thực, mã số khách hàng này sẽ được tự động liên kết với tài khoản trong quá trình đăng ký, bằng Keycloak hoặc bằng hệ thống bên ngoài.

### Extension of the ‘UsernamePasswordForm’ (mở rộng )

Biểu mẫu này được sử dụng trong quy trình xác thực trình duyệt để đánh giá các trường đăng nhập cho tên người dùng/địa chỉ email.

![_config.yml]({{ site.baseurl }}/images/cus-p-02.png)

- **View of the configured browser authentication flow**

Đây là nơi chúng tôi muốn bắt đầu và đảm bảo rằng số khách hàng hợp lệ kết hợp với mật khẩu chính xác cũng dẫn đến nỗ lực đăng nhập thành công. . Keycloak cung cấp phương thức ‘UsernamePasswordForm’ cho mục đích này.

- We can extend this by overwriting the ‘validateUserAndPassword’ method from the abstract class ‘AbstractUsernameFormAuthenticatot’.
- Điều này cho chúng tôi cơ hội can thiệp vào việc xác thực các giá trị được nhập trong màn hình đăng nhập
- Điều này cho phép chúng tôi kiểm tra xem tên đăng nhập có phải là mã số khách hàng hiện tại hay không.
- Để làm như vậy, chúng tôi tìm kiếm khu vực hiện tại cho tất cả các tài khoản đã lưu giá trị này làm thuộc tính trong tài khoản của họ:

```java
	List<UserModel> users = context.getSession().users()
		        .searchForUserByUserAttributeStream(
				context.getRealm(), "customer_number", username)
		        .toList();
```

- Dự kiến, một số tài khoản có số khách hàng có thể được tìm thấy nếu điều này không được ngăn chặn trước đó một cách chính xác. Trường hợp này phải được xử lý thích đáng. Nếu không tìm thấy tài khoản nào, bạn có thể kiểm tra xem giá trị đã nhập có phải là tên người dùng hay không:

```java
	user = context.getSession().users()
			.getUserByUsername(context.getRealm(), username);
```

- Nếu không tìm thấy tài khoản nào ở đây thì bước cuối cùng là kiểm tra xem giá trị có phải là địa chỉ email hay không.
- Nhưng hãy cẩn thận, chỉ có thể tìm kiếm e-mail nếu chúng tôi đã kích hoạt cài đặt tương ứng trong khu vực và do đó được phép đăng nhập bằng e-mail:

![_config.yml]({{ site.baseurl }}/images/cus-p-03.png)

- **E-mail settings at realm level in the ‘Login’ tab**

```java
	if (user == null && context.getRealm().isLoginWithEmailAllowed()) {
		    user = context.getSession().users()
				.getUserByEmail(context.getRealm(), username);
		}
```

- Nếu chúng tôi vẫn chưa tìm thấy tài khoản, chúng tôi có thể chắc chắn rằng giá trị đã nhập không tồn tại dưới dạng số khách hàng, tên người dùng hoặc e-mail
- Trong trường hợp này, chúng tôi có thể hủy đăng ký và hiển thị thông báo lỗi tương ứng trên trang đăng ký:

```java
	setInvalidUserError(context);
		return false;
		private void setInvalidUserError(AuthenticationFlowContext context) {
		    Response challengeResponse = challenge(context, Messages.INVALID_USER);
		    context.failureChallenge(
			AuthenticationFlowError.INVALID_USER, challengeResponse);
		}
```
- Khi chúng tôi đã tìm thấy một tài khoản, chúng ta vẫn cần đảm bảo rằng mật khẩu được xác thực tương ứng.
- Điều này thường được thực hiện bằng phương thức ghi đè của siêu lớp.
- Nếu chúng tôi không làm điều này ở đây, người dùng sẽ đăng nhập thành công, ngay cả khi họ nhập mật khẩu không hợp lệ cho tài khoản.

```java
	if (user == null || !validatePassword(context, user, inputData, false)) {
		    setInvalidUserError(context);
		    return false;
		}
```

- Điều quan trọng là chúng tôi sử dụng cùng một thông báo lỗi cho cả mật khẩu không chính xác và tên người dùng không chính xác
- Điều này ngăn kẻ tấn công sử dụng thông báo lỗi để tìm hiểu xem liệu hắn có tìm thấy tài khoản hợp lệ hay không.
- Nếu mật khẩu cũng hợp lệ, chúng ta có thể cho rằng người dùng đã đăng nhập thành công:

```java
	context.setUser(user);
		return true;
```

### Configuration of the authentication flow in Keycloakv

- Tuy nhiên, trước khi những thay đổi này có hiệu lực trong Keycloak, cần phải cung cấp lớp Factory cho các tùy chỉnh của chúng tôi trong tệp MANIFEST.
- Để làm như vậy, chúng tôi tạo tệp sau và thêm nhà máy của chúng tôi vào đó:

```java
de.adesso.CustomerNumberAuthenticatorFactory
```

- Chúng ta cũng cần điều chỉnh cấu hình luồng trình duyệt. Để làm như vậy, chúng tôi sao chép luồng trình duyệt mặc định và điều chỉnh bản sao như sau để sử dụng biểu mẫu mới của chúng tôi:

![_config.yml]({{ site.baseurl }}/images/cus-p-04.png)

- **Setting the new customised browser authentication flow to use customer number login**

- Bản sao phải được đặt làm tiêu chuẩn mới cho luồng trình duyệt.
- Mọi thứ hiện đã sẵn sàng để các tài khoản cũng có thể đăng nhập bằng số khách hàng đã lưu trữ.

