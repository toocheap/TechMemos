# OAuthについて復習

## Oauthに登場する役割（ロール)

- ユーザー(End user)
	- サービスプロバイダーのID/Passwordを持っている
- サービスプロバイダー(SP)
	- ユーザーのID情報を持っている
- コンシューマー(Consumer)
	- サービスプロバイダーのAPIを使用するためのConsumer Key

## 目的

- ユーザーは、コンシューマーに不必要なID情報と権限を与えることなく、サービスプロバイダーにアクセスを許可(移譲)したい。
- サービスプロバイダーはユーザーの同意に基づいて、セキュアな多様なアクセス手段を提供したい
- コンシューマーは不必要にID情報や権限を持ちたくない。

###例

- Twitterクライアント（コンシューマー）をつかって、Twitter（サービスプロバイダー）にアクセスしたいが、クライアントにユーザーパスワードを保存したくない。

========================

## OAuth1.0

### フロー(概略)

0. ConsumerはSPに登録し、リクエストトークン取得のためのConsumer key/Consumer Secet(共通鍵)を取得
1. Consumerは未認可のリクエストークンをSPから取得
	- この際Consume key/Consumer SecretでConsumerをidentifyする
	- ConsumerはSPの許認可ページヘ未認可リクエストトークンとともにUserをリダイレクトする
2. Userはリクエストトークンを認可し、利用に同意する
	- 認可のためのページはSPが提供し、SPはユーザーからの同意を得るとリクエストトークンを認可に変更する
	- SPは Consumerのページへリダイレクトし、認可済み(Authorized)リクエストトークンを返す
3. 認可済みリクエストトークンをアクセストークンと交換する。
	- ConsumerはConsumer key, Consumer Secret, 認可済みリクエストトークンをつかって、SPにアクセストークンを要求する
	- SPは情報を確認した上で、Consumerにアクセストークンと、アクセストークンを署名するためのToken secretを返す
4. アクセストークンをつかってSPのAPIにアクセスする。
	- アクセストークンはToken Secretで署名された上でリクエストされる

#### 用語まとめ

- Consumer key
- Consumer Secret
- 未認可リクエストトークン
- 認可済み(Authorized)リクエストトークン
- アクセストークン
- Token Secret

### 問題

2. Webを前提にしていて、他のサービスへの転用が難しい
3. 複雑な署名と認証フロー
1. スケール次のパフォーマンス
	- SPはリクエストークンをアクセストークンに交換されるまで保持しなければならない

##### 参考

-[YDN:OAuth1.0](http://developer.yahoo.co.jp/other/oauth/flow.html)
-[@IT:Oauth1.0](http://www.atmarkit.co.jp/fsmart/articles/oauth2/01.jpg)

========================

## OAuth2.0

### OAuth1.0からの変化

1. HTTPSを前提として署名を不要に
2. アクセストークンのみでリソース習得が可能に
3. Web以外にも対応

### Oauth2.0の役割(ロール)

- リソースオーナー(Resource Owner)
	- 保護されたリソースへのアクセス許可を行うエンティティ。人の場合はエンドユーザーと呼ばれる
- リソースサーバー(Resource Server)
	- 保護されたリソースをホストし、アクセストークンを用いたリクエストを処理できるサーバー
- クライアント(client)
	- リソースオーナーの許可を得て、リソースサーバーの保護されたリソースにリクエストするアプリケーション
- 認可サーバー(Authorization Server)
	- リソースオーナーの認証とリソースオーナーからの許可取得が成功した場合に、アクセストークンを発行するサーバー

### クライアントのタイプ

クライアントは認可サーバと安全に認証できるかどうかによってコンフィデンシャルとパブリックに分かれる。

機密性が守れるクライアントを **コンフィデンシャル(confidential)クライアント** と呼び、Webアプリケーションのようにリソースオーナーによって公開されたりアクセス可能にならないものを指す。それ以外を **パブリッククライアント** と呼ぶ。ユーザーエージェントとアプリケーション（ブラウザなど）やネイティブアプリケーションはパブリッククライアントの例である。

パブリッククライアントを利用するような場合はリダイレクトエンドポイントのURIは事前に認可サーバに登録されていなければならない。

### プロトコルエンドポイント

認証サーバのエンドポイントは以下の2つ。

#### 認可エンドポイント

ユーザーエージェント経由でリソースオーナーから認可を得るために使用される

#### トークンエンドポイント

認可グラントとアクセストークンを交換するためにクライアントが利用する。クライアント認証が行われる。

クライアントのエンドポイントは以下のひとつ。

#### リダイレクトエンドポイント

リソースオーナーのユーザーエージェント経由で、認可クレデンシャルを含むレスポンスをクライアント側に渡すために、認証サーバによって使用される。

### OAuth2.0の基本フロー

	  +--------+                               +---------------+
	  |        |--(A)- Authorization Request ->|   Resource    |
	  |        |                               |     Owner     |
	  |        |<-(B)-- Authorization Grant ---|               |
	  |        |                               +---------------+
	  |        |
	  |        |                               +---------------+
	  |        |--(C)-- Authorization Grant -->| Authorization |
	  | Client |                               |     Server    |
	  |        |<-(D)----- Access Token -------|               |
	  |        |                               +---------------+
	  |        |
	  |        |                               +---------------+
	  |        |--(E)----- Access Token ------>|    Resource   |
	  |        |                               |     Server    |
	  |        |<-(F)--- Protected Resource ---|               |
	  +--------+                               +---------------+

1. クライアント<==>リソースオーナーのやり取りで、クライアントが**認可グラント**を取得。
2. クライアント<==>認証サーバーのやりとりで、クライアントが**アクセストークン**を取得。
3. クライアント<==>リソースサーバーのやりとりで、クライアントが**保護されたリソース**へアクセス。

### 認可グラント(Authorization Grant)の取得フロー

認可グラントには4つのタイプがあり、それぞれ取得フローが異なる。

#### 認証コード(Authorization code)

- 認証サーバがクライアントとリソースオーナーの仲介となって発行するコード。
- ブラウザ→認証サーバにリダイレクトされで許認可→クライアントのページに再リダイレクト、の流れはこれ。
- クライアントがリソースオーナーのクレデンシャルを取得せずに、リソースオーナーはリソースへの許可をクライアントに与えることができる。
- クライアントを認証する機会がある、クライアントのユーザーエージェントを介さずに認証サーバからクライアントに直接認証コードを送れる、などセキュリティ面でメリットが有る。

	  +----------+
	  | Resource |
	  |   Owner  |
	  |          |
	  +----------+
	       ^
	       |
	      (B)
	  +----|-----+          Client Identifier      +---------------+
	  |         -+----(A)-- & Redirection URI ---->|               |
	  |  User-   |                                 | Authorization |
	  |  Agent  -+----(B)-- User authenticates --->|     Server    |
	  |          |                                 |               |
	  |         -+----(C)-- Authorization Code ---<|               |
	  +-|----|---+                                 +---------------+
	    |    |                                         ^      v
	   (A)  (C)                                        |      |
	    |    |                                         |      |
	    ^    v                                         |      |
	  +---------+                                      |      |
	  |         |>---(D)-- Authorization Code ---------'      |
	  |  Client |          & Redirection URI                  |
	  |         |                                             |
	  |         |<---(E)----- Access Token -------------------'
	  +---------+       (w/ Optional Refresh Token)

##### クライアントが認証サーバへ送る認可コード取得リクエストの例

	  GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
	      &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
	  Host: server.example.com

- responce_type=code (必須)
	- "認証コード"を用いることを宣言
- client_id=xxxx
	- 別にリソースサーバや認証サーバから発行されたクライアント識別子を指定（必須)
- state=xyz (推奨)
	- リクエストとコールバックの間での状態を維持するランダムな値。CSRF対策にも有効。
- redirect_uri=https://....(任意)
	- 認証サーバの処理後のリダイレクト先
- scope(任意)
	- アクセストークンに与える権限の範囲を指定する文字列。認証サーバによって定義される。

##### 認証サーバがクライアントに送る認可コードレスポンスの例

	HTTP/1.1 302 Found
	  Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA
	            &state=xyz

- code=xxxx (必須)
	- クライアント識別子とリダイレクトURIに紐づく認可コード。短時間でexpireし推奨は10分。
- state=xyx (リクエストにあれば必須)
	- クライアントから受け取ったstateをそのまま返す。

##### 認証サーバからのエラーレスポンス

	 HTTP/1.1 302 Found
	  Location: https://client.example.com/cb?error=access_denied&state=xyz

- error
	- エラー内容は [Section 4.2.1.2](http://openid-foundation-japan.github.com/rfc6749.ja.html#code-authz-resp)に記述。

##### アクセストークンのリクエスト例

	POST /token HTTP/1.1
	  Host: server.example.com
	  Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
	  Content-Type: application/x-www-form-urlencoded

	  grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
	  &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb

- grant_type=authorization_code (必須)
	- グラントのタイプを認証コードと宣言
- code=xxxx (必須)
	- 取得した認証コードを指定
- redirect_uri=https://...
	- アクセストークン取得後のリダイレクト先のURIを指定

リクエストを受けた際に認可サーバはクライアントの認証を行わなければならないため、クライアントはクレデンシャルを送らなければならない。送付方法はBASIC認証、client_idパラメータなどがある。

##### アクセストークンの成功レスポンス例

	HTTP/1.1 200 OK
	  Content-Type: application/json;charset=UTF-8
	  Cache-Control: no-store
	  Pragma: no-cache

	  {
	    "access_token":"2YotnFZFEjr1zCsicMWpAA",
	    "token_type":"example",
	    "expires_in":3600,
	    "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
	    "example_parameter":"example_value"
	  }

#### Implicit Grant(暗黙のグラント)

- 認可コードでは認可リクエストと、アクセストークンリクエストの2回のリクエストが発行されるが、暗黙のグラントでは認可リクエストの結果としてアクセストークンを取得する。
- クライアント認証は行わず、リダイレクトURIのクライアントによる事前に登録とユーザーの介在によってセキュリティを担保する。
- リソースオーナーはもとより、クライアントもリソースオーナーのユーザーエージェント経由で認証サーバとやり取りを行う。
- 認証コードではサポートされる、アクセストークン更新を促すリフレッシュトークンはサポートされない。
- 簡便で速度にメリットがあるが、セキュリティ面ではやや劣る。

	  +----------+
	  | Resource |
	  |  Owner   |
	  |          |
	  +----------+
	       ^
	       |
	      (B)
	  +----|-----+          Client Identifier     +---------------+
	  |         -+----(A)-- & Redirection URI --->|               |
	  |  User-   |                                | Authorization |
	  |  Agent  -|----(B)-- User authenticates -->|     Server    |
	  |          |                                |               |
	  |          |<---(C)--- Redirection URI ----<|               |
	  |          |          with Access Token     +---------------+
	  |          |            in Fragment
	  |          |                                +---------------+
	  |          |----(D)--- Redirection URI ---->|   Web-Hosted  |
	  |          |          without Fragment      |     Client    |
	  |          |                                |    Resource   |
	  |     (F)  |<---(E)------- Script ---------<|               |
	  |          |                                +---------------+
	  +-|--------+
	    |    |
	   (A)  (G) Access Token
	    |    |
	    ^    v
	  +---------+
	  |         |
	  |  Client |
	  |         |
	  +---------+

##### 認可リクエストの例

	 GET /authorize?response_type=token&client_id=s6BhdRkqt3&state=xyz
	      &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
	  Host: server.example.com

- responce_type=token (必須)
	- Implicit Grantを使用することを宣言する
- client_id=xxxx (必須)
- state
- redirect_uri=https://xxxx (任意)
	- 事前登録されたredirect_uriを指定する。このURIは認証サーバが事前登録されているURIと照合して一致するかが検証される(MUST)。

##### アクセストークンレスポンスの例

	 HTTP/1.1 302 Found
	  Location: http://example.com/cb#access_token=2YotnFZFEjr1zCsicMWpAA
	            &state=xyz&token_type=example&expires_in=3600

- access_token=xxx (必須)
- state
- token_type=...(必須)
- expires_in=3600 (推奨)
	- アクセストークンが有効な期間。単位は秒。

#### Resource Owner Password Credential Grant

- リソースオーナーとクライアントが信頼関係にある場合に、オーナーのパスワード認証を元にアクセストークンを取得する。

	  +----------+
	  | Resource |
	  |  Owner   |
	  |          |
	  +----------+
	       v
	       |    Resource Owner
	      (A) Password Credentials
	       |
	       v
	  +---------+                                  +---------------+
	  |         |>--(B)---- Resource Owner ------->|               |
	  |         |         Password Credentials     | Authorization |
	  | Client  |                                  |     Server    |
	  |         |<--(C)---- Access Token ---------<|               |
	  |         |    (w/ Optional Refresh Token)   |               |
	  +---------+                                  +---------------+

#### Client Credential Grant

- クライアント自身が保護されたリソースを保持している場合にのみ、使用される

	  +---------+                                  +---------------+
	  |         |                                  |               |
	  |         |>--(A)- Client Authentication --->| Authorization |
	  | Client  |                                  |     Server    |
	  |         |<--(B)---- Access Token ---------<|               |
	  |         |                                  |               |
	  +---------+                                  +---------------+


##### 参考

- [RFC6749 OAuth2.0 Authorization Framework](http://openid-foundation-japan.github.com/rfc6749.ja.html)



