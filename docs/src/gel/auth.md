## 内置的 Auth 解决方案 🔐

大多数应用都需某种形式的用户认证, 通常的办法是自己造轮子重复开发, 或选择 Auth0 或者 Clerk 这样的托管服务.

直接使用 Gel 内置、易于集成的 Auth 解决方案也是一个选择:
- 利用内置的身份验证 UI 可以快速搭建应用, 可随时替换为定制的 UI;
- 深度集成: 部署 Gel 意味着您同时部署了 Auth(启用扩展即可), 无需复杂的配置环节;
- access policies for role-based access control
- layer multiple authentication factors onto a single unified User type
- full control over how your authentication data is stored and managed.
- 多种用户验证方式支持(可自行扩展)
- 相对于托管服务按照活跃用户或者验证次数收费, 成本更低;

![](https://www.geldata.com/_images/_blog/b6381c0388a044cf6102402abfb02fe5a204c067-940-2x.webp)

```
using extension auth;
```

Custom Authentication API:
- Login in: POST /authenticate
    - body(json): challenge | email | password | provider | redirect_to | redirect_to_on_signup
    - response: { code }
- Register user: POST /register
    - body(json): challenge | email | password | provider | verify_url
    - response: { code, identity_id }
- Get auth token: GET /token
    - params: code | verifier
    - response: { auth_token, id_token, provider_token }
- Verify Email Link: POST /verify
    - body(json): verification_token | provider
    - response: { code }
- Send Reset-Password Email: POST /send-reset-email
    - body(json): email | provider | reset_url | challenge
    - response: { email_sent }
- Reset Password: POST /reset-password
    - body(json): reset_token | password | provider
    - response: { code }

Magic Link Auth:
- Register user: POST /magic-link/register
    - body(json): challenge | email | provider | callback_url | redirect_on_failure
- Sign In with email: POST /magic-link/email
    - body(json): challenge | email | provider | callback_url
- Get auth token

WebAuthn:
- Get the options for registering: GET /webauthn/register/options
    - params: email
- GET /webauthn/authenticate/options
    - params: email
- POST /webauthn/register
    - body(json): provider | email | credentials | verify_url | user_handle | challenge
- POST /webauthn/authenticate
    - body(json): provider | email | assertion | challenge
    - response: { code }

用户注册成功后与用户进行绑定(1-1 or 1-m):

```
type User {
    required email: str;
    name: str;

    required identity: ext::auth::Identity {
        constraint exclusive;
    };
}
...

# After POST /register response
if ("identity_id" in registerJson) {
  await client.query(`
    with
      identity := <ext::auth::Identity><uuid>$identity_id,
      emailFactor := (
        select ext::auth::EmailFactor filter .identity = identity
      ),
    insert User {
      email := emailFactor.email,
      identity := identity
    };
  `, { identity_id: registerJson.identity_id });
} 
```

> 如何增加身份验证方式? 比如验证码或者扫码登录.

相关阅读:
- OAuth 2 Explained: https://www.youtube.com/watch?v=ZV5yTm4pT8g
- How PKCE Works: https://cloudentity.com/developers/basics/oauth-extensions/authorization-code-with-pkce/
- System Design: https://bytebytego.com/
- NextJS Template: https://github.com/geldata/nextjs-gel-auth-template
- Rust Example: https://github.com/dustypomerleau/audit/blob/main/src/auth.rs