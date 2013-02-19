# Node.js Passportモジュールによる認証

## Express 3.0での基本的な設定

### モジュールのインストール

Passportは本体と、各サービスごとの認証モジュール(Strategy)に分けられる。
必要な認証モジュールを使用することで、様々なサービスに対応できる。

    $ npm install Passport

例) Twitterの場合

    $ npm install passport-twitter

## Expressへの追加 (Tumblrを例に)

TumblrはOAuth1.0ベースの認証を行う。

### Connectモジュールの追加
### Sessionのシリアル化・でシリアル化
### Passport.useによる認証モジュールの登録
### routeハンドラに対する認証リダイレクト処理の登録
### カスタムCallback

### req.user
### flashメッセージ


### failed to find request token in session がでる。

callback URLがhttp://127.0.0.1/xxxなのに、http://localhost/xxxでアクセスしている場合など、OAuthを開始したホストがcallback endpointにアクセスしてきた場合に発生する。
発生箇所のソースのコメントに書いてあった。

    // Bail if the session does not contain the request token and corresponding
    // secret.  If this happens, it is most likely caused by initiating OAuth
    // from a different host than that of the callback endpoint (for example:
    // initiating from 127.0.0.1 but handling callbacks at localhost).
    if (!req.session[self._key]) { return self.error(new Error('failed to find request token in session')); }

callback URLをlocalhostに書き換え。たしかTwitterだとIPのほうがいいような記述があってIPで書いていた気がする（うろ覚え）

